# TypeScript 特定风险模式

## 类型安全问题

### 1. any 类型滥用 🚨

**风险等级**: High

**问题模式**:
```typescript
// 危险
const data: any = fetchData();
function process(param: any) { ... }
```

**建议**:
- 使用具体类型或泛型
- 必要时使用 `unknown` 代替 `any`
- 添加类型守卫进行运行时检查

### 2. 类型断言过度使用 ⚠️

**风险等级**: Medium

**问题模式**:
```typescript
const value = data as SomeType;
const element = document.getElementById('id') as HTMLElement;
```

**建议**:
- 确保断言的正确性
- 考虑使用类型守卫替代
- 添加运行时验证

### 3. 非空断言操作符 (!) 💥

**风险等级**: High

**问题模式**:
```typescript
const value = obj.property!;
user!.name = 'test';
```

**建议**:
- 使用可选链 `?.` 替代
- 添加显式的 null/undefined 检查
- 只在绝对确定的情况下使用

### 4. 隐式 any 📉

**风险等级**: Medium

**问题模式**:
```typescript
function process(data) { ... }  // 参数隐式 any
const items = [];  // any[]
```

**建议**:
- 为所有参数添加类型注解
- 为数组字面量指定类型
- 启用 `noImplicitAny` 配置

### 5. 类型兼容性问题 🔄

**风险等级**: Medium

**问题模式**:
```typescript
interface User { id: number; }
interface Admin { id: number; role: string; }
const user: User = admin;  // 可能丢失信息
```

**建议**:
- 明确类型关系 (extends/implements)
- 使用联合类型或交叉类型
- 添加类型品牌化 (nominal typing)

### 6. Promise 类型缺失 ⏱️

**风险等级**: Medium

**问题模式**:
```typescript
async function fetchData() {
  return fetch('/api');  // Promise<Response> 不明确
}
```

**建议**:
- 明确返回类型 `Promise<T>`
- 为异步函数添加完整类型注解
- 处理 rejection 情况

### 7. 枚举使用不当 🔢

**风险等级**: Low

**问题模式**:
```typescript
enum Status { Active, Inactive }  // 数字枚举可能混淆
const status = 1 as Status;  // 不安全
```

**建议**:
- 优先使用字符串枚举或字符串字面量联合类型
- 避免数字枚举的反向映射
- 考虑使用 `const enum`

### 8. 泛型约束缺失 🎯

**风险等级**: Medium

**问题模式**:
```typescript
function getValue<T>(obj: T, key: string) {
  return obj[key];  // 类型不安全
}
```

**建议**:
```typescript
function getValue<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

## React + TypeScript 特定问题

### 9. 组件 Props 类型缺失 ⚛️

**风险等级**: High

**问题模式**:
```typescript
const Component = ({ data, onSubmit }) => { ... }
```

**建议**:
```typescript
interface ComponentProps {
  data: DataType;
  onSubmit: (value: string) => void;
}
const Component: React.FC<ComponentProps> = ({ data, onSubmit }) => { ... }
```

### 10. Event 类型不明确 🖱️

**风险等级**: Medium

**问题模式**:
```typescript
const handleClick = (e) => { ... }
const handleChange = (e: any) => { ... }
```

**建议**:
```typescript
const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => { ... }
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => { ... }
```

### 11. Ref 类型不正确 📌

**风险等级**: Medium

**问题模式**:
```typescript
const ref = useRef();
const inputRef = useRef<HTMLInputElement>();
```

**建议**:
```typescript
const ref = useRef<HTMLDivElement>(null);
const inputRef = useRef<HTMLInputElement>(null);
// 访问时: inputRef.current?.focus()
```

### 12. Hook 返回值类型不明确 🪝

**风险等级**: Medium

**问题模式**:
```typescript
const [state, setState] = useState();
const [data, setData] = useState(null);
```

**建议**:
```typescript
const [state, setState] = useState<StateType | null>(null);
const [data, setData] = useState<DataType | undefined>(undefined);
```

