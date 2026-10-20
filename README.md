# LIVALL K230 development snapshot

This branch uses the latest tracking-branch commits from the official GitHub
development manifest, resolved on 2026-10-20. Third-party dependencies pinned
by upstream remain pinned. The 24 source revisions are frozen in `default.xml`
and documented in `upstream-lock.json`; `upstream-bases.xml` preserves the
official development manifest.

## Checkout and build

```sh
mkdir livall-k230
cd livall-k230
repo init -u https://github.com/livall-2026/manifest -b livall-main -m default.xml \
  --repo-url=https://github.com/canmv-k230/git-repo.git --repo-branch stable
repo sync -c -j4
env -u DEBUG make k230d_canmv_bpi_zero_defconfig
env -u DEBUG make
```

`development.xml` tracks the LIVALL source branches rather than frozen custom
commits. All source forks use `livall-main` as their default branch. The
`livall-v1.8` branches have been retired.

## Preserved customizations

- PMU PAD68 / INT4 sampled shutdown handshake, startup GPIO guard, owner and
  generation tracking, polling HAL APIs and `machine.PMU`.
- K230D BPI Zero board configuration, eMMC at 1.8 V, HS200 disabled,
  RTL8733BS Wi-Fi on SDIO1 and CDC/MTP/ADB selection.
- Upstream composite USB / full-speed descriptors with Windows MS OS property
  interface indexing and invalid-request checks retained.
- Source-built U-Boot and upstream fixed application preload layout.

Any nonempty environment variable `DEBUG`, including `release` or `0`, selects
a debug CanMV build and skips final ELF stripping. Always unset it for release
builds. If changing build modes, clean CanMV first.

## Validation

`validation.json` records source, configuration, regression and image checks
for this snapshot. Prior reports in the SDK tests directory describe older
snapshots. Successful compilation does not establish hardware boot or USB
enumeration; flashing and hardware validation remain separate.
