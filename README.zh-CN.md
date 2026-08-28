# 怪物猎人 PSP 系列内存标记表（MHF · MHF2 · MHFU · MHP3RD）

PSP 怪物猎人系列（MHF/MHF2/MHFU/MHP3RD，含 HD）的 PSP 虚拟内存地址资料库：怪物数据结构、任务状态、玩家、物品、渲染/相机、模组加载管线。

> 所有地址均为 **PSP 虚拟地址**（实体机与 PPSSPP 一致）。外部工具读模拟器进程内存需另加宿主基址；真机 cheat 库常用 "RAM 偏移"（绝对地址 = 偏移 + `0x08800000`）。

## 内容

| 文件 | 说明 |
|---|---|
| [内存表](docs/zh-CN/memory-map.md) | 完整表（四游戏）＋地址性质详解＋逐条来源标注 |
| [挖掘指南](docs/zh-CN/digging-guide.md) | 如何发现新地址（5 条路线＋验证清单） |
| [贡献指南](docs/zh-CN/contributing.md) | 如何提 PR / 提供公开内存地址 |
| [LANGUAGES.md](LANGUAGES.md) | 翻译状态与如何参与翻译 |

## 快速用法

| 场景 | 地址用法 |
|---|---|
| PPSSPP 内置代码 / 内存编辑器 | 绝对地址，如 `0x09B4C244`（箱子第一格） |
| 真机 cheat 库的"偏移" | 先加 `0x08800000` 再用 |
| 外部工具（CE / Python） | 还需模拟器宿主基址（`Memory::base`） |

## 来源与致谢

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload), [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman), [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar), [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers), [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator), [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## 关联项目
- - In-emulator MH HP overlay (PPSSPP build): https://github.com/MEKCCK/ppsspp (`UI/MHOverlay.cpp`)


## License
GPL-3.0。数据归属各原始项目（见上），本仓库仅做整理、标注与索引。

---
*简体中文* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
