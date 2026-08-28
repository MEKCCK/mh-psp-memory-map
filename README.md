# Monster Hunter PSP Series Memory Map (MHF · MHF2 · MHFU · MHP3RD)

PSP virtual memory address database for Monster Hunter PSP titles (MHF/MHF2/MHFU/MHP3RD incl. HD): monster data structures, quest state, player data, items, render/camera, mod loader pipeline.

> All addresses are **PSP virtual addresses** (identical on real hardware and PPSSPP). External tools reading emulator process memory need the host base address; real-device cheat DBs often use "RAM offsets" (absolute = offset + `0x08800000`).

## Contents

| File | Description |
|---|---|
| [Memory Map](docs/en/memory-map.md) | Full table (4 games) + address-nature deep dive + per-entry source tags |
| [Digging Guide](docs/en/digging-guide.md) | How to discover NEW addresses (5 routes + verification checklist) |
| [Contributing](docs/en/contributing.md) | How to submit PRs / provide public addresses |
| [Resources](docs/en/resources.md) | Curated index of 30 public MH PSP research projects (addresses/cheats/mods/save tools, P0 picks) |
| [LANGUAGES.md](LANGUAGES.md) | Translation status & how to help translate |

## Quick usage

| Scenario | Address to use |
|---|---|
| In-PPSSPP code / memory editor | absolute, e.g. `0x09B4C244` (item box slot 1) |
| Real-device cheat DB "offset" | add `0x08800000` first |
| External tools (CE/Python) | also need emulator host base (`Memory::base`) |

## Sources & credits

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload), [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman), [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar), [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers), [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator), [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## Related projects
- - In-emulator MH HP overlay (PPSSPP build): https://github.com/MEKCCK/ppsspp (`UI/MHOverlay.cpp`)


## License
GPL-3.0. Data belongs to the original projects (above); this repo only organizes, annotates and indexes it.

---
*English* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
