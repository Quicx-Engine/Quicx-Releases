# Quicx releases

[![Latest release](https://img.shields.io/badge/release-v2.1.0-orange)](releases/v2.1.0)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Prebuilt binaries of [Quicx](https://github.com/anastassow/Quicx), a task queue for devices and servers with little memory. Quicx never goes past its memory budget, and when the budget is full it keeps the most valuable work. Each binary is the whole program: the daemon and its command line, with no dependencies beyond the system C library.

This repository only holds the builds. The source, the documentation and the measurements are in [anastassow/Quicx](https://github.com/anastassow/Quicx) and on [quicx.dev](https://quicx.dev).

## Install

```sh
curl -fsSL https://quicx.dev/install.sh | sh
```

The installer picks the binary for your OS and CPU from this repository and puts it in `/usr/local/bin/quicx`. To install a particular version:

```sh
curl -fsSL https://quicx.dev/install.sh | QUICX_VERSION=v2.0.0 sh
```

Then:

```sh
quicx start          # port 16381, 8 MiB budget
quicx status
quicx stop
```

## Download by hand

v2.1.0, the latest:

| Platform | File | Size |
|---|---|---:|
| Linux, x86-64 | [`quicx-linux-x86_64`](releases/v2.1.0/quicx-linux-x86_64) | 37,664 B |
| Linux, arm64 (Raspberry Pi with a 64-bit OS) | [`quicx-linux-aarch64`](releases/v2.1.0/quicx-linux-aarch64) | 36,616 B |
| macOS, Apple Silicon | [`quicx-darwin-arm64`](releases/v2.1.0/quicx-darwin-arm64) | 88,816 B |
| macOS, Intel | [`quicx-darwin-x86_64`](releases/v2.1.0/quicx-darwin-x86_64) | 47,559 B |

```sh
curl -fLO https://raw.githubusercontent.com/Quicx-Engine/Quicx-Releases/main/releases/v2.1.0/quicx-linux-aarch64
chmod +x quicx-linux-aarch64
sudo mv quicx-linux-aarch64 /usr/local/bin/quicx
```

The Linux builds use glibc, so they run on Debian, Ubuntu, Raspberry Pi OS, Fedora and most other distributions, but not on Alpine.

## Check a download

Every version folder has a `SHA256SUMS` file. Download it next to the binary and check:

```sh
curl -fLO https://raw.githubusercontent.com/Quicx-Engine/Quicx-Releases/main/releases/v2.1.0/SHA256SUMS
sha256sum --ignore-missing -c SHA256SUMS          # Linux
shasum -a 256 --ignore-missing -c SHA256SUMS      # macOS
```

The line for your file should end in `OK`.

## Versions

| Version | Published | What changed |
|---|---|---|
| [v2.1.0](releases/v2.1.0) | 2026-11-10 | Value-aware admission: tasks carry a level from 0 to 15, and a full budget evicts a less valuable waiting task only when the allocator proves the freed memory fits the new one. Several daemons per machine (`--port` for `status` and `stop`). `make MAX_PAYLOAD=N` for bigger tasks. |
| [v2.0.0](releases/v2.0.0) | 2026-11-10 | A new allocator, apool: memory follows the traffic, 46,593 tasks of 100 B fit in 8 MiB, and memory goes back to the OS after a burst. Strict config validation, a separate pool for connections and workers. |
| [v1.0.4](releases/v1.0.4) | 2026-11-05 | Fixes: tasks with a payload were not delivered, a crash on DONE without READY, a misread after a refused payload. |
| [v1.0.3](releases/v1.0.3) | 2026-08-01 | Payloads limited to 1,024 bytes; producers receive DONE and the worker's real FAILED reason. |
| [v1.0.2](releases/v1.0.2) | 2026-05-31 | Failed sends reported to the producer; config size classes replace the defaults instead of merging. |
| [v1.0.1](releases/v1.0.1) | 2026-05-19 | `--config` optional, with a built-in default configuration. |
| [v1.0.0](releases/v1.0.0) | 2026-04-24 | First public release. |

The full notes are in the [Quicx changelog](https://github.com/anastassow/Quicx/blob/main/CHANGELOG.md) and on [quicx.dev/docs/changelog](https://quicx.dev/docs/changelog).

## How the builds are made

Nobody edits these files by hand. Pushing a version tag to [anastassow/Quicx](https://github.com/anastassow/Quicx) starts its *Build & Publish Release* workflow, which cross-compiles the four binaries from that tag with `zig cc -O2`, strips them, and commits the folder `releases/vX.Y.Z` here. You can rebuild any of them from the tagged source with `make release VERSION=X.Y.Z`.

## Licence

MIT, the same as Quicx. See [LICENSE](LICENSE).
