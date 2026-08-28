# Mapa de memória Monster Hunter PSP (MHF · MHF2 · MHFU · MHP3RD)

Banco de endereços de memória virtual PSP dos Monster Hunter de PSP (MHF/MHF2/MHFU/MHP3RD, incl. HD): estruturas de monstros, estado de missão, jogador, itens, render/câmera e o pipeline do loader de mods.

> Todos os endereços são **endereços virtuais PSP** (idênticos no hardware real e no PPSSPP). Ferramentas externas que leem a memória do processo do emulador precisam da base do host; bases de cheat de console costumam usar "offsets de RAM" (absoluto = offset + `0x08800000`).

## Conteúdo

| Arquivo | Descrição |
|---|---|
| [Mapa de memória](docs/en/memory-map.md) | Tabela completa (4 jogos) + explicação de endereços + tags de origem |
| [Guia de extração](docs/en/digging-guide.md) | Como encontrar endereços NOVOS (5 rotas + checklist) |
| [Contribuir](docs/en/contributing.md) | Como enviar PR / fornecer endereços públicos |
| [LANGUAGES.md](LANGUAGES.md) | Status de tradução e como ajudar |

## Uso rápido

| Cenário | Endereço a usar |
|---|---|
| Código no PPSSPP / editor | endereço absoluto, ex. `0x09B4C244` |
| "Offset" de cheat de console | somar `0x08800000` primeiro |
| Ferramentas externas (CE/Python) | precisa também da base do host (`Memory::base`) |

## Fontes e créditos

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## Projetos relacionados
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## Licença
GPL-3.0. Os dados pertencem aos projetos originais (acima); este repo apenas organiza, anota e indexa.

---
*Português (BR)* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
