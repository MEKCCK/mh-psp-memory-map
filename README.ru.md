# Карта памяти Monster Hunter PSP (MHF · MHF2 · MHFU · MHP3RD)

База виртуальных адресов памяти PSP для Monster Hunter на PSP (MHF/MHF2/MHFU/MHP3RD, включая HD): структуры монстров, состояние квеста, игрок, предметы, рендер/камера и конвейер загрузчика модов.

> Все адреса — **виртуальные адреса PSP** (одинаковы на реальном железе и в PPSSPP). Внешним инструментам, читающим память процесса эмулятора, нужен адрес базы хоста; читерские базы для консоли часто используют «смещения RAM» (абсолютный = смещение + `0x08800000`).

## Содержание

| Файл | Описание |
|---|---|
| [Карта памяти](docs/en/memory-map.md) | Полная таблица (4 игры) + разбор адресов + метки источников |
| [Гайд по поиску](docs/en/digging-guide.md) | Как находить НОВЫЕ адреса (5 путей + чек-лист) |
| [Участие](docs/en/contributing.md) | Как отправлять PR / предоставлять публичные адреса |
| [LANGUAGES.md](LANGUAGES.md) | Статус перевода и как помочь |

## Быстрое использование

| Сценарий | Какой адрес |
|---|---|
| Код внутри PPSSPP / редактор | абсолютный, напр. `0x09B4C244` |
| «Смещение» из читов консоли | сначала прибавить `0x08800000` |
| Внешние инструменты (CE/Python) | плюс база хоста (`Memory::base`) |

## Источники и благодарности

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## Связанные проекты
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## Лицензия
GPL-3.0. Данные принадлежат исходным проектам (выше); этот репозиторий только систематизирует, комментирует и индексирует.

---
*Русский* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
