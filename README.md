# kleoris.gcc

CI toolchain builder for **GCC 16 (with C++26 P2996 static-reflection support)** (`-freflection`).

This repository contains nothing but a GitHub Actions workflow that builds GCC 16 from source for all three supported platforms and publishes the result as a downloadable artifact. It is a companion to [kleoris](https://github.com/kleoris/kleoris_cpp), which requires a C++26 compiler with P2996 support.

## Platforms

| Artifact | Runner | Source |
|---|---|---|
| `gcc-16-macos-gcc-16` | `macos-latest-xlarge` (Apple Silicon) | [iains/gcc-darwin-arm64](https://github.com/iains/gcc-darwin-arm64) |
| `gcc-16-linux-gcc-16` | `ubuntu-latest` | [gcc-mirror/gcc](https://github.com/gcc-mirror/gcc) |
| `gcc-16-win-gcc-16` | `win-x64` | [gcc-mirror/gcc](https://github.com/gcc-mirror/gcc) |

## Usage

1. Go to **Actions → Build GCC 16 with P2996 support**.
2. Click **Run workflow**.
3. Optionally override the branch/tag for each platform (defaults to `master`).
4. Once complete, download the `.tar.xz` artifact for your platform from the run summary.

## Install locations

| Platform | Prefix |
|---|---|
| macOS | `/opt/homebrew/gcc-16` |
| Linux | `/usr/local/gcc-16` |
| Windows | `C:/gcc-16` |

Binaries are installed as `gcc-16` / `g++-16` (see `--program-suffix=-16` in workflow).

## What gets built

- Languages: `c`, `c++`
- C++ standard: `c++26`
- Key flag: `-freflection` (P2996 compile-time static reflection)
- Multilib: disabled
- Bootstrap: disabled (single-stage build for CI speed)

## macOS note

The macOS build uses the unofficial [iains/gcc-darwin-arm64](https://github.com/iains/gcc-darwin-arm64) fork, which carries Apple Silicon patches not yet merged upstream. Prerequisites (`gmp`, `mpfr`, `libmpc`, `isl`) are installed via Homebrew, which is pre-installed on GitHub-hosted macOS runners.
