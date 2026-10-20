# LIVALL CanMV K230 v1.8

This branch uses the official CanMV K230 v1.8 multi-repository release as its
base, retaining LIVALL board, PMU and USB customizations. `release-bases.xml`
preserves the official release manifest used for the migration.

## Download

Start in a new directory:

```sh
mkdir livall-k230-v1.8
cd livall-k230-v1.8
repo init -u https://github.com/livall-2026/manifest -b livall-v1.8 -m default.xml \
  --repo-url=https://github.com/canmv-k230/git-repo.git --repo-branch stable
repo sync -c -j4
```

If your installed launcher is `k230 repo`, use that prefix instead of `repo`.
`default.xml` pins all project commits. `development.xml` follows the six
LIVALL source forks on `livall-v1.8`, keeping other dependencies pinned.
The previous development snapshot is preserved on the `livall` branch.

## Preserved customizations

* K230D BPI Zero eMMC boot and fixed 1.8V configuration.
* RTL8733BS Wi-Fi on SDIO1.
* PMU INT4/PAD68 sampled shutdown request, application acknowledgement,
  listener ownership and GPIO initialization guard.
* PMU polling HAL and the `machine.PMU` MicroPython interface.
* CDC/MTP/ADB composite USB descriptors and registration.
* Existing local tests and historical migration/validation reports.

At the user's request, SDIO/MMC sources are the official v1.8 implementations
and HS200 is disabled. Enabling HS200 again requires a separate driver change
and hardware validation.

## Compatibility backports

The local PMU features rely on the newer shutdown/wakeup contract. Two focused
upstream PMU changes are backported into the v1.8 kernel and HAL, together with
the matching PMU example updates. No unrelated main-branch changes are merged.
`release-lock.json` records exact release bases, backports and final commits.

The MicroPython release tree has a gitlink to `lib/mynewt-nimble` at
`42849560ba7906f023f61e5f7ff3709ba2c1dfca`, which the official release manifest
omits. This manifest explicitly checks out that same upstream commit to retain
a complete MicroPython source tree. The other three extra repositories from
the former development checkout are outside this release manifest.

## Source forks

| Checkout path | LIVALL fork | Upstream |
| --- | --- | --- |
| `.` | [k230_rtos_sdk](https://github.com/livall-2026/k230_rtos_sdk) | kendryte/k230_rtos_sdk |
| `src/canmv` | [canmv_k230](https://github.com/livall-2026/canmv_k230) | kendryte/canmv_k230 |
| `src/uboot/uboot` | [u-boot](https://github.com/livall-2026/u-boot) | canmv-k230/u-boot |
| `src/rtsmart/rtsmart` | [rtsmart](https://github.com/livall-2026/rtsmart) | canmv-k230/rtsmart |
| `src/rtsmart/libs` | [k230_rtsmart_lib](https://github.com/livall-2026/k230_rtsmart_lib) | canmv-k230/k230_rtsmart_lib |
| `src/rtsmart/examples` | [k230_rtsmart_examples](https://github.com/livall-2026/k230_rtsmart_examples) | canmv-k230/k230_rtsmart_examples |

## Boot and Windows enumeration fixes

The first customized v1.8 image was tested on a 128 MiB board and failed in
SPL before RT-Smart started. Its 44 MiB application ELF exceeded the fixed
upper-third preload window (about 42.7 MiB). SPL now chooses a page-aligned
preload address that fits the actual ELF, while retaining DRAM-bound and
compressed-source-overlap validation.

The ADB Windows MS OS property table now uses its actual composite interface
number. Invalid or absent interface properties are rejected without reading
past the table. Regression tests exercise real SPL functions and both USB
descriptor modes.

Use the image with `bootfix` in its name. The earlier image without that
suffix is superseded. The fixed image still requires hardware boot and USB
enumeration verification; successful compilation is not hardware validation.

## Validation

The pre-sync, upstream-upgrade, repair and storage-fix reports under `tests/pmu`
are historical records, not validation results for this branch. The current
release migration checks are recorded in `release-validation.json`.
Hardware validation and flashing are separate from source and build checks.
