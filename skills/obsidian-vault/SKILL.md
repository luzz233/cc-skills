---
name: obsidian-vault
description: Search, create, and manage notes in the Obsidian vault with wikilinks and index notes. Use when user wants to find, create, or organize notes in Obsidian.
---

# Obsidian Vault

## Vault location

`D:/code/workspace_obsidian/vault2026/`

> 注意：路径用于 bash 命令（find/grep），用正斜杠。对应 Windows 路径 `D:\code\workspace_obsidian\vault2026`。

## 结构组织

用**编号前缀的一级文件夹**分类，支持多层嵌套。根目录不放零散笔记。

一级文件夹语义：

- `00. 收件箱/` — 新笔记、待整理材料的暂存区（可按项目建子文件夹，如 `202601远程双录/`）
- `01.待开发/` — 待开发项
- `05.cmis-cloud/` — 项目：cmis-cloud
- `10. 技术资料/` — 技术笔记，按技术栈分子文件夹（`Flutter/`、`Rust/`、`React/`、`Java/` 等）
- `20. 业务知识/` — 业务/行业知识（`银行业务/`、`财务会计/`、`数据中心/` 等）
- `30. 系统/` — 系统设计
- `50. 软件/`、`51. 硬件/`
- `95.阅读笔记/` — 读书笔记
- `99. 其他/` — 杂项（工具设置、开发规范等）

子文件夹也用编号前缀排序（如 `01.布局/`、`02.导航/`）。**`00.` 前缀的文件充当该目录的索引/速查页**（如 `00.组件索引.md`、`00.样式速查.md`）。

## 命名约定

- **中文为主**：`机房.md`、`架构分层.md`、`租赁会计准则.md`
- **技术术语/API 保留英文原形**（PascalCase）：`Scaffold.md`、`AlertDialog.md`
- **编号序列用点分**：`2.1 变量绑定与解构.md`、`01.React原理.md`
- 不用 Title Case，不用 YAML frontmatter（直接 `# 标题` 开头）
- 不使用 `#tag` 标签系统

## 链接

- 用 `[[wikilinks]]`，但并非每篇都用（约 40% 笔记含链接，按需添加）
- 形式：`[[笔记名]]`、`[[文件夹/笔记.md]]`、`[[#标题]]`，图片嵌入用 `![[xxx.png]]`
- 相关链接集中在笔记底部"相关组件 / 相关资源"章节

## Workflows

### Search for notes

```bash
# 按文件名搜索
find "D:/code/workspace_obsidian/vault2026/" -name "*.md" | grep -i "keyword"

# 按内容搜索
grep -rl "keyword" "D:/code/workspace_obsidian/vault2026/" --include="*.md"
```

或直接对 vault 路径使用 Grep/Glob 工具（中文关键词注意编码）。

### Create a new note

1. **归类**：根据内容放入对应一级文件夹；拿不准就放 `00. 收件箱/`
2. **命名**：中文为主，技术术语保留英文原形；属于编号序列就用点分前缀
3. **正文**：直接 `# 标题` 开头（不写 frontmatter）
4. **链接**：底部加"相关"章节，用 `[[wikilinks]]` 关联已有笔记

### Find related notes (backlinks)

搜索某篇笔记的反向链接：

```bash
grep -rl "\[\[Note Title\]\]" "D:/code/workspace_obsidian/vault2026/"
```

### Find index notes

`00.` 前缀的文件是各目录的索引/速查页：

```bash
find "D:/code/workspace_obsidian/vault2026/" -name "00.*.md"
```
