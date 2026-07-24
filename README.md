# webkitgtk-prebuilt

A reusable WebKitGTK build for Sinty OS. Building WebKitGTK from source takes
hours, so the OS image must not rebuild it on every bake and must never carry the
built binary in git. This repository builds WebKitGTK once per version in CI and
publishes the captured staging and target files as a tarball on a tagged GitHub
release. singularity-os then downloads that release asset at build time (its
`webkitgtk-prebuilt` package) instead of committing a 75 MB blob.

## How it works

- `capture.sh` copies WebKitGTK's staging and target files, from a green
  buildroot `webkitgtk` build, into `webkitgtk-prebuilt-<version>-<arch>.tar.zst`,
  laid out as `staging/` and `target/` so buildroot's extract drops them in place.
- The release workflow checks out this repo and singularity-os (the br2-external
  tree), builds `webkitgtk` with buildroot, runs `capture.sh`, and attaches the
  tarball to the release for the pushed tag.

The build is self-contained (buildroot builds the toolchain and WebKitGTK's
dependency tree). It could later reuse the OS SDK toolchain to skip rebuilding
the compiler, but the dependencies still build from source, so the gain is small.

## Release

Tag a version matching the OS `WEBKITGTK_VERSION` (for example `v2.52.3`):

```sh
git tag v2.52.3 && git push origin v2.52.3
```

CI builds and attaches `webkitgtk-prebuilt-2.52.3-x86_64.tar.zst`.

## License

The build scripts here are GPL-3.0-or-later. The captured artifact is WebKitGTK
and its dependencies, under their own licenses (LGPL-2.0+, BSD-2-Clause).
