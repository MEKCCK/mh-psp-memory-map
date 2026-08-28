# Monster Hunter PSP Speicherkarte (MHF · MHF2 · MHFU · MHP3RD)

Datenbank mit virtuellen PSP-Speicheradressen für Monster Hunter auf PSP (MHF/MHF2/MHFU/MHP3RD, inkl. HD): Monster-Strukturen, Queststatus, Spieler, Items, Rendering/Kamera und die Mod-Loader-Pipeline.

> Alle Adressen sind **virtuelle PSP-Adressen** (identisch auf echter Hardware und in PPSSPP). Externe Tools, die den Prozessspeicher des Emulators lesen, brauchen die Host-Basisadresse; Konsolen-Cheat-Datenbanken nutzen oft „RAM-Offsets“ (absolut = Offset + `0x08800000`).

## Inhalt

| Datei | Beschreibung |
|---|---|
| [Speicherkarte](docs/en/memory-map.md) | Komplette Tabelle (4 Spiele) + Adress-Erklärung + Quellen-Tags |
| [Grabungs-Guide](docs/en/digging-guide.md) | Wie findet man NEUE Adressen (5 Wege + Checkliste) |
| [Mitwirken](docs/en/contributing.md) | So reicht man PRs ein / liefert öffentliche Adressen |
| [LANGUAGES.md](LANGUAGES.md) | Übersetzungsstatus & Mithelfen |

## Schnellnutzung

| Szenario | Zu verwendende Adresse |
|---|---|
| Code in PPSSPP / Editor | absolute Adresse, z. B. `0x09B4C244` |
| „Offset“ aus Konsolen-Cheats | erst `0x08800000` addieren |
| Externe Tools (CE/Python) | zusätzlich Host-Basis (`Memory::base`) |

## Quellen und Danksagungen

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## Verwandte Projekte
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## Lizenz
GPL-3.0. Die Daten gehören den Ursprungsprojekten (siehe oben); dieses Repo organisiert, kommentiert und indexiert nur.

---
*Deutsch* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
