# Mac Apple silicon setup

This is the step-by-step guide for the Mac workstation used to develop PS5-ECAM. It uses Docker
Desktop to run an Ubuntu 24.04 / Clang 18 build environment in a `linux/amd64` container and keeps
the PS5-ECAM source checkout separate from this workstation project.

## Requirements

- A Mac with Apple silicon.
- Git in Terminal.
- [Docker Desktop for Mac with Apple silicon](https://docs.docker.com/desktop/setup/install/mac-install/),
  installed and running.
- Network access for downloading pinned source dependencies on the first build.

The project does not require installing Clang, the PS5 SDK or build dependencies directly on macOS.
Docker Desktop's x86-64 container emulation is used for the local build environment. Rosetta is not
required by the workstation commands.

## Clone the workstation and source

Create a parent folder, then clone the workstation and a PS5-ECAM source branch beside each other.
The example uses the current integration branch; switch the source checkout to whichever branch
you are contributing to:

```sh
mkdir -p ~/Developer/ps5-ecam
cd ~/Developer/ps5-ecam
git clone https://github.com/JMUtechnologies/ecam-workbench.git ECAM-Workbench
git clone --single-branch --branch integration/gameboy-family \
  https://github.com/JMUtechnologies/PS5-ECAM.git PS5-ECAM
cd ECAM-Workbench
```

Tell the wrapper where the source checkout lives. The following commands assume these sibling
folders; use an absolute path if yours are elsewhere.

```sh
export ECAM_SOURCE_DIR="$HOME/Developer/ps5-ecam/PS5-ECAM"
```

## Prepare Docker

Open Docker Desktop and wait until its engine is ready. Build the pinned container and run the
workstation check:

```sh
bash tools/docker/workstation.sh image
bash tools/docker/workstation.sh doctor
```

The container image targets `linux/amd64`. The source checkout mounts at `/src`. The container does
not mount Docker Desktop's daemon socket inside itself, run privileged, or access the PS5.

## Fetch dependencies and run checks

The first dependency fetch downloads the pinned source inputs into the PS5-ECAM checkout. Those
downloads remain in its ignored `.deps/` folder and can be reused on later runs.

```sh
bash tools/docker/workstation.sh run make deps
bash tools/docker/workstation.sh run make test-retro
bash tools/docker/workstation.sh run make test-contribution
```

After dependencies are prepared, the launcher and Eden can be built with:

```sh
bash tools/docker/workstation.sh run make build eden
```

For systems with separate donor builds, use the project-specific preparation and build steps in the
PS5-ECAM documentation. Some builds require PS5 SDK or vendor archives that developers must obtain
through the documented sources.

## Apple silicon qualification limits

Docker Desktop emulates the x86-64 Linux container on Apple silicon. It is useful for the repeatable
build environment and host checks, but it is not a native x86-64 host. In particular, the Eden JIT
executable-alias diagnostic cannot qualify this emulated environment. The [native x86-64
qualification workflow](https://github.com/JMUtechnologies/PS5-ECAM/blob/workstation/native-qualification/.github/workflows/native-qualification.yml)
shows the separate Linux runner workflow.

Host tests, cross-compilation and successful packaging do not establish PS5 gameplay compatibility.
Record actual console results per system; do not label a system tested just because it builds or
appears in the launcher.

## Persistent data and cleanup

- Build outputs and fetched sources are kept in the PS5-ECAM checkout's `build/` and `.deps/`
  folders.
- RADV incremental files use the named Docker volume `ecam-workbench_radv-work`.
- Eden's compiler cache uses `ecam-workbench_eden-cache`.
- Do not remove Docker volumes if you want to retain those incremental build files.

The workstation Docker setup itself does not connect to the console. Keep PS5 testing as a separate,
attended step and record only results observed on the hardware.
