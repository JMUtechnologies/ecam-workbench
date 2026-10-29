# Workbench container

The container is the isolated Ubuntu 24.04 / Clang 18 environment used by ECAM Workbench. The
wrapper mounts the separately cloned PS5-ECAM source at `/src` and this repository read-only at
`/workbench`.

From the ECAM Workbench root, with Docker Desktop running:

```sh
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh image
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh doctor
ECAM_SOURCE_DIR=../PS5-ECAM bash tools/docker/workstation.sh run make test-retro
```

See [the setup guide](../../docs/SETUP-MAC-APPLE-SILICON.md) for clone and build steps.
