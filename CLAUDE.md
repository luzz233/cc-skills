# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库定位

个人 Claude Code plugin marketplace，集中管理自用的 skill、hook、subagent。这里没有 build/test/lint——仓库本身就是交付物，通过 Claude Code 的 plugin 机制消费。

## 架构：共享根切片

仓库根即 marketplace 根。每个功能是 `.claude-plugin/marketplace.json` 里 `plugins` 数组的一条独立 plugin 条目（`source: "./"`），用组件字段从共享目录切片。路径一律相对仓库根、以 `./` 开头；列出的路径就是该条目的完整组件集合，共享目录里未列出的不加载。

目录职责：

- `skills/<name>/SKILL.md` — skill 本体，一个子目录一个 skill
- `agents/<name>.md` — subagent，一个文件一个 agent
- `hooks/<name>.json` — hooks 配置，一个文件一组 hooks
- `scripts/` — hook 脚本等可执行文件

条目写法（组件字段按需组合；`skills`/`agents`/`commands` 是数组，`hooks` 是路径字符串或内联对象）：

```json
{
  "name": "<plugin-name>",
  "source": "./",
  "skills": ["./skills/<skill-name>"],
  "agents": ["./agents/<agent-name>.md"],
  "hooks": "./hooks/<name>.json"
}
```

## 新增组件的流程

**skill**：建 `skills/<name>/SKILL.md`（frontmatter 必含 `name`、`description`）→ 条目加 `"skills"` → validate。

**subagent**：建 `agents/<name>.md`（frontmatter 必含 `name`、`description`；可选 `model`、`tools`、`effort`、`maxTurns` 等，`model` 默认 `inherit`）→ 条目加 `"agents"` → validate。
限制：plugin 分发的 agent 不支持 `hooks`、`mcpServers`、`permissionMode` 字段（写了被忽略）；需要这些能力的 agent 放 `~/.claude/agents/`，不进本仓库。

**hook**：脚本放 `scripts/`，建 `hooks/<name>.json` → 条目加 `"hooks"` → validate。hooks.json 结构（事件名、matcher 与用户级 hooks 同一套）：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          { "type": "command", "command": "\"${CLAUDE_PLUGIN_ROOT}\"/scripts/xxx.sh", "timeout": 30 }
        ]
      }
    ]
  }
}
```

任何改动后跑 `claude plugin validate .`；已安装端用 `/plugin marketplace update` 刷新。

## 常用命令

- `claude plugin validate .` — 校验 manifest、条目、frontmatter、hooks.json；改过仓库必跑
- 消费（Claude Code 会话内）：`/plugin marketplace add <本地路径或 owner/repo>`，再 `/plugin install <name>@cc-skills`

## 约定与红线

- `skills/`、`agents/`、`hooks/`、`scripts/` 等组件目录必须在仓库根，绝不能放进 `.claude-plugin/`（里面只放 json）
- marketplace 名字不能用 Anthropic 保留名（`claude-plugins-official`、`claude-community` 等）
- 本仓库约定：条目的 `name` 与其主组件的目录/文件名保持一致，便于对应
- malformed `hooks/*.json` 会导致整个 plugin 加载失败——改 hooks 后必须 validate
- hook 引用插件文件一律用 `${CLAUDE_PLUGIN_ROOT}`（解析到缓存副本，每次更新都变，所以要加双引号防路径带空格）；需要跨更新存活的状态放 `${CLAUDE_PLUGIN_DATA}`
