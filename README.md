# EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision

> An extended, multi-language fork of [EV3-AdvMotorControllers-Block](https://github.com/a10036gt/EV3-AdvMotorControllers-Block) — adding precision control blocks to make your EV3 robot move faster and more accurately.

![EV3 Advanced Motor Controllers blocks overview](https://i2.wp.com/www.ofdl.nctu.me/wp-content/uploads/2020/07/EV3_AdvMotorCtrl.jpg?w=592&ssl=1)

## What is this?

WRO robots need to be **fast, accurate, and consistent**. The built-in EV3-G blocks are far from sufficient for competitive use. This project extends the original `EV3-AdvMotorControllers-Block` with additional precision control blocks, multi-language UI support, and color sensor utilities — all implemented at a low level for maximum speed and accuracy.

![EV3-G block palette showing OFDL motor controller blocks](https://www.tecnonews.info/files/3-10397-fotoArticulo/Captura%20de%20pantalla%202018-11-24%20a%20las%201.13.30.png)

---

## Blocks Included

### Original blocks (by [OFDL / a10036gt](https://github.com/a10036gt/EV3-AdvMotorControllers-Block))

| Block | Description |
| ----- | ----------- |
| **PD Controller** | Line-following PD controller using two sensors |
| **Synchronous Movement Controller** | Keeps two motors in sync during straight travel |
| **Acceleration-Deceleration Controller** | Smooth speed ramp-up and ramp-down by encoder |

### Added in this fork

| Block | Description |
| ----- | ----------- |
| **Color Sensor Speed Controller** | Calculates motor speed from two color sensor values (Linear / Quadratic / Cubic / Sqrt / Step / Smooth modes) with optional smoothing |
| **CS Calibration** | Normalizes raw color sensor values to 0–100 using user-defined white/black calibration points |
| **Advanced Motor Control** | Raw and standard advanced motor power output per port |
| **Advanced Encoder** | Reset, read angle, and read change per motor port |

---

## Usage Guides

Detailed documentation for each block (primary language: English):

| Block | en-US | en-GB | ja | de | fr | es | it | nl | pt | ru | ko | zh-Hans | da | nb-NO | sv |
| ----- | ----- | ----- | -- | -- | -- | -- | -- | -- | -- | -- | -- | ------- | -- | ----- | -- |
| **PD Controller** | [EN](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/pd-controller.md) | [en-GB](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/en-GB/pd-controller.md) | [ja](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ja/pd-controller.md) | [de](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/de/pd-controller.md) | [fr](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/fr/pd-controller.md) | [es](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/es/pd-controller.md) | [it](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/it/pd-controller.md) | [nl](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nl/pd-controller.md) | [pt](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/pt/pd-controller.md) | [ru](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ru/pd-controller.md) | [ko](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ko/pd-controller.md) | [zh-Hans](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/zh-Hans/pd-controller.md) | [da](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/da/pd-controller.md) | [nb-NO](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nb-NO/pd-controller.md) | [sv](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/sv/pd-controller.md) |
| **Acceleration Controller** | [EN](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/acceleration-controller.md) | [en-GB](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/en-GB/acceleration-controller.md) | [ja](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ja/acceleration-controller.md) | [de](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/de/acceleration-controller.md) | [fr](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/fr/acceleration-controller.md) | [es](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/es/acceleration-controller.md) | [it](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/it/acceleration-controller.md) | [nl](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nl/acceleration-controller.md) | [pt](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/pt/acceleration-controller.md) | [ru](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ru/acceleration-controller.md) | [ko](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ko/acceleration-controller.md) | [zh-Hans](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/zh-Hans/acceleration-controller.md) | [da](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/da/acceleration-controller.md) | [nb-NO](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nb-NO/acceleration-controller.md) | [sv](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/sv/acceleration-controller.md) |
| **MoveSync Controller** | [EN](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/sync-controller.md) | [en-GB](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/en-GB/sync-controller.md) | [ja](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ja/sync-controller.md) | [de](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/de/sync-controller.md) | [fr](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/fr/sync-controller.md) | [es](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/es/sync-controller.md) | [it](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/it/sync-controller.md) | [nl](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nl/sync-controller.md) | [pt](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/pt/sync-controller.md) | [ru](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ru/sync-controller.md) | [ko](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ko/sync-controller.md) | [zh-Hans](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/zh-Hans/sync-controller.md) | [da](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/da/sync-controller.md) | [nb-NO](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nb-NO/sync-controller.md) | [sv](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/sv/sync-controller.md) |
| **ColorSpeed Controller** | [EN](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/color-sensor-speed-controller.md) | [en-GB](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/en-GB/color-sensor-speed-controller.md) | [ja](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ja/color-sensor-speed-controller.md) | [de](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/de/color-sensor-speed-controller.md) | [fr](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/fr/color-sensor-speed-controller.md) | [es](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/es/color-sensor-speed-controller.md) | [it](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/it/color-sensor-speed-controller.md) | [nl](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nl/color-sensor-speed-controller.md) | [pt](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/pt/color-sensor-speed-controller.md) | [ru](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ru/color-sensor-speed-controller.md) | [ko](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/ko/color-sensor-speed-controller.md) | [zh-Hans](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/zh-Hans/color-sensor-speed-controller.md) | [da](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/da/color-sensor-speed-controller.md) | [nb-NO](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/nb-NO/color-sensor-speed-controller.md) | [sv](https://github.com/Craft-Core/EV3-AdvMotorControllers-Block-Unofficial-Multi-language-Precision/blob/master/docs/sv/color-sensor-speed-controller.md) |

---

## Multi-Language Support

All blocks are fully localized for 15 languages:

| Language | Folder |
| -------- | ------ |
| English (US) | en-US |
| English (GB) | en-GB |
| Japanese (日本語) | ja |
| German (Deutsch) | de |
| French (Français) | fr |
| Spanish (Español) | es |
| Italian (Italiano) | it |
| Dutch (Nederlands) | nl |
| Portuguese (Português) | pt |
| Russian (Русский) | ru |
| Korean (한국어) | ko |
| Simplified Chinese (简体中文) | zh-Hans |
| Danish (Dansk) | da |
| Norwegian (Norsk) | nb-NO |
| Swedish (Svenska) | sv |

---

## Credits

- Original block: **OFDL / a10036gt** — <https://github.com/a10036gt/EV3-AdvMotorControllers-Block>
- Original user guide (繁體中文): <https://ofdl.tw/ev3-hack/adv-motor-controllers-block/>
- Original user guide (English): <https://ofdl.tw/en/ev3-hacking/advanced-motor-controllers-block/>
