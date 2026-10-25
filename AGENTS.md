# SoC agent guide

This repository is the shared **SDM710** layer of UniXNU. It owns hardware data and platform
facts common to every SDM710 board: the DTSI include, shared kernel settings and shared XNU
patches. It does not own board wiring, partitions, firmware selection or OS policy.

## Read first

| Task | Required reading |
| --- | --- |
| Every change | [SoC support](https://unixnu.com/porting/soc-support), [device tree](https://unixnu.com/porting/device-tree), UniXNU `components.toml` |
| Kernel | [Source and patches](https://unixnu.com/xnu/source-and-patches), selected OS profile |
| Verification | [Porting](https://unixnu.com/porting/), the device repository's `porting.toml` |

## Rules

1. Put a value here only when it is shared by more than one SDM710 board **and** has a source:
   a vendor or mainline DTS line, a firmware table, or a hardware measurement. Record the
   source in a comment.
2. Board-specific memory, CPU selection, peripherals and reservations stay in the device DTS.
   The board includes `dts/sdm710.dtsi`; it does not copy it.
3. A shared XNU patch goes in `patches/xnu/series` in dependency order, after the ARM64 profile
   patches and before the OS and device series. A board-only patch belongs in that board's
   repository.
4. Keep the device list in `README.md` current, and update the device record's SoC evidence
   when a measurement here changes.

## Checks

From a UniXNU checkout, with a device that selects this repository:

```sh
./unixnu status --json -D ../UniXNU-<device>
./unixnu tree -D ../UniXNU-<device>
```

Confirm the resolved SoC commit, the DTSI include and the patch order before a device build
depends on a change here.
