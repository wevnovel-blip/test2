# Build strategy: the best system available for a Chromium Android build

## Limits that shape the design (GitHub docs)
- Standard hosted runner, public repo: 4 vCPU, 16 GB RAM, 14 GB SSD on the work volume. Private repo: 2 vCPU, 8 GB.
- Job limit: 6 hours on GitHub-hosted runners. Workflow run limit: 35 days.
- Actions cache: 10 GB per repository, least-recently-used eviction.
- Chromium's own Android instructions: x86-64 Linux, at least 8 GB RAM (more than 16 GB recommended), at least 100 GB free disk. Windows and macOS are not supported for Android builds.

## What the workflow does about it
1. **Disk**: `easimon/maximize-build-space` removes unused preinstalled toolchains and merges the free space of `/` and `/mnt` into one volume (about 100+ GB) mounted over the workspace. Skipped on self-hosted runners. The workflow aborts early if less than 85 GB is free.
2. **Source**: `gclient sync --no-history` on the exact Stable tag (no full history, about a third of the download).
3. **Compile cache**: `cc_wrapper = "ccache"` with `CCACHE_BASEDIR`, `CCACHE_COMPILERCHECK=content` and mtime/ctime sloppiness. A fresh checkout changes file timestamps on every run, so without these settings the cache would never hit. ccache is capped at 5 GB so it fits the 10 GB repo cache with room for one replacement; older cache entries are pruned after each save.
4. **Resumable**: the compile step is time-boxed to the job limit minus 25 minutes, the cache is saved, and the workflow dispatches itself again (max 15 runs). A new run restores the cache, so already compiled objects are reused.
5. **Fast GN args**: release, non-component, `symbol_level = 0`, no official/LTO build, warnings not fatal, static analysis off. Optional args are dropped automatically if a Chromium revision no longer declares them.
6. **Output**: the APK(s) and the exact `args.gn` are published as a GitHub Release (no 500 MB artifact quota problem).

## Fastest options, in order
1. **GitHub larger runner** (Team/Enterprise Cloud plans; paid per minute): set `CHROMIUM_RUNNER_JSON` to its label. 32-64 cores finish a full build in about one to two hours, in one run.
2. **Self-hosted Linux x64 runner** (your own PC/server/cloud VM with 16+ cores, 64 GB RAM, 150+ GB SSD): free for you on public repos; set the labels. Keep the checkout between runs to make rebuilds incremental.
3. **Free standard runner** (default): works, but expect roughly two to four chained runs for a first build; later builds are fast thanks to ccache.

Notes
- Chromium's build system uses Siso by default; Chromium developers state ccache behaves the same under Siso. `use_siso = false` (Ninja) is documented as short-lived, so the workflow does not set it.
- `use_remoteexec = true` needs Google-internal or paid remote-execution infrastructure and is not used.
- An `is_official_build = true` build (ThinLTO, PGO) is much slower and needs far more RAM; use it only on a large self-hosted runner.
