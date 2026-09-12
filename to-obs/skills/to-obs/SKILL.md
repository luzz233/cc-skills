---
name: to-obs
description: Search, create, and manage notes in the Obsidian vault with wikilinks and index notes. Use whenever the user mentions Obsidian, 笔记, or the vault — including recording debugging pitfalls and fixes（记踩坑 / 记一下 / 存到笔记）, organizing the inbox (00. 收件箱), archiving or renaming existing notes, fixing wikilinks, or asking where a note belongs. Also proactively suggest recording a hard-won fix when one surfaces during work.
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

### Capture a pitfall or fix（记踩坑与解法）

**主动建议时机**：调试中刚解决一个不直观的坑、发现反直觉的行为、找到关键配置/参数时，用一句话提议「要不要把这个坑记到笔记？」，用户同意才写。一次任务提议一次就够，被拒绝就不再提。

**落笔流程**：

1. **先查重**：按关键词搜文件名和内容（见 Search）。已有同主题笔记 → 用 Edit 在合适章节**追加**（遵守下面 Update 的逐段改动纪律），不新建
2. **没有 → 新建短笔记**：归属明确的直接进对应子文件夹；拿不准就放 `00. 收件箱/`。捕获要快，不在分类上纠结——治理时再归
3. **模板**（短，不写长文）：

   ```markdown
   # <问题一句话标题>

   **现象**：<什么场景、什么报错/异常行为>
   **原因**：<根因，一两句>
   **解法**：<关键步骤或代码片段>

   ## 相关
   - [[相关笔记]]
   ```

4. 命名遵循上面的命名约定

### Update an existing note

> 修改已有笔记**必须逐段改动**，让用户逐段审批 diff。禁止整篇重写。

- **用 Edit 工具**（`old_string → new_string`）替换要改的那一段，**不要用 Write 覆盖整篇**
- 多处改动 → 多次 Edit，一次一段，每次产出独立 diff 供审批
- `old_string` 要带足够上下文（前后一两行或所在标题），保证在文件中唯一匹配
- **只有这两种情况才用 Write**：①新建笔记；②确需大范围重写（重写前先向用户说明）
- diff 由 Claude Code 客户端呈现：终端为 unified diff，VS Code 扩展为 split diff

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

### Maintain existing notes（存量治理）

治理是批量视角的操作。**总纪律：先出计划、后动手**——先产出治理计划表（文件 → 建议动作：移动到哪 / 改成什么名 / 并入哪篇 / 删除），用户批准后才执行；每批最多 10 篇左右，分批做。

**清理收件箱**（`00. 收件箱/`）：

1. 逐篇读开头判断主题
2. 出计划表：目标位置 + 改名建议（按命名约定规范化）
3. 批准后移动，并对每篇执行下面的断链修复

**分类调整**：如果积压的根因是「无处可放」——多篇笔记堆在收件箱是因为现有分类兜不住——不要硬塞。先提分类调整建议（新增 / 合并 / 改名一级或二级文件夹），用户批准后再批量归档。

**重命名 / 移动的断链修复（硬规则）**：任何移动或改名前，先列出引用方；移动后逐个 Edit 更新这些文件里的 wikilink。跳过这步 = 留下断链。

```bash
grep -rl "\[\[旧笔记名\]\]" "D:/code/workspace_obsidian/vault2026/" --include="*.md"
```

**未命名文件**：读内容 → 按命名约定起名 → 归档。

**孤立笔记检测**：列出全 vault 没有任何入链的笔记，供用户决定补链还是合并（不自动处理）。

**索引补全**：找出有笔记但缺 `00.` 索引页的文件夹，提议生成。
