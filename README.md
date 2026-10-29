# ECAM Workbench

ECAM Workbench is the standalone Docker development environment and Mac setup guide for
contributors to [PS5-ECAM](https://github.com/JMUtechnologies/PS5-ECAM). It keeps the Linux build
tools in this repository while mounting a separate PS5-ECAM source checkout into the container.

The workstation is designed around the Mac with Apple silicon used to prepare the project. Docker
Desktop runs the pinned `linux/amd64` Ubuntu environment for the PS5 build toolchain.

## Quick start

Clone both repositories side by side. The source command checks out the current integration branch;
developers can switch it to whichever PS5-ECAM branch they are contributing to:

```sh
mkdir -p ~/Developer/ps5-ecam
cd ~/Developer/ps5-ecam
git clone https://github.com/JMUtechnologies/ecam-workbench.git ECAM-Workbench
git clone --single-branch --branch integration/gameboy-family \
  https://github.com/JMUtechnologies/PS5-ECAM.git PS5-ECAM
cd ECAM-Workbench
```

Install and start [Docker Desktop for Mac with Apple silicon](https://docs.docker.com/desktop/setup/install/mac-install/),
then run:

```sh
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh image
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh doctor
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh run make deps
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh run make test-retro
```

For broader host checks and a PS5 cross-build after dependencies are ready:

```sh
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh run make test-contribution
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh run make build eden
```

The detailed [Apple silicon setup and troubleshooting guide](docs/SETUP-MAC-APPLE-SILICON.md)
covers the commands, persistent build data, native x86 qualification limits and the distinction
between host validation and PS5 testing.

## What this project does

- Builds a local Ubuntu 24.04 / Clang 18 Docker image with pinned Meson and CMake versions.
- Mounts the PS5-ECAM checkout at `/src`; build products and downloaded dependencies remain in that
  source checkout's ignored folders.
- Keeps incremental RADV work in a named Docker volume and Eden cache in a separate named volume.
- Does not deploy builds to a console or publish a release.

No games, saves, BIOS, keys, firmware or DLC are included. The workstation does not move, delete or
rewrite data on a PS5. See [LICENSE](LICENSE) for the workstation's license.
