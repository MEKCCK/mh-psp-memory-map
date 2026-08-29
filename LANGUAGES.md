# LANGUAGES — 语言状态 / Language Status

目标是 9 种主流语言（English, 简体中文, 日本語, 한국어, Español, Português (BR),
Русский, Français, Deutsch）。README 已全量翻译；docs 从 en/zh-CN 起步，其余语言
靠社区 PR 逐步补齐（每条翻译 PR：只翻正文与表头，**地址与数字保持原样**）。

Status legend: ✅ 完成 · 🟡 部分 · ⬜ 待翻译/待创建

| Language | Code | README | docs/memory-map | docs/digging-guide | docs/contributing |
|---|---|---|---|---|---|
| English | en | ✅ `README.md` | ✅ `docs/en/` | ✅ `docs/en/` | ✅ `docs/en/` |
| 简体中文 | zh-CN | ✅ `README.zh-CN.md` | ✅ `docs/zh-CN/` | ✅ `docs/zh-CN/` | ✅ `docs/zh-CN/` |
| 日本語 | ja | ✅ `README.ja.md` | ⬜ `docs/ja/` | ⬜ | ⬜ |
| 한국어 | ko | ✅ `README.ko.md` | ⬜ `docs/ko/` | ⬜ | ⬜ |
| Español | es | ✅ `README.es.md` | ⬜ `docs/es/` | ⬜ | ⬜ |
| Português (BR) | pt-BR | ✅ `README.pt-BR.md` | ⬜ `docs/pt-BR/` | ⬜ | ⬜ |
| Русский | ru | ✅ `README.ru.md` | ⬜ `docs/ru/` | ⬜ | ⬜ |
| Français | fr | ✅ `README.fr.md` | ⬜ `docs/fr/` | ⬜ | ⬜ |
| Deutsch | de | ✅ `README.de.md` | ⬜ `docs/de/` | ⬜ | ⬜ |

## 如何添加一种新语言的 docs（How to add a language's docs）

1. 复制 `docs/en/` 下三个文件到 `docs/<code>/`（目录命名见上表 Code）。
2. 只翻译 **正文与表格表头**；地址、偏移、Disc ID、数字、表格数据**保持原样**。
3. 更新本文档状态表，并在该语言 README（如 `README.ja.md`）里把 `docs/en/` 链接改成
   `docs/<code>/`。
4. 提交 PR（参见 `docs/en/contributing.md` 的 Translations 一节）。

> 翻译不用一次翻译全部：可以先只翻 `contributing.md` 或 `digging-guide.md`，
> 状态表如实更新即可。