# Alpine: https://alpinelinux.org

Not originally considered as way back in 2019 Alpine's tools for building images were
focused on producing images installing the distribution or a base container image,
neither allowed for customisation. However, in 2022 things are very different and the
team at Chainguard are working on tools to:
* enable folks to build custom [distroless-like images with Alpine] -- [apko]
* make it easier to package arbitrary software for Alpine -- [melange]

In our talk in 2019 we confidently stated that most engineers were not interested in
packaging their software for Linux distributions. The increase in supply chain attacks
in the intervening years _may_ have increased organisations willingness to be more
rigourous about their engineering practices, but signs still point to that cost needing
to be borne at the institutional level.

[distroless-like images with Alpine]: https://blog.chainguard.dev/introducing-apko-bringing-distroless-nirvana-to-alpine-linux/
[apko]: https://github.com/chainguard-dev/apko
[melange]: https://github.com/chainguard-dev/melange
