# モンスターハンター PSP シリーズ メモリマップ（MHF・MHF2・MHFU・MHP3RD）

PSP モンハンシリーズ（MHF/MHF2/MHFU/MHP3RD、HD 含む）の PSP 仮想メモリ住所録：モンスター構造体、クエスト状態、プレイヤー、アイテム、描画/カメラ、mod ローダー管線。

> すべてのアドレスは **PSP 仮想アドレス**（実機と PPSSPP で同一）。エミュのプロセスメモリを外部ツールで読む場合は別途ホストベースが必要。実機 cheat データベースは「RAM オフセット」表記が多い（絶対アドレス = オフセット + `0x08800000`）。

## 内容

| ファイル | 説明 |
|---|---|
| [メモリマップ](docs/en/memory-map.md) | 全ゲーム分の表＋アドレス解説＋出所タグ |
| [発掘ガイド](docs/en/digging-guide.md) | 新しいアドレスの見つけ方（5 ルート＋検証リスト） |
| [コントリビュート](docs/en/contributing.md) | PR の出し方／公開アドレスの提供方法 |
| [LANGUAGES.md](LANGUAGES.md) | 翻訳状況と協力方法 |

## クイック使用法

| 場面 | 使用するアドレス |
|---|---|
| PPSSPP 内蔵コード / メモリエディタ | 絶対アドレス（例 `0x09B4C244`） |
| 実機 cheat DB の「オフセット」 | まず `0x08800000` を加算 |
| 外部ツール（CE / Python） | さらにホストベース（`Memory::base`）が必要 |

## 出典と謝辞

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## 関連プロジェクト
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## ライセンス
GPL-3.0。データの帰属は上記各オリジナルプロジェクト。本リポジトリは整理・注記・索引のみ。

---
*日本語* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
