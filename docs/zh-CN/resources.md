# 资源索引 —— 怪物猎人 PSP 研究项目清单

与 PSP 怪物猎人内存研究、逆向、作弊、模组、存档工具相关的公开项目索引。
用途：交叉验证地址、找结构、挖新地址。**P0** = 直接填补 [memory-map.md](zh-CN/memory-map.md) 的空白。

> 想补充资源，或从这些项目里提取地址？见 [contributing.md](zh-CN/contributing.md)
> 与 [digging-guide.md](zh-CN/digging-guide.md)。

## 一、内存地址/数据结构（最高价值，6）

1. **James-028/mhfu_transmog**（含 Exceen fork）— https://github.com/James-028/mhfu_transmog
   - `FINDINGS.md`：MHFU 静态数据表完整逆向成果
   - 武器数据表 `0x089574E8`（24 字节/条目，约 1149 条）
   - 防具指针表 `0x08975970` + 5 个装备槽表（HEAD/CHEST/ARMS/WAIST/LEGS，40 字节/条目）
   - 模型查找表 `0x0893E7F0`、制作/升级表 `0x08938D1A`、武器索引表 `0x089A1878`
   - FUComplete TABLE_A/B/E；完整条目结构解析 + CWCheat 格式 + PPSSPP 限制说明
2. **Exceen/mhp3rd_transmog** — https://github.com/Exceen/mhp3rd_transmog
   - MHP3RD 幻化：`transmog.py`（667 行）+ `build_data.py`（493 行）
   - 含 MHP3RD 武器/防具数据表地址与结构 —— 填补本仓库 MHP3RD 静态数据表空白 **P0**
3. **SiD3W4y/mhfu-re** — https://github.com/SiD3W4y/mhfu-re
   - MHFU 逆向工程：`doc/objects.txt`（结构体）、`doc/functions.txt`（函数表）、
     `doc/runtime.txt`、`doc/ovl.txt`；`tools/ppsspp-py/`（WebSocket 调试库）、
     `tools/binja-mhfu/` + `tools/ghidra_scripts/`（NID 解析）、`tools/comete/`（trace 分析）
   - 实体/玩家/怪物 vtable 追踪脚本 **P0**
4. **9r3i/mhp3-cheats** — https://github.com/9r3i/mhp3-cheats
   - MHP3 + MHFU 大量 CWCheat（可反解地址）
   - MHP3：`NPJB40001.this.ini`（27KB HD）、`cheats.md`（全武器会心等）、`mhp3.js`（护石生成器，含护石数据）、`name.txt`
   - MHFU（`mhfu/`）：`ULUS10391.ini` 系列、分类文件、`mhfu.monweak.txt`（弱点）、`mhfu.key.quest.txt`、`mhfu.armor.skill.txt`
5. **RetroHero/MHPSP-Cheats** — https://github.com/RetroHero/MHPSP-Cheats
   - Cheat Engine `.CT` 表（含 *MHFU COMPLETE v1.4.0*）；AoB 扫描定位 PSP 内存的 Lua 脚本
6. **tclamb/mhp2g-decomp** — https://github.com/tclamb/mhp2g-decomp
   - MHP2G/MHFU 反编译（进行中）：函数名、结构体、数据段地址 **P0**

## 二、Cheat 数据库/代码集合（高价值，4）

7. **Saramagrean/CWCheat-Database-Plus-** — https://github.com/Saramagrean/CWCheat-Database-Plus-
   - 大型 CWCheat 库，含 MHP3RD (ULJM05800) 已测名字编辑（PR #62 +118 行）
8. **gamehacking.org**（非 GitHub）— https://gamehacking.org
   - 覆盖 MHF (US v1.01)、MHF2、MHFU 等版本的 CWCheat 库
9. **etherealgames.com**（非 GitHub）— https://etherealgames.com
   - 各版本 CWCheat：金钱/农场点/公会点/物品/HP 地址
10. **wikiwiki.jp/cwcheat/**（日文维基，非 GitHub）— https://wikiwiki.jp/cwcheat/
    - MHP2G 页面：武器种类地址表等

## 三、Mod/补丁项目（中高价值，含 hook 地址，7）

11. **FUComplete/Patch** — https://github.com/FUComplete/Patch
    - FUComplete v1.4.0（MHP2G 增强 mod）—— 大量 MIPS 补丁/hook，可反解函数与数据结构
12. **FUComplete/FUCTool** — https://github.com/FUComplete/FUCTool
    - 管理工具（Python，GPL-3.0）：Patcher/Configuration/File Replacer/Custom Quests；文件替换（nativePSP）机制
13. **SilverJolteon/FreedomDX** — https://github.com/SilverJolteon/FreedomDX
    - MHF1（MHP1 JPN / MHF1 USA & EUR）全合一补丁；每功能一个 MIPS 补丁 → MHF1 函数地址 **P0**
14. **Immortalcripple/Monster-Hunter-Freedom-DX** — https://github.com/Immortalcripple/Monster-Hunter-Freedom-DX
    - 另一 MHF DX 项目；`build.py` 含 ISO 补丁逻辑（与 #13 重叠/互补）
15. **Kethen/mhp3_patcher**（原 codestation/mhp3）— https://github.com/Kethen/mhp3_patcher
    - MHP3RD 英文补丁加载器（PSP homebrew，PPSSPP + CFW 可用）；loader 钩子与文件替换机制
16. **darkpigyummy/GePatch_mh** — https://github.com/darkpigyummy/GePatch_mh
    - MHP3RD 高清化插件（Adrenaline/PSVita），GePatch 特化 → 渲染相关 hook
17. **nuzcraft/FreedomUniteColorRandomizer** — https://github.com/nuzcraft/FreedomUniteColorRandomizer
    - MHFU 怪物颜色随机化 → 怪物纹理数据结构

## 四、存档/加密/数据工具（中价值，5）

18. **svanheulen/mhef** — https://github.com/svanheulen/mhef
    - MH 加密库；`mhef/psp.py` 含 PSP 全系列存档加解密、Disc ID 常量、盐、算法
19. **svanheulen/mhsc** — https://github.com/svanheulen/mhsc
    - 存档转换器（PPSSPP 插件）—— 存档结构知识
20. **hzhreal/save-decrypters** — https://github.com/hzhreal/save-decrypters
    - `monsterhunter-psp-decrypter/main.c`：MHP2G/MHFU/MHP3RD 存档解密、hash salt
21. **viciousShadow/MH-PSP-DLC**（KouyaOsamu fork）— https://github.com/viciousShadow/MH-PSP-DLC
    - PSP MH 全系列 DLC 档（MHP/MHF1、MHP2/MHF2、MHP2G/MHFU、MHP3rd）—— DLC 任务数据结构
22. **amaillo/MH-Pointers-Tool** — https://github.com/amaillo/MH-Pointers-Tool
    - 文件内字符串指针表工具（翻译用，MIT）；MH1~MHP3RD/MHTri —— 文件指针表结构参考

## 五、游戏数据库/参考数据（低中价值，交叉引用，3）

23. **Kolyn090/mhfu-db**（+ asset/image db）— https://github.com/Kolyn090/mhfu-db
    - MHFU 完整数据库 JSON（武器/防具/物品/技能/任务/地点）
24. **ar-tur-33/MH-Portable-3rd-Data** — https://github.com/ar-tur-33/MH-Portable-3rd-Data
    - MHP3rd 数据（CSV，含地点）
25. **gaugustini/MHFUDatabase** — https://github.com/gaugustini/MHFUDatabase
    - MHFU 数据库安卓应用（数据源自 #23），SQLite 结构

## 六、HD 纹理/UI 包（低价值，仅纹理替换地址，3）

26. **HolographicWings/Monster-Hunter-Portable-3rd-HD-Pack-Enhanced** — https://github.com/HolographicWings/Monster-Hunter-Portable-3rd-HD-Pack-Enhanced
    - MHP3RD HD 纹理增强包（ULJM05800 + NPJB40001），README "missing addresses" 值哈希映射
27. **David-vz/MonsterHunterPortable3rdHDRemake**（StormieVN / rivenirvana fork）— https://github.com/David-vz/MonsterHunterPortable3rdHDRemake
    - MHP3RD HD 纹理重制包 —— 替换教程与哈希映射
28. **alien038/MHFU-HD** — https://github.com/alien038/MHFU-HD
    - MHFU HD 纹理/UI 包 —— UI 纹理替换知识

## 七、其他（低价值，2）

29. **YNDZX/CheatGuildCardMhp3rd** — https://github.com/YNDZX/CheatGuildCardMhp3rd
    - MHP3RD 公会卡 cheat（GPL-2.0，仅 HD NPJB40001）
30. **GReinoso96/XXModding (Wiki)** — https://github.com/GReinoso96/XXModding/wiki/Quality-of-Life-Codes
    - QoL 代码集合，含 MHF2 (EUR ULES00851) 的 True Raw/Ele/Status（多为 PS2/3DS）

---

## 汇总

| 类别 | 数量 | P0 代表 |
|---|---|---|
| 内存地址/数据结构 | 6 | mhfu_transmog、mhp3rd_transmog、mhfu-re、mhp3-cheats、mhp2g-decomp |
| Cheat 数据库 | 4 | — |
| Mod/补丁 | 7 | FreedomDX（补 MHF1 空白） |
| 存档/加密/工具 | 5 | — |
| 游戏数据库 | 3 | — |
| HD 纹理包 | 3 | — |
| 其他 | 2 | — |

## 怎么用这份清单

- **交叉验证**：入库前至少两个来源互证（如 mhp3-cheats × etherealgames × mhp3rd_transmog）。
- **挖掘**：P0 仓库（transmog / FUComplete / decomp）结构质量最高，按 digging-guide.md 路线四（代码考古）入手。
- **贡献**：验证后按 contributing.md 提交，务必附来源仓库链接。