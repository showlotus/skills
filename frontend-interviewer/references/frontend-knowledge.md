# Frontend Knowledge Reference

前端技术知识点参考，用于面试官深度提问时查阅。

## 目录

1. [JavaScript 核心](#javascript-核心)
2. [TypeScript](#typescript)
3. [React 生态](#react-生态)
4. [Vue 生态](#vue-生态)
5. [CSS 与布局](#css-与布局)
6. [浏览器原理](#浏览器原理)
7. [性能优化](#性能优化)
8. [工程化](#工程化)
9. [网络协议](#网络协议)

---

## JavaScript 核心

### 数据类型与内存
- 基本类型 vs 引用类型的存储差异
- 深拷贝与浅拷贝实现
- 闭包的内存影响与垃圾回收
- WeakMap/WeakSet 与垃圾回收的关系

### 执行机制
- 事件循环（Event Loop）与宏任务/微任务
- 调用栈与执行上下文
- 变量提升与暂时性死区
- this 绑定规则（默认、隐式、显式、new）

### 异步编程
- Promise 实现原理与状态机
- async/await 的 Generator 实现
- Promise.all/race/allSettled/any 区别
- 异步错误捕获与异常穿透

### 原型与继承
- 原型链查找机制
- 继承的多种实现方式（寄生组合式最优）
- class 的底层实现
- Object.create 与 new 的区别

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "手写一个 Promise" |
| L4 | "Promise 的 then 链是如何实现回调地狱解决的？" |
| L5 | "如何实现 Promise 的中断和取消？" |

---

## TypeScript

### 类型系统
- any/unknown/never 的区别与使用场景
- 类型推断与类型断言
- 泛型的约束与推断
- 条件类型与 infer 关键字

### 类型编程
- 映射类型（Partial/Required/Readonly/Pick/Omit）
- 模板字面量类型
- 递归类型（DeepPartial/DeepReadonly）
- 类型体操：实现 ReturnType/Parameters

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "如何为一个复杂的 API 响应定义类型？" |
| L4 | "never 类型在什么场景下会自动推断出来？" |
| L5 | "如何实现一个类型工具，提取对象中值为函数的键？" |

---

## React 生态

### 核心机制
- Virtual DOM 与 Diff 算法（同层比较、key 的作用）
- Fiber 架构与时间切片
- 合成事件系统与事件池
- 批量更新机制

### Hooks
- useState/setState 的批量更新
- useEffect 的依赖数组与清理函数
- useMemo/useCallback 的缓存机制
- 自定义 Hook 的设计原则

### 状态管理
- Context 的性能问题与优化
- Redux 中间件机制（applyMiddleware）
- Zustand/Jotai/Recoil 的设计理念
- 状态管理方案选型依据

### 性能优化
- React.memo 与浅比较
- useMemo/useCallback 的正确使用
- 虚拟列表实现原理
- 代码分割与懒加载

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "useEffect 和 useLayoutEffect 的区别？" |
| L4 | "Fiber 架构是如何实现任务中断和恢复的？" |
| L5 | "如何设计一个支持撤销/重做的状态管理方案？" |
| L6 | "大型 React 应用的性能监控和优化策略？" |

---

## Vue 生态

### 响应式原理
- Vue 2：Object.defineProperty 的局限（数组、新增属性）
- Vue 3：Proxy 的优势与兼容性
- 依赖收集与派发更新的流程
- computed 与 watch 的实现差异

### 组件系统
- 组件通信方式（props/emit/provide-inject/Vuex）
- slot 的编译原理
- keep-alive 的缓存策略
- Teleport 的实现原理

### 编译优化
- 模板编译流程（parse → transform → codegen）
- 静态提升与 PatchFlag
- Tree-shaking 与副作用标记
- Vue 3 编译优化点

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "Vue 2 和 Vue 3 的响应式有什么区别？" |
| L4 | "computed 是如何实现惰性求值和缓存的？" |
| L5 | "如何实现一个自定义指令？指令的生命周期？" |
| L6 | "设计一个支持 SSR 的 Vue 组件库需要注意什么？" |

---

## CSS 与布局

### 盒模型与布局
- 标准盒模型 vs 怪异盒模型
- BFC 触发条件与应用场景
- Flex 布局常用属性
- Grid 布局与 Flex 的选型

### 响应式与适配
- 媒体查询与移动端适配方案
- rem/em/vw/vh 的计算与使用
- 1px 边框问题的解决方案
- 安全区域适配（env(safe-area-inset-*))

### 动画与性能
- GPU 加速的触发条件
- will-change 的使用与滥用
- requestAnimationFrame 的节流作用
- 动画性能监控（Long Tasks API）

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "如何实现一个水平垂直居中？" |
| L4 | "BFC 是什么？有什么应用场景？" |
| L5 | "如何优化动画性能？" |
| L6 | "如何设计一个 CSS 架构支持主题切换和多品牌？" |

---

## 浏览器原理

### 渲染流程
- DOM 树 → CSSOM 树 → 渲染树 → 布局 → 绘制 → 合成
- 重排与重绘的触发条件
- 图层合成与 GPU 加速
- requestAnimationFrame 的执行时机

### 资源加载
- 预加载（preload/prefetch/preconnect）
- 资源优先级与加载顺序
- 懒加载与IntersectionObserver
- 字体加载策略（font-display）

### 存储机制
- Cookie/LocalStorage/SessionStorage/IndexedDB
- Service Worker 与 Cache API
- PWA 的离线缓存策略

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "从输入 URL 到页面展示发生了什么？" |
| L4 | "浏览器是如何解析 HTML/CSS/JS 的？" |
| L5 | "如何诊断和优化首屏渲染性能？" |
| L6 | "如何设计一个高性能的 SPA 路由方案？" |

---

## 性能优化

### 加载优化
- 代码分割与动态 import
- Tree Shaking 原理与副作用标记
- 压缩策略（terser/brotli）
- CDN 与缓存策略

### 运行时优化
- 长列表虚拟化
- 防抖与节流
- Web Worker 处理 CPU 密集任务
- requestIdleCallback 的使用

### 监控指标
- Core Web Vitals（LCP/FID/CLS）
- FP/FCP/TTI/TBT
- 性能数据采集（Performance API）
- 错误监控与上报

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "常用的性能优化手段有哪些？" |
| L4 | "Webpack 的 Tree Shaking 是如何实现的？" |
| L5 | "如何设计一个前端性能监控系统？" |
| L6 | "在大型电商场景下，性能优化的完整策略是什么？" |

---

## 工程化

### 构建工具
- Webpack 核心概念（entry/output/loader/plugin）
- Vite 的快启动原理（esbuild + 原生 ESM）
- 模块化演进（CommonJS/ESM/UMD）
- SourceMap 类型与选择

### 代码规范
- ESLint 规则配置与插件开发
- Prettier 与 ESLint 的协作
- Husky + lint-staged 的工作流
- Commitlint 与约定式提交

### CI/CD
- GitHub Actions 工作流配置
- Docker 镜像构建优化
- 环境变量管理
- 灰度发布与回滚策略

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "Webpack 的 loader 和 plugin 有什么区别？" |
| L4 | "Vite 为什么比 Webpack 快？" |
| L5 | "如何设计一个 Monorepo 的构建缓存策略？" |
| L6 | "大型团队的前端工程化最佳实践是什么？" |

---

## 网络协议

### HTTP 协议
- HTTP/1.1 的队头阻塞问题
- HTTP/2 的多路复用与头部压缩
- HTTP/3 与 QUIC 协议
- HTTPS 的握手过程

### 缓存策略
- 强缓存（Cache-Control/Expires）
- 协商缓存（ETag/Last-Modified）
- 缓存位置（Service Worker/内存/磁盘）
- 缓存策略设计

### 安全
- XSS 攻击与防御（CSP）
- CSRF 攻击与防御（SameSite）
- CORS 预检请求与凭证
- CSP（内容安全策略）配置

### 深挖问题示例
| 层级 | 问题 |
|------|------|
| L3 | "HTTP 和 HTTPS 有什么区别？" |
| L4 | "HTTP/2 相比 HTTP/1.1 有哪些改进？" |
| L5 | "如何设计 API 的缓存策略？" |
| L6 | "如何防范大规模的前端安全攻击？" |
