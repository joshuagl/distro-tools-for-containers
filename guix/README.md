# Guix: https://guix.gnu.org/

Guix is a package manager and a linux operating system focused on maintaining sources. What's interesting about them is that they work towards reducing the size of the bootstrapping set of binaries required to build an OS from scratch.

Blog article: https://guix.gnu.org/blog/2019/guix-reduces-bootstrap-seed-by-50/
Update: https://guix.gnu.org/en/blog/2020/guix-further-reduces-bootstrap-seed-to-25/

## Environment
Fedora with 4 cpu cores

## Dependencies
No dependencies. We can't build from source on a non-guix system. So we installed their binary tarball.

## Installing guix
I followed a number of places for installation instructions:

- https://guix.gnu.org/manual/en/html_node/Binary-Installation.html
- https://elephly.net/posts/2015-06-21-getting-started-with-guix.html

Their one-stop install script did not work. You don't have to verify the gpg signature of the downloaded tarball but they highly recommend it.

1. Download x86_64 tarball from their download page: https://guix.gnu.org/download/
2. Optional: download their x86_64 signature from the same location
3. Optional: Import signer's public key `$ wget https://sv.gnu.org/people/viewgpg.php?user_id=15145 -qO - | gpg --import -`
4. Optional: verify the gpg signature `gpg --verify guix-binary-1.0.1.system.tar.xz.sig`

As of this writing the signature belongs to:
```
$ gpg --verify guix-binary-1.0.1.x86_64-linux.tar.xz.sig
gpg: assuming signed data in 'guix-binary-1.0.1.x86_64-linux.tar.xz'
gpg: Signature made Sun 19 May 2019 01:47:35 PM PDT
gpg:                using RSA key 3CE464558A84FDC69DB40CFB090B11993D9AEBB5
gpg: Good signature from "Ludovic Courtès <ludo@gnu.org>" [unknown]
gpg:                 aka "Ludovic Courtès <ludo@chbouib.org>" [unknown]
gpg:                 aka "Ludovic Courtès (Inria) <ludovic.courtes@inria.fr>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 3CE4 6455 8A84 FDC6 9DB4  0CFB 090B 1199 3D9A EBB5
```
Following the binary installation steps installs guix as root user and allows access to guix for all users.

It takes a very very long time to get a system ready for building.


## Go image

`$ guix pack --format=docker -S /bin=bin go`
`$ docker load < /gnu/store/<HASH>-docker-pack.tar.gz`
`$ docker run -it go:latest /bin/go version`
`go version go1.12.7 linux/amd64`

213M tarball


## Some notes of seemingly relevant commands/features:

* [`guix import`](https://guix.gnu.org/manual/en/html_node/Invoking-guix-import.html) - a mechanism to automatically create Guix package metadata. Can import from PyPI (`guix import pypi -r tern`), Ruby Gems, CPAN, CRAN, Nix, etc as well as taking generic info about a package in JSON format.
    * __Note__: when running Guix on a foreign distro `guix` needs to know how
      to locate certificates, see [X.509 Certificates](https://guix.gnu.org/manual/en/html_node/X_002e509-Certificates.html#X_002e509-Certificates)
    * The output of `guix import` should be piped into a file that can then be
      built with `guix build -f <output-file.scm>`
    * __Note__: this file can't be parsed without editing to add some
      `use-modules` statements
    * Example usage:
      `guix import json coredns.json > coredns.scm`
      <edit _coredns.scm_ to add `use-modules` lines>
      `guix build -f coredns.scm`
* [`guix refresh`](https://guix.gnu.org/manual/en/html_node/Invoking-guix-refresh.html) - attempts to determine whether any packages are out of date and automatically update them.
* [`guix system docker-image container.scm`](https://guix.gnu.org/manual/en/html_node/Invoking-guix-system.html) will take a system definition as a Scheme file and create a Docker image matching that definiton
* [`guix pack --format=docker -S /opt/gnu/bin=bin mysql`](https://guix.gnu.org/manual/en/html_node/Invoking-guix-pack.html) will create a Docker container image containing the specified package(s) and its dependencies only
    * `guix pack` will create a tarball containing files associated with the installed package and its dependencies. The directories have the package's checksum, name and version.
    * The `docker` format will create a tarball that docker will accept with `docker load` but it will not run because the required `/sbin/init` inode is not present.
    * Still haven't figured out how to get the corresponding source.
    * Relevant command: `guix pack --format=docker -S /opt/gnu/bin=bin go`, `docker load - guix-go < guix.tar.gz`, `docker run -t guix-go /opt/gnu/bin/go --version`
    * The resulting docker image from `docker load` is around 700MB large, which is larger than the debian based golang container image.
	* `guix pack` now supports [creating deb packages](https://git.savannah.gnu.org/cgit/guix.git/commit/?id=82daab42811a2e3c7684ebdf12af75ff0fa67b99)
	    * Use it like `guix pack --format=deb --symlink=/usr/bin/hello=bin/hello hello`
* It is increasingly easy to build variants of packages in Guix. You can [derive
   variants](http://guix.gnu.org/manual/en/html_node/Defining-Package-Variants.html)
  of packages (with different configuration options, different upstream
  versions, different dependencies, etc) or apply
  [build time transformations](http://guix.gnu.org/manual/en/html_node/Package-Transformation-Options.html)
  to do many of those things without even creating/editing a recipe.
* [https://guix.gnu.org/en/blog/2023/the-filesystem-hierarchy-standard-comes-to-guix-containers/](https://guix.gnu.org/en/blog/2023/the-filesystem-hierarchy-standard-comes-to-guix-containers/)
  introduces the ability to run "containers" in `guix shell` which present an
  FHS compatible file-system layout. TBD whether we can also use this in
  container images?

## References

- [A packaging tutorial for Guix](https://guix.gnu.org/blog/2018/a-packaging-tutorial-for-guix/)
- [Scheme API](https://guix.gnu.org/en/manual/en/html_node/Programming-Interface.html)
- [Packaging EDSL](https://guix.gnu.org/manual/en/html_node/Defining-Packages.html)
- [A Scheme Crash Course](https://guix.gnu.org/cookbook/en/html_node/A-Scheme-Crash-Course.html)
- [Nonguix channel](https://gitlab.com/nonguix/nonguix)
- [System Crafters wiki -- Nonguix](https://wiki.systemcrafters.cc/guix/nonguix-installation-guide)
- [Building a Secure Software Supply Chain with GNU Guix](https://programming-journal.org/2023/7/1/)
- [Identifying software](https://guix.gnu.org/en/blog/2024/identifying-software/) gives an excellent summary of Guix features for identifying software, source code and binaries, and further gives a nicer term for JeOS--application bundles.
