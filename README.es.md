# Mapa de memoria de Monster Hunter PSP (MHF · MHF2 · MHFU · MHP3RD)

Base de datos de direcciones de memoria virtual PSP para Monster Hunter de PSP (MHF/MHF2/MHFU/MHP3RD, incl. HD): estructuras de monstruos, estado de misión, jugador, objetos, renderizado/cámara y el pipeline del cargador de mods.

> Todas las direcciones son **direcciones virtuales de PSP** (idénticas en hardware real y PPSSPP). Las herramientas externas que leen la memoria del proceso del emulador necesitan la base del host; las bases de cheat de consola suelen usar "offsets de RAM" (absoluta = offset + `0x08800000`).

## Contenido

| Archivo | Descripción |
|---|---|
| [Mapa de memoria](docs/en/memory-map.md) | Tabla completa (4 juegos) + explicación de direcciones + etiquetas de origen |
| [Guía de excavación](docs/en/digging-guide.md) | Cómo encontrar direcciones NUEVAS (5 rutas + checklist) |
| [Contribuir](docs/en/contributing.md) | Cómo enviar PR / proporcionar direcciones públicas |
| [LANGUAGES.md](LANGUAGES.md) | Estado de traducción y cómo ayudar |

## Uso rápido

| Escenario | Dirección a usar |
|---|---|
| Código dentro de PPSSPP / editor | dirección absoluta, ej. `0x09B4C244` |
| "Offset" de cheat de consola | añadir primero `0x08800000` |
| Herramientas externas (CE/Python) | además la base del host (`Memory::base`) |

## Fuentes y créditos

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## Proyectos relacionados
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## Licencia
GPL-3.0. Los datos pertenecen a los proyectos originales (arriba); este repo solo organiza, anota e indexa.

---
*Español* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
