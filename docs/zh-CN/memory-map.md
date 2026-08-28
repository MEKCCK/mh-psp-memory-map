# 怪物猎人 PSP 系列内存标记表（MHF · MHF2 · MHFU · MHP3RD）

所有地址均为 **PSP 虚拟地址**（PPSSPP/overlay 直接读写的地址体系）。

## 来源标注（Source Tags）

| 标记 | 来源 | 说明 |
|---|---|---|
| **[orig]** | Alexander-Lancellott/**MH-HP-Overlay-For-PSP-Emulator**（Python 原版 overlay 项目） | 指针表与结构偏移的真正来源；本项目仅移植/集成，无原创 |
| **[load]** | Kurogami2134/mhp3reload loader 汇编（src/*.asm） | 模组加载器钩子 |
| **[modman]** | Kurogami2134/p3rdml_modman | 模组管理器部署配置 |
| **[hpbar]** | Kurogami2134/mhp3rd_monster_hp_bar | 怪物血条 mod |
| **[dmg]** | Kurogami2134/mhp3rd_dmg_numbers | 伤害数字 mod |
| **[sharp]** | Kurogami2134/p3rd_sharpness_indicator | 斩味指示 mod |
| **[item]** | Kurogami2134/p3rd_item_sets | 道具套装 mod |
| **[transmog]** | James-028/[mhfu_transmog](https://github.com/James-028/mhfu_transmog) `FINDINGS.md` | MHFU 静态数据表 |
| **[transmog3]** | Exceen/[mhp3rd_transmog](https://github.com/Exceen/mhp3rd_transmog) `build_data.py` | MHP3RD 装备/武器表（ULJM05800） |
| **[re]** | SiD3W4y/[mhfu-re](https://github.com/SiD3W4y/mhfu-re) `doc/*.txt` | MHFU 运行时结构/函数/OVL |
| **[cheats]** | 9r3i/[mhp3-cheats](https://github.com/9r3i/mhp3-cheats) | MHP3/MHFU cheat 码、护石技能表 |
| **[mhef]** | svanheulen/[mhef](https://github.com/svanheulen/mhef) `mhef/psp.py` | PSP MH 存档加密 |
| **[val]** | 本项目（MEKCCK/ppsspp 内置 overlay） | 仅做**交叉验证/集成**，不产生新地址 |

> 声明：本表所有内存知识均来自上述两个原作者的**公开项目**，本项目（PPSSPP 内置
> overlay）只是把这些已公开的数据**移植进模拟器并交叉核对**，没有一条地址是本项目
> 原创发现。伤害数字、血条等能力的归属均为 Kurogami2134 的 mhp3rd_dmg_numbers /
> mhp3rd_monster_hp_bar。

---

## 0. 地址性质与偏移说明（重要）

- 本表全部为 **PSP 虚拟地址**。PSP 内存映射由硬件/系统固定（用户 RAM 基址
  `0x08800000`），**实体机与 PPSSPP 一致** —— mhp3reload 等 mod 的 MIPS 补丁
  就是在真机上用这些地址打进去的。
- **"偏移问题"只存在于"外部工具读模拟器进程内存"**：PPSSPP 把 PSP 内存映射到
  宿主进程的任意地址，外部工具（原版 Python overlay、Cheat Engine 等）必须再加
  一个 `base_address` 换算（原版 overlay 用 `SendMessageW(0xB118)` 拿它）。
- **本项目（内置 overlay）直接以 PSP 虚拟地址读写 `Memory::ReadUnchecked_*`**，
  PPSSPP 内部完成映射，**不存在任何偏移**。
- 需要按"游戏版本"区分的：
  - 原版 `ULJM05800` vs HD `NPJB40001`：EBOOT 布局不同 → 地址不同（本表已双列，
    "未提供"= 来源 mod 只有该版本实现）。
  - 同一游戏同版本：实体机 / PPSSPP 地址相同。

### 地址的两种写法（重要）

很多真机作弊工具/数据库按 **"RAM 偏移"** 记地址（相对 `0x08800000`），例如
"箱子第一格 = 0x0134C244"。写入模拟器时**必须加基址**：

```
绝对地址 = 偏移 + 0x08800000
0x09B4C244 = 0x0134C244 + 0x08800000   （ITeM_BOX，两写法互证 ✓）
```

- 真机工具能直接用偏移，因为它们内部自己加基址；
- PPSSPP 内存编辑器 / 内置代码一律用**绝对地址**（`0x09xxxxxx`）。
- 直接用偏移当绝对地址写 `0x0134xxxx` = 未映射区，无效。

| 功能 | 绝对地址 | RAM 偏移写法 |
|---|---|---|
| 箱子 ITEM_BOX | `0x09B4C244` | 0x0134C244 |
| 口袋 1 ITEM_POUCH1 | `0x09BA8D4A` | 0x013A8D4A |
| 口袋 2 ITEM_POUCH2 | `0x09B4D9B4` | 0x012CD9B4 |

### PSP 内存映射详解（为什么是 0x08800000）

PSP 的 CPU（Allegrex，MIPS R4000 系）虚拟地址空间是硬件定死的，游戏和系统共用：

```
地址范围             内容
─────────────────────────────────────────────────────────────
0x00000000 – 0x03FFFFFF  保留 / 用户区（游戏一般用不到）
0x04000000 – 0x041FFFFF  VRAM（图形引擎显存 512KB，GE 专用）
0x08000000 – 0x087FFFFF  主 RAM 片区开头（保留/别名区，不可用）
0x08800000 – 0x09FFFFFF  ★ 主内存（用户 RAM）
                          ├ 0x08804000 左右：游戏 ELF/EBOOT 解压后入口
                          ├ 0x0880xxxx–0x0886xxxx：游戏代码段（hooks 在这）
                          ├ 0x0896xxxx：sceIo 等导入/跳转表
                          ├ 0x089Exxxx：preload 数据段
                          ├ 0x09B4xxxx / 0x09BAxxxx：物品/箱子/口袋
                          ├ 0x09DA9860：MHP3 怪物列表指针
                          └ 0x09F00000 起：Slim/64MB 扩展 RAM
                            （loader 工作区 0x09FA2100 就在这）
0x40000000 + addr         主 RAM 的"未缓存"镜像
0x48000000 + addr         同上（0x08800000 起始全套镜像）
0x88000000 + addr         主 RAM 的"内核访问"镜像（普通用户态不可用）
─────────────────────────────────────────────────────────────
```

关键点：

1. **0x08800000 由硬件决定**。内存映射从 0x08000000 起，但实际上是
   "从 0x08800000 才开始 usable"（PPSSPP MemMap.cpp 原注释：`only from 0x08800000
   is it usable (last 24 megs)`）。0x08000000–0x087FFFFF 是保留区。
2. **游戏的一切绝对地址都落在 0x08800000+**：代码、全局数据、指针、结构体。
   PSP 上的指针存的就是绝对地址，所以"怪物指针→结构体"这类链条全是这条街的门牌号。
3. **真机与模拟器一致性**：PPSSPP 忠实模拟这套映射，游戏在两边用同一地址跑
   （mhp3reload 的 MIPS 补丁就是在真机上用这些地址）。
4. **"未缓存"镜像 0x4xxxxxxx**：真机作弊工具有时用 `0x4xxxxxxx` 写内存（绕过缓存、
   保证立即生效，如 VRAM/自改码常用）。在 PPSSPP 里写 `0x4xxxxxxx` 也会被处理，
   但常规读写用 0x08xxxxxx 绝对地址即可。
5. **模拟器宿主内存是另一回事**：PPSSPP 把 PSP 内存块映射到**宿主进程的任意地址**
   （`Memory::base`），外部工具要读必须知道它（原版 PC overlay 用
   `SendMessageW(0xB118)` 拿基址）。我们内置版直接给 PSP 虚拟地址，PPSSPP 内部换算。

### 实操换算规则

| 场景 | 用哪个地址 | 示例 |
|---|---|---|
| 游戏代码/内置 overlay / PPSSPP 内存编辑器 | 绝对地址 `0x08xxxxxxxx` | `0x09B4C244` |
| 真机 cheat 数据库给的地址（偏移） | 先加基址：`偏移 + 0x08800000` | `0x0134C244 → 0x09B4C244` |
| 外部工具读 PPSSPP 进程 | 还需要宿主 `base_address`（= `Memory::base`） | base + 0x09B4C244 |
| 真机"立即生效"写 | 未缓存镜像 `0x4xxxxxxx`（可选） | 0x49B4C244 |

> 所以：**拿到任何 PSP 地址，先判它是不是"相对 0x08800000 的简写"，再决定要不要
> 加基址；同版本游戏真机/模拟器地址永远相同。**

## 1. 怪物数据（全部游戏）

> 以下四个游戏的表全部来自 **[orig]**（Alexander-Lancellott 的 Python 原版 overlay）。
> 用法：`列表指针 = 0x08800000 + initial` → 怪物列表 `指针[i](4字节)`，非零即现存怪物；
> 结构偏移 = 怪物结构体指针 + 下表偏移。名称字节 → 怪表见各游戏 `monsters_*.py`
> 生成的 `UI/monster_tables.inc`；尺寸/金冠阈值、异常状态判定与 [orig] 完全一致。

### 1.1 MHF / MHP（ULES00318 / ULUS10084 / ULJM05066）
| Disc ID | 列表指针(initial) | 名 | HP | 最大HP | 尺寸 | 毒 当前/阈值 | 眠 当前/阈值 | 麻 当前/阈值 | 晕 当前/阈值 | 怒 @u16 |
|---|---|---|---|---|---|---|---|---|---|---|
| `ULES00318` | `0x1254D70` | `0x210` | `0x312` | `0x43C` | `0x2A4` | `0x3A8` / `0x46C` | `0x462` / `0x460` | `0x474` / `0x472` | —（无） | `0x580` |
| `ULUS10084` | `0x1253F70` | `0x210` | `0x312` | `0x43C` | `0x2A4` | `0x3A8` / `0x46C` | `0x462` / `0x460` | `0x474` / `0x472` | —（无） | `0x580` |
| `ULJM05066` | `0x1253570` | `0x210` | `0x312` | `0x43C` | `0x2A4` | `0x3A8` / `0x46C` | `0x462` / `0x460` | `0x474` / `0x472` | —（无） | `0x580` |
### 1.2 MHF2 / MHP2（ULES00851 / ULUS10266 / ULJM05156）
| Disc ID | 列表指针(initial) | 名 | HP | 最大HP | 尺寸 | 毒 当前/阈值 | 眠 当前/阈值 | 麻 当前/阈值 | 晕 当前/阈值 | 怒 @u16 |
|---|---|---|---|---|---|---|---|---|---|---|
| `ULES00851` | `0x127AD70` | `0x1E8` | `0x2E2` | `0x41E` | `0x274` | `0x388` / `0x450` | `0x446` / `0x444` | `0x458` / `0x456` | `0x440` / `0x55E` | `0x564` |
| `ULUS10266` | `0x12799F0` | `0x1E8` | `0x2E2` | `0x41E` | `0x274` | `0x388` / `0x450` | `0x446` / `0x444` | `0x458` / `0x456` | `0x440` / `0x55E` | `0x564` |
| `ULJM05156` | `0x1278E70` | `0x1E8` | `0x2E2` | `0x41E` | `0x274` | `0x388` / `0x450` | `0x446` / `0x444` | `0x458` / `0x456` | `0x440` / `0x55E` | `0x564` |
### 1.3 MHFU / MHP2G（ULES01213 / ULUS10391 / ULJM05500）
| Disc ID | 列表指针(initial) | 名 | HP | 最大HP | 尺寸 | 毒 当前/阈值 | 眠 当前/阈值 | 麻 当前/阈值 | 晕 当前/阈值 | 怒 @u16 |
|---|---|---|---|---|---|---|---|---|---|---|
| `ULES01213` | `0x1412140` | `0x1E8` | `0x2E4` | `0x41E` | `0x274` | `0x388` / `0x450` | `0x446` / `0x444` | `0x458` / `0x456` | `0x440` / `0x566` | `0x56C` |
| `ULUS10391` | `0x1412240` | `0x1E8` | `0x2E4` | `0x41E` | `0x274` | `0x388` / `0x450` | `0x446` / `0x444` | `0x458` / `0x456` | `0x440` / `0x566` | `0x56C` |
| `ULJM05500` | `0x140D3C0` | `0x1E8` | `0x2E4` | `0x41E` | `0x274` | `0x388` / `0x450` | `0x446` / `0x444` | `0x458` / `0x456` | `0x440` / `0x566` | `0x56C` |
### 1.4 MHP3RD / HD（ULJM05800 / NPJB40001）
| Disc ID | 列表指针(initial) | 名 | HP | 最大HP | 尺寸 | 毒 当前/阈值 | 眠 当前/阈值 | 麻 当前/阈值 | 晕 当前/阈值 | 怒 @u16 |
|---|---|---|---|---|---|---|---|---|---|---|
| `ULJM05800` | `0x15A9860` | `0x62` | `0x246` | `0x288` | `0xD4` | `0x23C` / `0x252` | `0x24E` / `0x24C` | `0x25A` / `0x258` | `0xC5C` / `0xC5E` | `0xBC8` |
| `NPJB40001` | `0x19B0AE0` | `0x62` | `0x246` | `0x288` | `0xD4` | `0x23C` / `0x252` | `0x24E` / `0x24C` | `0x25A` / `0x258` | `0xC5C` / `0xC5E` | `0xBC8` |
> MHP3RD 偏移经 [hpbar]（Kurogami2134/mhp3rd_monster_hp_bar）交叉验证：
> 列表指针 `0x09DA9860 == 0x08800000 + 0x15A9860` ✅、HP `+0x246`、最大HP `+0x288` ✅

## 2. P3 专属扩展：任务 / 游戏状态（仅 P3 有公开数据；其余版本对应区待挖）

| 含义 | ULJM05800（原版/NOHD） | NPJB40001（HD） | 来源 |
|---|---|---|---|
| 任务状态（in-quest 标志，0x656D6167 魔数附近） | `0x09C57CA0` | `0x0A05E620` | **[hpbar]**(仅原版) **[dmg]** |
| 返回中/任务阶段字节（<3 判定） | `0x09BAC044` | 未提供 | **[hpbar]** |
| 加载画面标志 | `0x08AB49EC` | 未提供 | **[hpbar]** |

## 3. P3 专属扩展：玩家数据（仅 P3）

| 含义 | ULJM05800（原版/NOHD） | NPJB40001（HD） | 来源 |
|---|---|---|---|
| player_area（玩家状态区） | `0x08B24979` | `0x08B2B139` | **[dmg]** |
| 手持武器结构体（EQUIPPED_WEAPON） | `0x09B49234` | `0x09F4FCE4` | **[sharp]** |
| 斩味当前值 | `0x09B49234 + 0x5CC` | `0x09F4FCE4 + 0x5DC` | **[sharp]** |
| 斩味表（武器各色斩味数据） | `0x0897D728` | `0x08983060` | **[sharp]** |
| 精灵/纹理显示信息 | `0x08B268DC` | `0x08B2D09C` | **[sharp]** |

## 4. P3 专属扩展：物品（仅 P3）

| 含义 | ULJM05800（原版/NOHD） | NPJB40001（HD） | 来源 |
|---|---|---|---|
| 箱子 ITEM_BOX | `0x09B4C244` | `0x09F52CF4` | **[item]** |
| 口袋 1 ITEM_POUCH1 | `0x09BA8D4A` | `0x09FAF7FE` | **[item]** |
| 口袋 2 ITEM_POUCH2 | `0x09B4D9B4` | `0x09F54464` | **[item]** |
| 给道具函数 GIVE_ITEM | `0x09CD0440` | `0x0A0D20A8` | **[item]** |
| 按键保持检测 CONTROL_HOLD | `0x09BB7A64` | `0x09FBE764` | **[item]** |
| 套装 hook | `0x09D48EE0` | `0x0A14AAE0` | **[item]** |

## 5. P3 专属扩展：渲染 / 相机（伤害飘字、小地图、自由视角用）

| 含义 | ULJM05800（原版/NOHD） | NPJB40001（HD） | 来源 |
|---|---|---|---|
| ViewMatrix（视图矩阵，世界→屏幕投影） | `0x09B486B0` | `0x09F4F120` | **[dmg]** |
| 主渲染 hook / 返回 | `0x088E6D64 / 0x088EBAB8` | `0x088E881C / 0x088EE410` | **[dmg]** |
| printf（画面打印文字） | `0x088EAA64` | `0x088EC51C` | **[dmg]** |
| 伤害数字 ADD hook/返回 | `0x09C750FC / 0x09C953E0` | `0x0A07BA7C / 0x0A09BD60` | **[dmg]** |
| 打印设置区 PRINT_SETTINGS | `0x09ADB910` | `0x09EE2350` | **[dmg]** |
| 伤害判定 CHECK | `0x09C1EC70` | `0x0A025608` | **[dmg]** |
| sceGeListEnQueue（绘制队列） | `0x08960CF8` | 未提供 | **[hpbar]** |
| 血条 mod 自用绘制区 | `0x08800FF0` | 未提供 | **[hpbar]** |

> ViewMatrix + player_area 是之前"伤害数字"需求的两个关键锚点（都有原版/HD 两套）—— 想做随时能继续。

## 6. P3/HD 专属：文件系统 / 模组加载（loader 管线）

| 名称 | P3 原版 | P3HD | 来源 |
|---|---|---|---|
| EBOOT_LOAD | `0x0880134C` | `0x0880134C` | **[load]** |
| PRELOAD_HOOK | `0x088215D4` | `0x08821818` | **[load]** |
| PRELOAD_INIT | `0x089E02A0` | `0x089DFE60` | **[load]** |
| 文件读 hook | `0x0886242C` | `0x0886365C` | **[load]** |
| 文件寻址 hook | `0x08864390` | `0x088655C0` | **[load]** |
| 解密 hook | `0x088641F0` | `0x08865420` | **[load]** |
| 模组表工作区 | `0x09FA2100`（+0x800 装载表） | `0x083B5600` | **[load]** |
| sceIo 导入表 | `0x08960A00` 起 | `0x08965690` 起 | **[load]** **[item]** |
| 替换文件目录 | `ms0:/P3RDML/FILES/` | `ms0:/P3RDHDML/FILES/` | **[load]** **[modman]** |
| 动画数据区 | `0x099C0000`（anim offset） | — | **[modman]** |

---

## 9. MHFU 静态数据表 [transmog]（来源：mhfu_transmog 的 FINDINGS.md）

> 基于 FUComplete 补丁 / 存档差分验证，CWCheat 示例确认映射关系。

| 表 | 地址 | 备注 |
|---|---|---|
| 武器表 | `0x089574E8` | 24 字节/条，约 1149 条（截至 ~`0x0895E0A0`）；模型 id @ `+0x10`（1 字节） |
| 防具指针表 | `0x08975970` | 按 `type2` 索引各槽表指针 |
| └ 腿 | `0x08970D30` | 40 字节/条 |
| └ 头 | `0x08960750` | 40 字节/条 |
| └ 胸 | `0x08964B70` | 40 字节/条 |
| └ 臂 | `0x08968D10` | 40 字节/条 |
| └ 腰 | `0x0896CD48` | 40 字节/条（指针索引 5/6 是标志 `0x02030007`/`0x04030002`，非指针） |
| 模型查找表 | `0x0893E7F0` | 全装备类型共享 —— 勿改 |
| 制作/升级表 | `0x08938D1A` | |
| 武器索引表 | `0x089A1878` | |
| FUComplete TABLE_A | `0x089972AC` | u16 数组（模型加载时不使用） |
| FUComplete TABLE_B | `0x08997BA8` | u16 数组（匹配 modelIdMale） |
| FUComplete TABLE_E | `0x0899851C` | u16 数组 |

**警示（FINDINGS.md 原文）**：运行时地址不稳定（随装备加载变化），例如
`0x08A35890`（当前头部模型 file_id）、`0x0912F54C`。只可用于实时工具，
**不能用于静态 CWCheat 代码**。

## 10. MHP3RD 静态数据表 [transmog3]（ULJM05800，来自 build_data.py）

### 防具槽表

| 槽 | 基址 | 条目数 | 玩家偏移 |
|---|---|---|---|
| 胸 chest | `0x08980144` | 233 | +0x1C |
| 臂 arms | `0x0897DFFC` | 213 | +0x26 |
| 腰 waist | `0x08984DAC` | 214 | +0x30 |
| 腿 legs | `0x08986F1C` | 220 | +0x3A |
| 头 head | `0x089825AC` | 256 | +0x44 |

### 武器表

| type | 武器 | 基址 | 条目大小 |
|---|---|---|---|
| 5 | 大剑 | `0x08992168` | 28 |
| 6 | 片手 | `0x0898FA78` | 28 |
| 7 | 大锤 | `0x0898E71C` | 28 |
| 8 | 长枪 | `0x08990D64` | 28 |
| 9 | 重弩 | `0x0898AB2C` | 80 |
| 11 | 轻弩 | `0x0898C01C` | 80 |
| 12 | 太刀 | `0x08991800` | 28 |
| 13 | 斩斧 | `0x0898D5D4` | 28 |
| 14 | 铳枪 | `0x089904DC` | 28 |
| 15 | 弓 | `0x089891DC` | 80 |
| 16 | 双剑 | `0x0898F164` | 28 |
| 17 | 笛 | `0x0898DDB4` | 28 |

### 相关代码地址（模型加载管线）

| 地址 | 含义 |
|---|---|
| `0x088691FC` | 装备模型加载函数（hook 目标） |
| `0x0886927C` | 80 字节武器类型 + 防具的 wrapper（`lhu v0,0(v0)`） |
| `0x088692A0` | 28 字节武器类型的 wrapper |
| `0x08868538`–`0x08868710` | 模型加载代码区 |
| `0x08966598` | 槽位映射跳转表 |
| `0x08966184` | 按 type 字节索引的处理函数表 |


## 11. [re] MHFU 运行时结构（SiD3W4y/mhfu-re `doc/objects.txt`）

| 结构 | 地址 / 偏移 | 字段 |
|---|---|---|
| 游戏态 | 指针 @ `0x09C122B0`（game_task.ovl 加载后） | 巨型全局；+0x1220 实体指针列表（0x14 个指针），+0x1270 实体数 u32 |
| 玩家态 | vtable `0x089BB3CC` | 见下 |
| — 坐标 | `+0x200 / +0x204 / +0x208` | x / y / z（f32）—— **玩家坐标！** |
| — 地图入口 | `+0x2A4` | u8（1/2/3） |
| — HP | `+0x2E4` | u16 |
| — 本帧伤害 | `+0x3B8` | i32（调用 deal_damage 前取负） |
| — 物品箱索引 | `+0x55D` | |
| 怪物态 | 与玩家同布局（坐标/HP/伤害变量同） | |
| — 实体 id | `+0x1E4` | u8 |
| — 下一个 id | `+0x1E6` | u16 |
| — 类型 | `+0x1E8` | u8 —— **== [orig] MHFU 名偏移 `+0x1E8`（互证 ✓）** |

玩家 vtable：[4] 任务开始 `0x09A65498`；[16] 换图 `0x09A655F0`（同时设置坐标）；
[26] `deal_damage` `0x088D6594`（>0 治疗，<0 受伤；调用点 `0x09A6B77C` 伤害 /
`0x09A69BCC` 回复）；[27] 玩家动作 `0x09A67630`。
怪物 vtable：[34] `change_state`（调用点 `0x09AC8B4C`）；[58] 受击处理 `0x09AD4C58`；
金狮子 vtable `0x089BCD64`。实体类型 0..175 表 = MHFU 怪表（对照 [orig] `monsters_mhfu`）。
箱子：指针 @ `0x089CC558`，物品数组 @ `+0x390`（u16 物品 id + s16 数量 对）。

## 12. [re] MHFU 关键函数

| 地址 | 函数 |
|---|---|
| `0x0884EA1C` | `decrypt_data(全局态, 数据, 大小)` |
| `0x09A6B130` | 伤害计算（对实体 `+0x3B8` 应用全部修正）—— **伤害数字 hook 候选** |
| `0x09AC8AF0` | 间接改怪物状态（vtable+0x88） |
| `0x088D72A4` | `give_item(玩家态, u16 物品id, s16 数量)` |

## 13. [re] OVL 格式与运行时段（`runtime.txt` / `ovl.txt`）

- overlay 在 `psp_game/usrdir/data.bin`（加密档案；用 svanheulen/mhff 或 mhef 解包）。
- `struct OVLFile`：magic `"MWo3"`、u32 load_base（== eboot 段 vaddr）、load_end、
  name[0x60]、随后数据；**映射期间头部保留在内存**。
- 运行时段列表：demo/edit/movie/install/arcade 等任务加载在 `0x09A5F300+`
  （完整列表见 doc/runtime.txt）。

## 14. [cheats] MHP3 护石技能组合表（节选）＋CWCheat 换算

`mhp3.talisman.txt`（175 行）：双技能组合 id → 技能，例：
`0001 = Torso Up`、`0101 = Torso Up / Poison`、`0202 = Poison / Sleep`、
`0404 = Sleep / Health`、`0F0F = Guard / Bomb Boost`、`1010 = Guard Up / Gluttony`……
完整表见 9r3i/mhp3-cheats。

CWCheat → 绝对地址示例（`cheats.md` 内代码）：
攻击倍率 `_L 0x200AD964 …` → 绝对地址 **`0x088AD964`**（u32 指令写，+`0x08800000`）。
库内任何码都按此规则换算。

## 15. [mhef] PSP 存档加密常量（`mhef/psp.py`）

- MHP2G JP 盐 `b'S)R?Bf8xW3#5h9lGU8wR'`、NA `b'3Nc94Hq1zOLh8d62Sb69'`、
  MHP3 JP `b'VQ(DOdIO9?X3!2GmW#XF'`；SHA-1(数据+盐)。
- 各版本密钥：MHP `b'>\r\xb2\xef…'`、MHP2 `b'\xe3\xb5\xce…'`、
  MHP2G `b'\xcd\x1f Y…'`、MHP3 `b"\xe3\x05\xce…"`（完整字节见原文件）。
- 加密默认键 `(0x2345, 0x7f8d)`；MHP2G/MHP3 例外字节列表。
- 用途：存档数据挖掘与校验（非运行时内存）。

## 16. 附加数据源（交叉引用，无运行时地址）

- Saramagrean/CWCheat-Database-Plus- —— 泰文 CWCheat 库，含 P3 已测名字编辑（PR #62）；偏移→绝对规则同上。
- `mhfu.monweak.txt`（怪物弱点）、`mhfu.armor.skill.txt`（防具技能）、`cheats.md`
  攻击码 —— 验证表语义的数据参考。
- tclamb/mhp2g-decomp：WIP，暂无公开成果（见 TODO）。

## 7. 交叉验证记录（[val] 本项目所做工作的全部内容）

- `0x09DA9860`（hpbar 怪物列表指针）== 本项目 `0x08800000 + 0x15A9860` ✅
- 怪物 HP 偏移 `+0x246`、最大 HP `+0x288`（hpbar）== 本项目 MHP3RD 表 ✅
- sceIo 导入表地址（load/item 两处仓库一致）✅

## 8. 待挖（TODO）

- 怪物坐标 x/y/z（结构体内/附近）—— P3 可从 `+0xD4` 附近/ViewMatrix 锚点入手
- 任务信息区（任务名/时限/报酬）
- 玩家属性（攻防/技能）
- 肉质/疲劳表（file id 体系，配合 `MHP3rd-Game-FIle-List`）
- **MHF / MHF2 / MHFU 的玩家区/物品区/渲染相机区**（目前只有怪物数据表 [orig]）
- MHP3RD HD 的怪物结构偏移二次确认（当前沿用 [orig]；[hpbar] 仅验证了原版）