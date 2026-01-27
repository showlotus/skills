# Claude Skills 技能集

为 Claude AI 量身定制的实用技能集合，提升开发效率和职业生产力。

## 📚 Skills 列表

### [Code Review](./code-review/) - 智能代码审查 🔍

自动检查 Git 暂存区代码改动的潜在风险和问题，提供专业的代码审查报告。

**核心能力：**
- 类型安全检查（TypeScript/JavaScript）
- 逻辑风险分析（条件判断、异步流程、错误处理）
- 调试代码检测（console.log、debugger 残留）
- 测试覆盖评估
- 性能隐患识别（重渲染、内存泄漏）
- 安全漏洞扫描（敏感信息泄露、注入风险）

**使用示例：**
```
"帮我 review 一下暂存区的代码"
"检查一下我的改动有没有问题"
```

**技术栈：** TypeScript/JavaScript, React/Vue, Node.js

---

### [Resume Optimizer](./resume-optimizer/) - 技术简历优化器 ✍️

将平淡的技术工作描述改写为符合大厂标准的专业简历要点。

**核心能力：**
- 强动词开头，提升表达力度
- 技术关键词优化，精准展示能力
- STAR 法则应用（情境-任务-行动-结果）
- 三种风格输出：标准专业版、数据驱动版、专家架构师版

**使用示例：**
```
"帮我优化这段项目经历"
"把这个技术描述改成简历语言"
```

**适用对象：** 后端/前端开发工程师、架构师、技术专家

---

## 🚀 快速开始

### 使用 openskills 安装

```bash
# 安装工具
npm install -g openskills

# 安装 skills
openskills install code-review
openskills install resume-optimizer

# 同步到项目
openskills sync
```

详细安装说明请参考 [Cursor 使用指南](./cursor.md)

---

## 📖 资源

- [Claude Skills 官方文档](https://www.anthropic.com/news/skills)
- [Awesome Claude Skills](https://github.com/ComposioHQ/awesome-claude-skills) - 更多 skills 集合
- [创建自定义 Skills 指南](https://support.claude.com/en/articles/12512198-creating-custom-skills)