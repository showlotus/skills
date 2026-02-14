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

### [Frontend Design](./frontend-design/) - 前端界面设计 🎨

打造独特、生产级的前端界面，避免千篇一律的 "AI 风格" 设计。

**核心能力：**
- 独特的字体排版，拒绝 Inter/Roboto 等通用字体
- 大胆的配色方案与主题设计
- 精心设计的动效与微交互
- 突破常规的空间布局（不对称、重叠、网格打破）
- 丰富的视觉细节（渐变、纹理、阴影、装饰边框）

**使用示例：**
```
"帮我设计一个着陆页"
"创建一个数据可视化仪表盘"
"把这个组件美化一下"
```

**技术栈：** HTML/CSS/JS, React, Vue 等

---

### [Vercel React Best Practices](./vercel-react-best-practices/) - React/Next.js 性能优化 ⚡

来自 Vercel 官方的 React 和 Next.js 性能优化最佳实践，涵盖 57 条规则。

**核心能力：**
- 消除瀑布流请求（async-parallel、Promise.all）
- Bundle 体积优化（动态导入、避免 barrel 文件）
- 服务端性能（React.cache()、LRU 缓存）
- 客户端数据获取（SWR 去重、事件监听优化）
- 重渲染优化（memo、useRef、startTransition）
- 渲染性能（SVG 优化、content-visibility）
- JavaScript 性能（循环优化、缓存策略）

**使用示例：**
```
"帮我优化这个 React 组件的性能"
"检查我的 Next.js 项目有没有性能问题"
"重构这段代码，遵循 Vercel 最佳实践"
```

**技术栈：** React, Next.js

---

### [NotebookLM](./notebooklm/) - NotebookLM 文档查询助手 📚

通过浏览器自动化直接查询 Google NotebookLM 笔记本，获取基于文档来源、带引用支持的 Gemini 回答。

**核心能力：**
- 浏览器自动化访问 NotebookLM
- 笔记本库管理（添加、搜索、激活、删除）
- 持久化认证（一次登录，多次使用）
- 智能追问机制，确保获取完整信息
- 基于文档的精准回答，大幅减少幻觉

**使用示例：**
```
"查询我的 NotebookLM 笔记本关于 XXX 的内容"
"把这个笔记本添加到库中"
"检查 NotebookLM 认证状态"
```

**技术栈：** Python, Patchright（浏览器自动化）

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