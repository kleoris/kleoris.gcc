# kleoris.gcc

[![Build GCC 16](https://github.com/kleoris/kleoris.gcc/actions/workflows/build_gcc.yml/badge.svg)](https://github.com/kleoris/kleoris.gcc/actions/workflows/build_gcc.yml)
![C++26](https://img.shields.io/badge/C%2B%2B-26-blue)
![GCC 16](https://img.shields.io/badge/GCC-16-orange)
![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux%20%7C%20Windows-lightgrey)
![License](https://img.shields.io/badge/license-GPLv3-green)

CI toolchain builder for **GCC 16 (with C++26 P2996 static-reflection support)** (`-freflection`).

This repository contains nothing but a GitHub Actions workflow that builds GCC 16 from source for all three supported platforms and publishes the result as a downloadable artifact. It is a companion to [kleoris](https://github.com/kleoris/kleoris_cpp), which requires a C++26 compiler with P2996 support.

## What gets built

- Languages: `c`, `c++`
- C++ standard: `c++26`
- Key flag: `-freflection` (P2996 compile-time static reflection)
- Multilib: disabled
- Bootstrap: disabled (single-stage build for CI speed)

## Install locations

| Platform | Prefix |
|---|---|
| macOS | `/opt/homebrew/gcc-16` |
| Linux | `/usr/local/gcc-16` |
| Windows | `C:/gcc-16` |

Binaries are installed as `gcc-16` / `g++-16` (see `--program-suffix=-16` in workflow).

## Platforms

| Artifact | Runner | Source |
|---|---|---|
| `gcc-16-macos-gcc-16` | `macos-latest-xlarge` (Apple Silicon) | [iains/gcc-darwin-arm64](https://github.com/iains/gcc-darwin-arm64) |
| `gcc-16-linux-gcc-16` | `ubuntu-latest` | [gcc-mirror/gcc](https://github.com/gcc-mirror/gcc) |
| `gcc-16-win-gcc-16` | `win-x64` | [gcc-mirror/gcc](https://github.com/gcc-mirror/gcc) |

## Github workflow usage

1. Go to **Actions → Build GCC 16 with P2996 support**.
2. Click **Run workflow**.
3. Select a build target (see [Selecting a single platform](#selecting-a-single-platform))
3. Optionally override the branch/tag for each platform (defaults to `master` and latest macOS wip).
4. Once complete, download the `.tar.xz` artifact for your platform from the run summary.

## Selecting a single platform

The `platform` input accepts `all` (default) or any single platform name:

```
all            — builds all three matrix entries in parallel
macos-gcc-16   — macOS (Apple Silicon) only
linux-gcc-16   — Linux only
win-gcc-16     — Windows only
```

Internally a lightweight `prepare` job runs first and uses `jq` to filter the full platform list down to the requested entry. The `main` build job then consumes this filtered JSON as its dynamic strategy matrix via `fromJSON(needs.prepare.outputs.matrix)`. This sidesteps the GitHub Actions limitation that the `matrix` context is unavailable in a job-level `if` condition.

## macOS note

The macOS build uses the unofficial [iains/gcc-darwin-arm64](https://github.com/iains/gcc-darwin-arm64) fork, which carries Apple Silicon patches not yet merged upstream. Prerequisites (`gmp`, `mpfr`, `libmpc`, `isl`) are installed via Homebrew, which is pre-installed on GitHub-hosted macOS runners.

## Build notes

* `--disable-fixincludes` : the flag prevents GCC from patching the includes for the build of `libiberty.a`. we do that because the build is transient and immediately repackaged for distribution

# Using the compiler

## macOS

```shell
sudo tar -xJf gcc-16-macos-gcc-16.tar.xz -C /opt/homebrew
/opt/homebrew/gcc-16/bin/clang --version
```
