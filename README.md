# UniXNU-sdm710-common

Shared Qualcomm **SDM710** (Snapdragon 710 / Kryo 360) support for UniXNU device ports.

This repository is the SoC layer of the format 2 device model: it holds the hardware data and
platform facts that several SDM710 boards have in common, so each device repository only states
its own wiring.

| Path | Contents |
| --- | --- |
| `soc.toml` | SoC profile: identity, hardware source, shared component selection |
| `dts/sdm710.dtsi` | Shared GIC-500, PSCI and counter description; boards include it |
| `configs/sdm710.config` | Shared kernel configuration (currently empty on purpose) |
| `patches/xnu/series` | Shared XNU patch series (currently empty) |

## Scope

The profile describes what has been **measured on hardware**, not what the vendor tree claims:

- GIC-500 (GICv3): GICD `0x17a00000`/`0x10000`, GICR `0x17a60000`/`0x100000`, stride `0x20000`.
- Architectural counter `19200000` Hz (Qualcomm XO).
- PSCI through SMC; eight cores at Affinity1 `0x000`–`0x700`, boot CPU `0x000`.
- The EL1 virtual timer is **GIC PPI 3 / INTID 19**, not the architectural PPI 27 that Apple
  silicon uses. This is the single most board-relevant SoC fact for a Darwin port.

## Use from a device repository

```toml
# feeds.toml
[[source]]
name = "sdm710"
kind = "soc"
git = "https://github.com/57UU/UniXNU-sdm710-common"
commit = "<reviewed commit>"
```

```toml
# board.toml
[board]
soc_source = "sdm710"

[device_tree]
source = "device:dts/<board>.dts"
include_dirs = ["soc:dts"]

[kernel]
config_fragments = ["arch:configs/common.config", "soc:configs/sdm710.config", "os:configs/ipados.config"]
patch_series = ["arch:patches/xnu/series", "soc:patches/xnu/series", "os:patches/xnu/series", "device:patches/xnu/series"]
```

The board DTS includes the DTSI and adds its memory node, its CPU children and its exported
peripherals:

```dts
/dts-v1/;
#include "sdm710.dtsi"
```

## Verified devices

| Device | Repository | State |
| --- | --- | --- |
| Xiaomi Mi 8 SE (sirius) | [UniXNU-sirius](https://github.com/57UU/UniXNU-sirius) | source kernel reaches IOKit; platform expert and boot-progress console on hardware |

## Documentation

- [SoC support](https://unixnu.com/porting/soc-support)
- [Device tree](https://unixnu.com/porting/device-tree)
- [Device repository](https://unixnu.com/porting/device-repository)

## License

BSD-2-Clause-Patent, as UniXNU.
