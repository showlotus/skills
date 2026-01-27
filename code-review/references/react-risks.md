# React 特定风险模式

## Hooks 使用问题

### 1. useEffect 依赖数组不完整 🪝

**风险等级**: High

**问题模式**:
```typescript
useEffect(() => {
  fetchData(userId);
}, []); // 缺少 userId 依赖
```

**风险**:
- 状态不同步
- 使用过期的闭包值
- 难以追踪的 bug

**建议**:
```typescript
useEffect(() => {
  fetchData(userId);
}, [userId]); // 包含所有依赖
```

### 2. useEffect 清理函数缺失 🧹

**风险等级**: High

**问题模式**:
```typescript
useEffect(() => {
  const timer = setInterval(() => {...}, 1000);
  window.addEventListener('resize', handler);
  // 没有清理
}, []);
```

**风险**:
- 内存泄漏
- 组件卸载后仍执行副作用
- 事件监听器累积

**建议**:
```typescript
useEffect(() => {
  const timer = setInterval(() => {...}, 1000);
  window.addEventListener('resize', handler);
  
  return () => {
    clearInterval(timer);
    window.removeEventListener('resize', handler);
  };
}, []);
```

### 3. useState 初始值计算未优化 ⚡

**风险等级**: Medium

**问题模式**:
```typescript
const [data, setData] = useState(expensiveCalculation());
```

**风险**:
- 每次渲染都会执行计算
- 性能浪费

**建议**:
```typescript
const [data, setData] = useState(() => expensiveCalculation());
```

### 4. 在循环/条件中使用 Hooks 💥

**风险等级**: Critical

**问题模式**:
```typescript
if (condition) {
  const [state, setState] = useState(0); // ❌
}

for (let i = 0; i < items.length; i++) {
  useEffect(() => {...}); // ❌
}
```

**风险**:
- 违反 Hooks 规则
- React 内部状态混乱
- 运行时错误

**建议**:
- Hooks 必须在函数组件顶层调用
- 不能在条件、循环、嵌套函数中使用

### 5. useCallback/useMemo 滥用或误用 🎯

**风险等级**: Medium

**问题模式**:
```typescript
// 过度优化
const value = useMemo(() => x + y, [x, y]);

// 依赖缺失
const callback = useCallback(() => {
  doSomething(prop);
}, []); // 缺少 prop
```

**建议**:
- 只在真正需要时使用(传递给子组件、昂贵计算)
- 确保依赖数组完整
- 不要为简单计算使用 useMemo

### 6. 自定义 Hook 命名不规范 📛

**风险等级**: Low

**问题模式**:
```typescript
function getUserData() { // ❌ 不是 use 开头
  const [data, setData] = useState(null);
  // ...
}
```

**建议**:
- 自定义 Hook 必须以 `use` 开头
- 例如: `useUserData`, `useFetch`, `useLocalStorage`

## 组件设计问题

### 7. Props drilling 过深 🕳️

**风险等级**: Medium

**问题模式**:
```typescript
<GrandParent>
  <Parent data={data}>
    <Child data={data}>
      <GrandChild data={data} /> {/* 透传多层 */}
    </Child>
  </Parent>
</GrandParent>
```

**建议**:
- 使用 Context API
- 使用状态管理库(Redux, Zustand)
- 组件组合模式

### 8. 组件内联定义导致重渲染 🔄

**风险等级**: Medium

**问题模式**:
```typescript
function Parent() {
  // 每次渲染都创建新组件
  const Child = () => <div>Child</div>;
  return <Child />;
}
```

**建议**:
- 组件定义移到外部
- 或使用 useMemo

### 9. 不必要的状态提升 📈

**风险等级**: Low

**问题模式**:
```typescript
// 父组件管理只有子组件用的状态
function Parent() {
  const [childState, setChildState] = useState();
  return <Child state={childState} setState={setChildState} />;
}
```

**建议**:
- 状态尽可能靠近使用位置
- 只在需要共享时才提升

### 10. Key 属性使用不当 🔑

**风险等级**: High

**问题模式**:
```typescript
// 使用索引作为 key
items.map((item, index) => <Item key={index} {...item} />)

// 使用随机值
items.map(item => <Item key={Math.random()} {...item} />)
```

**风险**:
- 列表更新时组件状态错乱
- 性能问题
- 不必要的重渲染

**建议**:
```typescript
// 使用稳定的唯一 ID
items.map(item => <Item key={item.id} {...item} />)
```

## 性能问题

### 11. 未使用 React.memo 优化子组件 ⚡

**风险等级**: Medium

**问题模式**:
```typescript
// 父组件状态变化导致子组件无意义重渲染
function ExpensiveChild({ data }) {
  // 复杂渲染逻辑
}
```

**建议**:
```typescript
const ExpensiveChild = React.memo(({ data }) => {
  // 复杂渲染逻辑
});
```

### 12. 在渲染中创建新对象/数组 📦

**风险等级**: Medium

**问题模式**:
```typescript
function Component() {
  return (
    <Child 
      style={{ margin: 10 }}  // 每次新对象
      items={[1, 2, 3]}        // 每次新数组
    />
  );
}
```

**建议**:
```typescript
const style = { margin: 10 };
const items = [1, 2, 3];

function Component() {
  return <Child style={style} items={items} />;
}
```

### 13. 大列表未虚拟化 📜

**风险等级**: High

**问题模式**:
```typescript
{items.map(item => <LargeItem key={item.id} {...item} />)} 
// items 有 1000+ 条
```

**建议**:
- 使用虚拟滚动库(react-window, react-virtualized)
- 或实现分页加载

## 副作用和数据获取

### 14. 在 useEffect 中未处理竞态条件 🏁

**风险等级**: High

**问题模式**:
```typescript
useEffect(() => {
  fetchData(id).then(data => setData(data));
}, [id]); // id 快速变化时可能出现问题
```

**风险**:
- 旧请求后返回覆盖新数据
- 状态不一致

**建议**:
```typescript
useEffect(() => {
  let cancelled = false;
  
  fetchData(id).then(data => {
    if (!cancelled) {
      setData(data);
    }
  });
  
  return () => {
    cancelled = true;
  };
}, [id]);
```

### 15. 异步更新状态未使用函数式更新 🔄

**风险等级**: Medium

**问题模式**:
```typescript
const [count, setCount] = useState(0);

const increment = () => {
  setTimeout(() => {
    setCount(count + 1); // 可能使用过期的 count
  }, 1000);
};
```

**建议**:
```typescript
const increment = () => {
  setTimeout(() => {
    setCount(prev => prev + 1); // 始终使用最新值
  }, 1000);
};
```

## 类型和 Props

### 16. Props 解构导致类型丢失 🎭

**风险等级**: Medium

**问题模式**:
```typescript
function Component({ data, ...rest }) {
  // rest 类型丢失
}
```

**建议**:
```typescript
interface ComponentProps extends BaseProps {
  data: DataType;
}

function Component({ data, ...rest }: ComponentProps) {
  // 类型完整
}
```

### 17. 事件处理函数内联定义 ⚡

**风险等级**: Low

**问题模式**:
```typescript
<button onClick={() => handleClick(id)}>Click</button>
// 每次渲染创建新函数
```

**建议**:
```typescript
const handleButtonClick = useCallback(() => {
  handleClick(id);
}, [id]);

<button onClick={handleButtonClick}>Click</button>
```

### 18. Context 值未使用 useMemo 🎯

**风险等级**: High

**问题模式**:
```typescript
function Provider({ children }) {
  const [state, setState] = useState();
  
  return (
    <MyContext.Provider value={{ state, setState }}>
      {children}
    </MyContext.Provider>
  ); // 每次渲染 value 都是新对象
}
```

**风险**:
- 所有 Consumer 无意义重渲染
- 性能严重下降

**建议**:
```typescript
function Provider({ children }) {
  const [state, setState] = useState();
  
  const value = useMemo(() => ({ state, setState }), [state]);
  
  return (
    <MyContext.Provider value={value}>
      {children}
    </MyContext.Provider>
  );
}
```

## 常见反模式

### 19. 在 JSX 中进行复杂计算 💭

**风险等级**: Low

**问题模式**:
```tsx
return (
  <div>
    {items.filter(x => x.active).map(x => x.value).join(', ')}
  </div>
);
```

**建议**:
```typescript
const activeValues = items
  .filter(x => x.active)
  .map(x => x.value)
  .join(', ');

return <div>{activeValues}</div>;
```

### 20. 直接修改 state 🚫

**风险等级**: Critical

**问题模式**:
```typescript
const [items, setItems] = useState([]);

items.push(newItem); // ❌ 直接修改
setItems(items);
```

**风险**:
- React 无法检测变化
- 组件不会重渲染
- 状态不一致

**建议**:
```typescript
setItems([...items, newItem]); // ✅ 创建新数组
// 或
setItems(prev => [...prev, newItem]);
```

