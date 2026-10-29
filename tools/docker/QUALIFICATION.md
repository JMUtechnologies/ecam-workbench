# Build qualification notes

ECAM Workbench supports local development from a Mac with Apple silicon by running the
`linux/amd64` container through Docker Desktop. It does not make this an x86-64 host.

The Workbench's local `doctor` checks the Docker daemon, compiler tools and basic C11 support. A
successful local check or source build is not a native x86 JIT qualification and is not a PS5
hardware test.

For native x86-64 runner qualification, see the workflow in the PS5-ECAM repository's
`workstation/native-qualification` branch. That workflow's result still does not establish game
compatibility on a PS5; console results must be recorded separately by system.
