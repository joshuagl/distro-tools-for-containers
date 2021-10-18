Pro: can run on macOS and Linux host


## Go image

`$ nix-build go-container.nix`
`$ docker-load < result`
`$ docker run -it go-image:latest /bin/go version`
`go: creating work dir: stat /tmp: no such file or directory`

369M tarball, but not ready to use...

## Why not `nix`?

Our KubeCon NA '19 talk mentioned `nix`/NixOS as one of two functional Linux
distributions, but all of our examples used Guix _despite_ a tacit admission that Guix
was unlikely to become a defacto tool in the cloud arena (due to license and GNU tool
bias). Why?

* I did not have the time to learn either the Nix expression language or Guix's
  [EDSL](https://guix.gnu.org/manual/en/html_node/Defining-Packages.html) while
  working on the talk – `guix` is usable as a command-line tool.
  * The command-line tool in `guix pack` has only grown more, and more interesting,
    options in the interim as they implement more `spack` inspired features.
  * Hallway discussion suggested that Nix derivations are more difficult to
    figure out than Dockerfiles and that there is little appetite to learn a
    more complex container build system.
* Guix has built-in import tools (though it turns out, not very reliable ones), whereas
  Nix relies on a suite of ad-hoc `*2nix`, i.e.
  [`pypi2nix`](https://github.com/nix-community/pypi2nix) tools.

## Some notes of seemingly relevant commands/features:

* [`nix bundle`](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-bundle.html) – would that it could create container images like `guix pack`

## `guix` features to find for or implement in `nix`

Guix has several neat features that I would love to see in nix:

| guix feature | nix equivalent | rationale |
| --- | --- | --- |
| `guix pack` | ? | ? |
| `guix import` and `guix import json` | ? | ? |
| `guix refresh` | ? | ? |

## References

* [Using Nix to build Docker images](https://yann.hodique.info/blog/using-nix-to-build-docker-images/)
* [Cheap Docker images with Nix](http://lethalman.blogspot.com/2016/04/cheap-docker-images-with-nix_15.html)
* [Nix expression language](https://nixos.org/manual/nix/stable/#ch-expression-language)
* [Nix Expression Language](https://nixos.wiki/wiki/Nix_Expression_Language) on
  nixos.wiki

## Useful nix things
* [`nix-diff`](https://github.com/Gabriel439/nix-diff)
