---
name: code-review
description: 自动化代码审查技能，用于检查 Git 提交的代码变更。当用户请求审查代码、检查 PR、code review、review changes、review [hash] 或分析代码质量时触发此技能。
---

# Code Review

审查 Git 提交的代码变更，检查代码质量问题并生成简洁报告。

## 触发场景

- `review` / `审查代码` / `code review`
- `review abc1234`（指定提交）
- `review abc1234..def5678`（多个连续提交）

## 审查流程

### 第一步：解析输入，确定审查范围

根据用户输入确定提交范围：

| 输入 | 审查范围 | Git 命令 |
|------|----------|----------|
| 无参数 | 最近一次提交 | `git show HEAD --stat` |
| `abc1234` | 指定的单个提交 | `git show abc1234 --stat` |
| `abc1234..def5678` | 从 abc1234 到 def5678 的所有提交 | `git log abc1234..def5678 --oneline` |

### 第二步：获取代码变更

**单个提交：**
```bash
git show <commit-hash> --format="%H%n%s%n%b" --stat
git show <commit-hash> --format="" -- .
```

**多个提交：**
```bash
# 获取提交列表（从旧到新，包含起始提交）
# 注意：git log A..B 不包含 A，需要使用 A~1..B 来包含 A
git log '<start>~1..<end>' --oneline --reverse

# 获取整体 diff（从 start 的父提交到 end，这样 diff 包含 start 的变更）
git diff '<start>~1..<end>'
```

> **注意**：
> - 用户输入 `abc1234..def5678` 表示要审查从 abc1234 到 def5678 的所有提交（包含两端）
> - 由于 `git log A..B` 语法不包含 A，实际执行时需要使用 `A~1..B` 来包含起始提交
> - 使用 `~1` 而非 `^`，因为 `^` 在 zsh 中是特殊字符需要转义
> - 多个提交时，按从旧到新的顺序展示提交列表，但只审查最终状态的代码变更

### 第三步：分析代码变更

1. 读取 [STANDARDS.md](STANDARDS.md) 获取审查规则
2. 根据变更文件的类型，应用对应的语言规则
3. 逐条检查是否违反规则

### 第四步：生成审查报告

**单个提交报告格式：**

```markdown
# Code Review: <short-hash>

## 📊 变更概览
- **提交信息**: <commit message>
- **文件数量**: X 个文件
- **变更行数**: +X / -Y

## 🔴 必须修复

1. [问题类型] - `文件名:行号`
   问题描述及修复建议

## ⚠️ 建议改进

1. [问题类型] - `文件名:行号`
   问题描述及改进建议

## ✅ 总结
- 必须修复: X 个
- 建议改进: Y 个
```

**多个提交报告格式：**

```markdown
# Code Review: <start-hash>..<end-hash>

## 📊 变更概览
- **提交范围**: X 个提交（从旧到新排列）
- **文件数量**: Y 个文件
- **变更行数**: +A / -B

## 📝 提交列表（从旧到新）
1. `abc1234` - feat: add address validation
2. `bcd2345` - fix: handle null case
3. `def5678` - refactor: extract common method

## 🔴 必须修复
...

## ⚠️ 建议改进
...

## ✅ 总结
...
```

**无问题时：**

```markdown
# Code Review: <hash>

## 📊 变更概览
...

✅ 审查通过，未发现问题
```

## 问题严重程度

| 级别 | 标识 | 说明 | 示例 |
|------|------|------|------|
| 必须修复 | 🔴 | 存在 bug、安全漏洞或严重问题 | 未处理的异常、硬编码密钥 |
| 建议改进 | ⚠️ | 影响代码质量或可维护性 | 魔法数字、重复代码、命名不规范 |

## 审查规则

详见 [STANDARDS.md](STANDARDS.md)
