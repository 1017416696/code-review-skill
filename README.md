# Code Review Skill

自动化代码审查技能，用于检查 Git 提交的代码变更。

## 功能特性

- ✅ 审查单个提交或连续多个提交
- ✅ 区分"必须修复"和"建议改进"两个严重级别
- ✅ 支持 Java、JavaScript/TypeScript、Python 等多语言规则
- ✅ 简洁清晰的报告格式

## 使用方法

### 安装

将本仓库克隆到 Cursor/Claude 的 skills 目录：

```bash
# 个人 Skill（全局可用）
git clone https://github.com/1017416696/code-review-skill.git ~/.claude/skills/code-review

# 或项目 Skill（仅当前项目）
git clone https://github.com/1017416696/code-review-skill.git .cursor/skills/code-review
```

### 使用方式

#### 审查最近一次提交
```
review
审查代码
code review
```

#### 审查指定提交
```
review abc1234
```

#### 审查多个连续提交
```
review abc1234..def5678
```

> **注意**：多个提交时，提交列表按从旧到新的顺序展示。

## 审查标准

### 通用规则

**🔴 必须修复：**
- 未处理的异常
- 安全问题（硬编码密钥、SQL 注入等）

**⚠️ 建议改进：**
- 魔法数字应使用常量
- 删除调试日志
- 遵循 DRY 原则
- 函数不宜过长
- 命名规范
- 注释问题

### 语言特定规则

- **Java**: 资源关闭、空指针风险、日志级别等
- **JavaScript/TypeScript**: 异步错误处理、类型问题、变量声明等
- **Python**: PEP 8 规范、类型注解、导入顺序等

详细规则请查看 [STANDARDS.md](STANDARDS.md)

## 报告格式

```markdown
# Code Review: <hash>

## 📊 变更概览
- 提交信息: ...
- 文件数量: X 个文件
- 变更行数: +X / -Y

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

## 自定义规则

你可以编辑 `STANDARDS.md` 文件来添加或修改审查规则，无需修改主 Skill 文件。

## License

MIT
