# Carte mémoire Monster Hunter PSP (MHF · MHF2 · MHFU · MHP3RD)

Base d'adresses mémoire virtuelles PSP pour les Monster Hunter PSP (MHF/MHF2/MHFU/MHP3RD, incl. HD) : structures de monstres, état de quête, joueur, objets, rendu/caméra et pipeline du chargeur de mods.

> Toutes les adresses sont des **adresses virtuelles PSP** (identiques sur console réelle et PPSSPP). Les outils externes lisant la mémoire du processus de l'émulateur doivent connaître la base hôte ; les bases de cheats console utilisent souvent des « offsets RAM » (absolue = offset + `0x08800000`).

## Contenu

| Fichier | Description |
|---|---|
| [Carte mémoire](docs/en/memory-map.md) | Tableau complet (4 jeux) + explication des adresses + tags de source |
| [Guide d'extraction](docs/en/digging-guide.md) | Comment trouver de NOUVELLES adresses (5 routes + checklist) |
| [Contribuer](docs/en/contributing.md) | Comment soumettre des PR / fournir des adresses publiques |
| [LANGUAGES.md](LANGUAGES.md) | État des traductions et comment aider |

## Utilisation rapide

| Situation | Adresse à utiliser |
|---|---|
| Code dans PPSSPP / éditeur | adresse absolue, ex. `0x09B4C244` |
| « Offset » d'une base console | ajouter `0x08800000` d'abord |
| Outils externes (CE/Python) | + base hôte (`Memory::base`) |

## Sources et crédits

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## Projets liés
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## Licence
GPL-3.0. Les données appartiennent aux projets d'origine (ci-dessus) ; ce dépôt ne fait qu'organiser, annoter et indexer.

---
*Français* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
