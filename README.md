# 怪物猎人 PSP 系列内存标记表（MH PSP Memory Map）

覆盖 **MHF / MHF2 / MHFU / MHP3RD**（含 HD 版）的 PSP 虚拟内存地址资料库：
怪物数据结构、任务状态、玩家、物品、渲染/相机、模组加载管线等。

> 所有地址均为 **PSP 虚拟地址**（实体机与 PPSSPP 一致）。外部工具读模拟器进程内存时
> 需另加宿主 base；真机 cheat 库常用 "RAM 偏移"（绝对地址 = 偏移 + `0x08800000`）。
> 详见 [docs/memory-map.md](docs/memory-map.md) 第 0 节。

## 仓库内容

| 文件 | 内容 |
|---|---|
| [docs/memory-map.md](docs/memory-map.md) | 完整内存表（四游戏）＋地址性质详解＋来源标注 |
| [docs/digging-guide.md](docs/digging-guide.md) | **如何挖掘新地址**（方法论，含工具与实战思路） |
| [CONTRIBUTING.md](CONTRIBUTING.md) | **如何提交 PR / 提供公开内存地址** |

## 快速用法

| 场景 | 地址用法 |
|---|---|
| PPSSPP 内置代码 / 内存编辑器 | 绝对地址，如 `0x09B4C244`（箱子第一格） |
| 真机 cheat 数据库给出"偏移" | 先 +`0x08800000` 再写 |
| 外部工具（CE / Python） | 还需要模拟器宿主基址（PPSSPP 的 `Memory::base`） |

## 来源与致谢

- 怪物指针/结构偏移：Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator)（Python）
- P3 扩展地址：Kurogami2134 的
  [mhp3reload](https://github.com/Kurogami2134/mhp3reload) /
  [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) /
  [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) /
  [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) /
  [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) /
  [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)
- 每个条目的具体标记得见 memory-map.md 的"来源标注"

## 关联项目

- **PPSSPP 内置 overlay**（MH HP Overlay 移植，含本表数据的实际使用）：
  https://github.com/MEKCCK/ppsspp （`UI/MHOverlay.cpp`、`docs/` 曾存放本表，已独立至此）

## License

GPL-3.0。表内容归属各原始项目（见上），本仓库仅做整理、标注与索引。