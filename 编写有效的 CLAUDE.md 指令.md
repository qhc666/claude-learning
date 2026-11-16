---
tags:
  - Claude
  - ClaudeCode
  - CLAUDE-md
来源: https://code.claude.com/docs/en/memory
创建日期: 2026-11-16
---

# 编写有效的 CLAUDE.md 指令

> [!important] 核心认识
> Claude 把 CLAUDE.md 当作**上下文**，而不是强制执行的配置。所以指令怎么写，直接影响 Claude 遵循它的可靠程度。

相关笔记：[[CLAUDE.md 的位置、层级与加载]]

## 一、指令要具体到可以验证

| ❌ 模糊的写法 | ✅ 具体的写法 |
| --- | --- |
| 正确格式化代码 | 使用 2 空格缩进 |
| 测试你的更改 | 提交前运行 `npm test` |
| 保持文件井然有序 | API 处理程序位于 `src/api/handlers/` |

判断标准：读完这条指令，能否明确检查出"做到了"还是"没做到"。

## 二、保持文件简短、有条理、一致

### 1. 长度（Size）

- 每个 CLAUDE.md 文件以**不超过 200 行**为目标。
- 文件越长，消耗的上下文越多，遵循度越低。
- 只与部分代码库相关的指令，移到[按路径限定范围的规则（path-scoped rules）](https://code.claude.com/docs/en/memory#path-specific-rules)中，这类规则只在 Claude 处理匹配的文件时才加载。
- [导入（imports）](https://code.claude.com/docs/en/memory#import-additional-files)可以帮助组织长文件，但**不会减少上下文开销**，因为被导入的文件会和导入它的 CLAUDE.md 一起加载。

### 2. 结构（Structure）

- 用 Markdown 标题和项目符号把相关指令分组。
- 有条理的章节比密集的段落更容易让 Claude 遵循。

### 3. 一致性（Consistency）

- 两条指令相互矛盾时，Claude 可能**随意选择其中一条**。
- 定期检查以下位置，移除过时或冲突的指令：
  - 项目的 CLAUDE.md 文件
  - 子目录中嵌套的 CLAUDE.md 文件
  - `.claude/rules/`
- 也可以让 Claude 代为查找：运行提示词审计（见下节）。

## 三、审计指令文件（prompt audit）

在会话中运行：

```
/doctor prompt-audit
```

### 它会检查什么

- 为旧版模型编写的指令
- 引用了不存在的文件或命令
- 文件之间相互矛盾

### 结果

- 得到一份报告，包含发现的问题和建议的修改。
- **在你要求 Claude 应用修改之前，文件不会有任何改动。**

### 审计范围

- 默认覆盖：
  - CLAUDE.md、CLAUDE.local.md、AGENTS.md
  - `.claude/` 和 `~/.claude/` 下的 rules、skills、commands、subagents、output styles
- 只审计单个文件或目录：传入路径，例如

```
/doctor prompt-audit .claude/skills/deploy
```

### 使用条件

- 审计通过内置的 `/claude-api` 技能运行；如果该技能在 `skillOverrides` 中被关闭，或通过 `disableBundledSkills` 被禁用，则审计不可用。
- 需要 Claude Code **v2.1.283 或更高版本**。

## 速记

- 具体 > 模糊：写能验证的指令
- 短：每个文件 < 200 行，局部规则用路径限定
- 整齐：标题 + 列表，不写大段文字
- 不打架：定期清理，或用 `/doctor prompt-audit`
