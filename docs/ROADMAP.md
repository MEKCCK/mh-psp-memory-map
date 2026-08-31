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

## 2. 关于"显血"的 3DS 来源

- 作者另有 **MH-HP-Overlay-For-3DS-Emulator**（3DS/Citra 版 overlay），部分显血
  数据与方法与 PSP 版同源/可互证（怪物结构、部位破坏、异常状态等心智模型一致）。
- 计划：把 3DS 版的已知数据整理进本内存表体系（3DS 为架构映射，新增一章
  `docs/3ds/`），并标注与 PSP 结构的异同，供交叉验证。

## 3. 统一前端（封装 PPSSPP + 3DS 模拟器）

目标形态：一个入口/外壳（App 或启动器），整合
- PPSSPP（PSP 系，见 MEKCCK/ppsspp 魔改分支 v1.20.4-mh）
- 3DS 模拟器（如 Citra），同样接入 overlay 数据层

阶段规划：
1. **P0 资料层**：完成 PSP 内存表（本仓库）；补 3DS 内存表（`docs/3ds/`）
2. **P1 模拟器层**：PPSSPP 全平台发布稳定（§1）；
   Citra 魔改分支建立（overlay 集成，参照 ppsspp 分支做法）
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