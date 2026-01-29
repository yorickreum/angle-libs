# angle-libs

Builds precompiled ANGLE libraries (static + shared) and bundles headers for distribution. This exists because prebuilt binaries are not published outside of Chrome, and building ANGLE locally is time‑ and disk‑intensive.

## What this repo does

- Fetches ANGLE using `depot_tools` and `gclient`.
- Builds `libEGL`, `libGLESv2`, `libEGL_static`, and `libGLESv2_static`.
- Packages the libs + headers + `LICENSE` into per‑OS tarballs.

## CI

GitHub Actions builds on:

- Ubuntu (x64, x86, arm64)
- macOS (arm64, x64)
- Windows (x86, x64, arm64)
- iOS (arm64)

## Revision pinning

The ANGLE revision is pinned in `REVISION` (one line). Examples:

- Branch ref: `refs/heads/main`
- Tag: `refs/tags/chrome_m124`
- Commit SHA: `3c1e0f3b7e5e6c8d9a...`

Update `REVISION` and push to change what CI builds.

## Artifacts

Each build produces a tarball named:

```
angle-<LABEL>-<REVISION>.tar.gz
```

Contents:

- Shared libraries (`.so` / `.dylib` / `.dll`) for desktop platforms
- Static libraries (`libEGL_static.a` / `libGLESv2_static.a` / `libGLESv2_static.lib`)
- Headers (`include/`)
- `LICENSE`

### Static library note

ANGLE builds thin static archives by default (fast for local builds but not
redistributable). CI applies `patches/angle-static-libs.patch` to disable thin
archives and set `complete_static_lib = true` for `libGLESv2_static`, producing
portable static libs.

Static libraries are toolchain/ABI-specific:
- Windows: MSVC `.lib` cannot be linked with MinGW; use MSVC-built apps or load the DLLs dynamically.
- macOS/iOS: use clang/Xcode with matching arch (arm64 vs x64).

### Debug symbols

CI builds use `symbol_level=0`, so debug symbols are not produced or packaged.
This reduces artifact size drastically, especially for static libraries.

## License

ANGLE is BSD 3‑Clause. When distributing the packaged artifacts, include the ANGLE license (already bundled) and comply with any third‑party licenses from bundled dependencies.

## References

Build instructions (official ANGLE DevSetup):

```
https://chromium.googlesource.com/angle/angle/+/HEAD/doc/DevSetup.md
```

ANGLE project home:

```
https://chromium.googlesource.com/angle/angle/
```

Chromium Dash (Stable channel releases for Windows):

```
https://chromiumdash.appspot.com/fetch_releases?channel=Stable&platform=Windows
```
