# ROADMAP — 全平台覆盖 + 3DS / 统一前端封装

目标：把 MH HP Overlay 从"安卓专用"升级为**全平台发行**，并把模拟器侧链路整理为
**PPSSPP + 3DS 模拟器（Citra 系）统一前端**。本仓库（内存表）为数据层，
关联仓库 `MEKCCK/ppsspp`（模拟器魔改层）。

## 1. 全平台发行

| 平台 | 载体 | 状态 |
|---|---|---|
| Android | APK（v1.20.4-mh 分支） | ✅ 构建中/已出包 |
| iOS | IPA/DEB（同分支） | ✅ 已适配（含打字修复） |
| Linux | AppImage/tar.gz → Actions `manual_generate_desktop` | 🔄 已建工作流 |
| macOS 桌面 | .app/.zip（同工作流, macos-latest） | 🔄 已建工作流 |
| Windows | exe（vcpkg 依赖链，Windows runner） | ⬜ 下一轮 |

所有平台共用同一份 UI 层代码（`UI/MHOverlay.*` + 枚举/菜单/CMake），无平台分支。
桌面端文字渲染走各自平台的 FreeType/CoreText，中文友好。

## 2. 关于"显血"的 3DS 来源（原项目已确认）

- **原项目 = Alexander-Lancellott 的 MH-HP-Overlay 双版本**：
  PSP 版（已用）+ **3DS 版 `MH-HP-Overlay-For-3DS-Emulator`**（支持 MH3G/3U、MH4/U/G、MHX/GEN/XX）。
- 3DS 版读取方式（`modules/citra.py`）：**Citra 自带 UDP 调试口 `127.0.0.1:45987`**
  （RequestType: ReadMemory/WriteMemory/ProcessList/SetGetProcess）——原生脚本/调试协议，
  与 PSP 侧 WS 调试口同构，**集成路径与 PPSSPP 一致**。
- 3DS 指针表（`modules/mh3u_mh3g.py / mh4u_mh4g.py / mhxx.py` + `monsters_*.py` 怪表）：
  基址示例 MHXX `0xDD2360 / 0xDA2360 / 0xDB9360`，接口指针链 + 偏移随代际参数化（如
  `+0xFAC / +0x10A8`、`+0x360`、大小 `+0x154/+0x18C` 秒、耐性阈值等）。
- 计划：把 3DS 版数据整理进 `docs/3ds/`（怪表 + 各代指针链 + 基址参数），与 PSP 结构互证。

## 3. 统一前端（封装 PPSSPP + 3DS 模拟器）

目标形态：一个入口/外壳（App 或启动器），整合
- PPSSPP（PSP 系，见 MEKCCK/ppsspp 魔改分支 v1.20.4-mh）
- 3DS 模拟器（如 Citra），同样接入 overlay 数据层

阶段规划：
1. **P0 资料层**：完成 PSP 内存表（本仓库）；补 3DS 内存表（`docs/3ds/`，数据源
   = 3DS overlay 项目的 citra.py + mh3u/mh4u/mhxx 指针链）
2. **P1 模拟器层**：PPSSPP 全平台发布稳定（§1）；
   **3DS 模拟器选型 + 魔改分支**（Citra 系：Citra/Lime3DS 等；overlay 原生集成，
   读取可走其 UDP 45987 口或直接内置——同 PPSSPP 分支做法）
3. **P2 前端层**：
   - 桌面：统一起动器（选模拟器 → 自动带 overlay；共享存档/设置入口）
   - 移动端：单 App 壳内嵌两个模拟器视图（后续评估体积/兼容）
4. **P3 联动**：怪物数据读取统一（内存表数据源整合）、
   "一个怪表全平台"，cross-version 校验自动化

## 4. 里程碑

- [x] 内存表仓库独立（mh-psp-memory-map），多语言文档
- [x] PPSSPP v1.20.4 稳定基线 + overlay（安卓/iOS）
- [ ] 桌面 Linux/macOS 出包（工作流已建，待验）
- [ ] Windows 桌面出包
- [ ] 3DS overlay 化用 + `docs/3ds/` 数据章
- [ ] Citra(3DS) 魔改分支
- [ ] 统一前端（桌面启动器 → 移动端）

## 5. 备注

- 所有引用数据尊重原始项目（见 memory-map 来源标注）。
- 前端封装进度与 Issue 追踪放在本仓库的 Issues（前缀 `[FE]`/`[3DS]`）。