# LIVALL K230 multi-repository SDK

This manifest preserves the source versions and local modifications from the
existing K230 checkout. It is forked from canmv-k230/manifest. Only the SDK root
and four modified source repositories are forked into livall-2026; all other
dependencies are fetched directly from their official GitHub repositories.

## Download the reproducible snapshot

Run these commands in a new, empty directory, using the existing `repo` launcher
(or substitute `k230 repo` if that is your installed launcher):

```sh
mkdir livall-k230
cd livall-k230
repo init -u https://github.com/livall-2026/manifest -b livall -m default.xml \
  --repo-url=https://github.com/canmv-k230/git-repo.git --repo-branch stable
repo sync -c -j4
```

`default.xml` pins every project to a full commit SHA. `development.xml` keeps
the five LIVALL projects on `refs/heads/livall` while pinning third-party
dependencies. To follow LIVALL development, select `-m development.xml` during
initialization. Do not run initialization or sync over an existing checkout
with uncommitted changes.

## Repository mapping

| Checkout path | LIVALL fork | GitHub upstream | Preserved changes |
| --- | --- | --- | --- |
| `.` | [k230_rtos_sdk](https://github.com/livall-2026/k230_rtos_sdk) | [kendryte/k230_rtos_sdk](https://github.com/kendryte/k230_rtos_sdk) | PMU host regression tests |
| `src/uboot/uboot` | [u-boot](https://github.com/livall-2026/u-boot) | [canmv-k230/u-boot](https://github.com/canmv-k230/u-boot) | BPI Zero MMC 1.8V |
| `src/rtsmart/rtsmart` | [rtsmart](https://github.com/livall-2026/rtsmart) | [canmv-k230/rtsmart](https://github.com/canmv-k230/rtsmart) | Board config and PMU shutdown handshake |
| `src/rtsmart/libs` | [k230_rtsmart_lib](https://github.com/livall-2026/k230_rtsmart_lib) | [canmv-k230/k230_rtsmart_lib](https://github.com/canmv-k230/k230_rtsmart_lib) | Polling PMU HAL APIs |
| `src/canmv` | [canmv_k230](https://github.com/livall-2026/canmv_k230) | [kendryte/canmv_k230](https://github.com/kendryte/canmv_k230) | `machine.PMU` Python API |

The GitHub SDK and CanMV repository names differ from the Gitee mirror names:
the Gitee root `canmv_k230` maps to `kendryte/k230_rtos_sdk`, and the Gitee
`canmv` project maps to `kendryte/canmv_k230`.

## Development and upstream updates

The default branch on all LIVALL forks is `livall`. After a snapshot checkout,
create a topic branch in the project being changed (`repo start <topic> <path>`)
and push that project to its LIVALL fork. Update its SHA in `default.xml` only
after testing the combined SDK. Upstream changes can be fetched from the
GitHub parent and merged into the LIVALL branch; the GitHub fork relationship
is retained.

Validation before migration: existing `python3 tests/pmu/test_pmu_host.py`
passed, and all pre-migration project commits were verified through the GitHub
API. A full firmware build and hardware validation were not performed.

`migration-lock.json` records original commits, migrated commits, changed
files and the upstream manifest commit. Build outputs, toolchains and ignored
local files are not part of this source snapshot.
