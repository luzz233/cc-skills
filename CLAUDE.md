# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库定位

个人 plugin marketplace，集中管理自用的 skill、hook、subagent。这里没有 build/test/lint——仓库本身就是交付物，通过 plugin 机制消费（Claude Code 与 ZCode 双端兼容）。

## 架构：每插件独立根

每个插件一个独立目录，自带清单与组件，目录即插件根：

```
interfaces/               ← 插件根
├── .claude-plugin/plugin.json   ← 清单：name/version/skills 等组件声明
└── skills/<name>/SKILL.md       ← skill 本体，一个子目录一个 skill
to-obs/
├── .claude-plugin/plugin.json
└── skills/to-obs/SKILL.md
```

- `.claude-plugin/marketplace.json` 里每条 entry 只写 `{"name", "source": "./<插件目录>"}`；组件一律由插件根的 plugin.json 声明，不要在 marketplace entry 上写组件字段
- 清单路径 `.zcode-plugin/plugin.json` 与 `.claude-plugin/plugin.json` ZCode 都认，本仓库统一用后者（Claude Code 原生格式）
- plugin.json 的 `skills` 字段 = 字符串或字符串数组，相对插件根；指向的目录下 `skills/<name>/SKILL.md` 会被扫描（本仓库约定 `skills` 目录内一层子目录一个 skill）
- **为什么放弃共享根切片**（旧的 `source: "./"` + entry 上写 `skills` 数组）：ZCode 加载插件时要求插件根必须有 plugin.json，组件字段写在 marketplace entry 上它不读；且多个 entry 共享同一根会导致清单归属混乱。独立根是两端的标准形态
- SKILL.md frontmatter 必含 `name`、`description`；多余键（如上游的 `disable-model-invocation: true`）ZCode 会降级为「不可自动触发」，不会导致加载失败，保留不动

## 新增组件的流程

**新插件**：建 `<name>/.claude-plugin/plugin.json` + `<name>/skills/...` → marketplace.json 加一条 `{"name", "source"}` → validate。

**往现有插件加 skill**：在 `<插件根>/skills/<skill-name>/` 建 SKILL.md → validate。无需改任何 json（`skills` 指向整个目录）。

**hook**：脚本放 `<插件根>/scripts/`，hooks.json 结构（事件名、matcher 与用户级 hooks 同一套）：

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

任何改动后跑 `claude plugin validate .`；安装端用 `/plugin marketplace update` 刷新。

## 常用命令

- `claude plugin validate .` — 校验 marketplace.json、条目、plugin.json、frontmatter；改过仓库必跑
- 消费（Claude Code 会话内）：`/plugin marketplace add luzz233/cc-skills`（或本地路径），再 `/plugin install <name>@cc-skills`

## 约定与红线

- plugin.json 的 `name` 必须与 marketplace entry 的 `name` 一致，且与所在目录名一致
- `backup/CLAUDE.global.md` 是全局 `~/.claude/CLAUDE.md` 的 tracked 备份副本（防误改、防工具覆写）；以 home 那份为准，改了全局就同步更新这份
- malformed hooks.json 会导致整个 plugin 加载失败——改 hooks 后必须 validate
- hook 引用插件文件一律用 `${CLAUDE_PLUGIN_ROOT}`（解析到缓存副本，每次更新都变，所以要加双引号防路径带空格）；需要跨更新存活的状态放 `${CLAUDE_PLUGIN_DATA}`
