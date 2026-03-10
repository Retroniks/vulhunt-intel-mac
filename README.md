# VulHunt Community Edition for Intel Macs

This repository is a **community fork** of [VulHunt Community Edition](https://github.com/vulhunt-re/vulhunt), maintained by **Retroniks** to verify, preserve, and distribute **Intel Mac builds on macOS x86_64**.

The upstream project currently publishes macOS ARM64 builds. This fork exists to make **Intel Mac support** visible and practical for users who cannot or do not want to compile the project themselves.

## Mission

This fork is intended to help Intel Mac users by providing:

- verified **macOS x86_64** build support
- **prebuilt release binaries** via GitHub Releases
- minimal, honest fork maintenance without rebranding the upstream project
- a practical distribution path for users who would otherwise be excluded

This is a **community compatibility and distribution fork**. It does **not** replace the upstream project and does **not** claim authorship of VulHunt itself.

## Status

- **Build verified on Intel Mac**
- **Platform:** macOS x86_64
- **Main binary:** `vulhunt-ce`
- **CLI verified:** yes
- **Prebuilt Intel Mac binaries:** intended to be published via GitHub Releases

Verification example:

```bash
./target/release/vulhunt-ce --help
file ./target/release/vulhunt-ce
```

Expected architecture output:

```text
Mach-O 64-bit executable x86_64
```

## Upstream project

Original upstream repository:

- `vulhunt-re/vulhunt`

VulHunt is a vulnerability hunting framework developed by Binarly's Research team. It is designed to help security researchers and practitioners identify vulnerabilities in software binaries and UEFI firmware. VulHunt is built on top of Binarly's Binary Analysis and Inspection System (BIAS), which provides a powerful and flexible environment for analysing and understanding binaries. VulHunt integrates with the capabilities of the Binarly Transparency Platform (BTP) to enable large-scale vulnerability management, hunting, and triage capabilities.

VulHunt Community Edition is a free and open-source version of the VulHunt engine within the BTP, designed to facilitate community-developed rulepacks and integrations.

## Building (with cargo-make)

### Prerequisites

```bash
cargo install cargo-make
```

### Building

```bash
cargo make --profile <development|release> build
```

With support for Binary Ninja:

```bash
cargo make --profile <development|release> build --features=bndb
```

### Installation

```bash
cargo make --profile <development|release> install
```

With support for Binary Ninja:

```bash
cargo make --profile <development|release> install --features=bndb
```

## Building (without cargo-make)

### Prerequisites

```bash
git submodule update --init
```

Install LuaJIT with requisite patches:

```bash
git clone https://github.com/LuaJIT/LuaJIT.git -b v2.1
cd LuaJIT
git apply /path/to/vulhunt-ce/patches/luajit-vulhunt.patch
```

For macOS:

```bash
export MACOSX_DEPLOYMENT_TARGET=$(sw_vers -productVersion)
```

For macOS and Linux:

```bash
make BUILDMODE='static'
export LUA_LIB=/path/to/LuaJIT/src/
export LUA_LIB_NAME=luajit
export LUA_LINK=static
```

For Windows:

```bash
cd src
msvcbuild.bat BUILDMODE='static'
set LUA_LIB=C:\path\to\LuaJIT\src\
set LUA_LIB_NAME=lua51
set LUA_LINK=static
```

### Building

```bash
cargo build --release
```

With support for Binary Ninja:

```bash
cargo build --release --features=bndb
```

### Packaging

Prerequisites:

```bash
cargo install cargo-make
```

Build packages for the current platform:

```bash
cargo make prepare-package --features=...
```

## Usage

### Scanning binaries

```bash
vulhunt-ce scan <INPUT> -o <OUTPUT> -d <BIAS_DATA> -r <RULES> [OPTIONS]
```

Options:

- `<INPUT>`: Path to the binary, BA2 archive, or BNDB file to scan
- `-o, --output <OUTPUT>`: Path to write output JSON
- `-d, --data <BIAS_DATA>`: Directory containing auxiliary data (processor specifications, etc.). Can also be set via `BIAS_DATA` environment variable
- `-r, --rules <RULES>`: Directory containing VulHunt rules. Can also be set via `BIAS_VULHUNT_RULES` environment variable
- `-m, --modules <MODULES>`: Directory containing VulHunt modules (optional). Can also be set via `BIAS_VULHUNT_MODULES` environment variable
- `--loader <LOADER>`: Configure the loader to use (default: `component`). Available loaders:
  - `component`: Scan single binary files
  - `ba2`: Scan BA2 (Binarly Archive 2) archives containing multiple components
  - `bndb`: Scan Binary Ninja databases (requires `--features=bndb` at build time)
- `--pretty`: Format output for human consumption and render issues to stdout
- `--stream`: Format output as a stream of JSONL messages
- `--compress`: Compress output JSONL stream with Zstandard

### Starting the MCP server

By default, VulHunt starts a streaming HTTP server with SSE transport at `http://127.0.0.1:8080`:

```bash
vulhunt-ce mcp -d <BIAS_DATA> [OPTIONS]
```

### BA2 archive utilities

```bash
vulhunt-ce ba2 list-components <INPUT>
vulhunt-ce ba2 extract-component <INPUT> -o <OUTPUT> --component-id <UUID>
```

### BTP integration

All BTP commands require authentication.

```bash
vulhunt-ce btp --help
```

## Releases

Prebuilt Intel Mac binaries should be distributed via **GitHub Releases**, not committed into the repository history.

Recommended release asset naming:

- `vulhunt-ce-macos-x86_64.zip`

Optional:

- `SHA256SUMS.txt`

## License

This project remains licensed under the **GNU General Public License v3.0**. See [LICENSE](LICENSE) for details.

Copyright (c) 2026 Binarly Inc. and VulHunt developers.

Additional fork-specific maintenance and compatibility and distribution work in this repository is provided by **Retroniks**.
