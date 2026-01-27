# 安全风险检查清单

## 敏感信息泄露

### 1. 硬编码的密钥和凭证 🔐

**风险等级**: Critical

**问题模式**:
```typescript
const API_KEY = 'sk_live_abc123xyz'; // ❌
const DB_PASSWORD = 'mypassword123'; // ❌
const SECRET_TOKEN = 'secret-token-value'; // ❌
```

**风险**:
- 代码泄露导致系统被攻击
- 无法撤销已提交的凭证
- 合规问题

**建议**:
```typescript
// 使用环境变量
const API_KEY = process.env.API_KEY;
const DB_PASSWORD = process.env.DB_PASSWORD;

// 或使用密钥管理服务
const SECRET = await secretManager.getSecret('my-secret');
```

### 2. 敏感数据记录到日志 📝

**风险等级**: High

**问题模式**:
```typescript
console.log('User login:', { username, password }); // ❌
logger.info('Payment info:', paymentData); // ❌
```

**建议**:
- 移除或脱敏敏感字段
- 使用专门的日志过滤器
- 审计日志输出内容

### 3. 前端暴露敏感信息 🌐

**风险等级**: High

**问题模式**:
```typescript
// 在前端代码中
const adminToken = 'admin-secret-token';
const internalAPI = 'https://internal-api.company.com';

// 返回过多用户信息给前端
return user; // 包含密码哈希、内部 ID 等
```

**建议**:
- 前端只接收必要的公开信息
- 敏感配置通过后端传递
- API 响应进行字段白名单过滤

## 注入攻击

### 4. SQL 注入风险 💉

**风险等级**: Critical

**问题模式**:
```typescript
// 字符串拼接 SQL
const query = `SELECT * FROM users WHERE id = ${userId}`;
const sql = `DELETE FROM items WHERE name = '${itemName}'`;
```

**风险**:
- 恶意用户可执行任意 SQL
- 数据泄露或删除
- 权限提升

**建议**:
```typescript
// 使用参数化查询
const query = 'SELECT * FROM users WHERE id = ?';
db.execute(query, [userId]);

// 或使用 ORM
const user = await User.findById(userId);
```

### 5. 命令注入 💻

**风险等级**: Critical

**问题模式**:
```typescript
// 直接拼接命令
exec(`convert ${userInput}.jpg output.png`); // ❌
spawn('sh', ['-c', `echo ${userInput}`]); // ❌
```

**风险**:
- 任意命令执行
- 系统被完全控制

**建议**:
```typescript
// 验证和转义输入
const sanitized = escapeShellArg(userInput);
exec(`convert ${sanitized}.jpg output.png`);

// 使用数组参数而非 shell
spawn('convert', [`${userInput}.jpg`, 'output.png']);
```

### 6. XSS (跨站脚本攻击) 🎭

**风险等级**: Critical

**问题模式**:
```typescript
// React 中使用 dangerouslySetInnerHTML
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// 直接拼接 HTML
element.innerHTML = userInput;

// 未转义的用户输入
const html = `<div>${userContent}</div>`;
```

**风险**:
- 执行恶意脚本
- 窃取用户 cookie/token
- 页面劫持

**建议**:
```typescript
// React 自动转义
<div>{userInput}</div>

// 必须使用 dangerouslySetInnerHTML 时,先消毒
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ 
  __html: DOMPurify.sanitize(userInput) 
}} />
```

### 7. 路径遍历 📂

**风险等级**: High

**问题模式**:
```typescript
// 未验证的文件路径
const filePath = `/uploads/${req.params.filename}`;
fs.readFile(filePath); // 用户可传入 ../../etc/passwd
```

**建议**:
```typescript
import path from 'path';

const sanitized = path.basename(req.params.filename);
const filePath = path.join('/uploads', sanitized);

// 确保路径在允许的目录内
if (!filePath.startsWith('/uploads/')) {
  throw new Error('Invalid path');
}
```

## 认证和授权

### 8. 缺少认证检查 🚪

**风险等级**: Critical

**问题模式**:
```typescript
// API 路由未检查登录状态
app.get('/api/user/profile', (req, res) => {
  const profile = getUserProfile(req.params.id);
  res.json(profile); // ❌ 未验证用户身份
});
```

**建议**:
```typescript
app.get('/api/user/profile', requireAuth, (req, res) => {
  if (req.user.id !== req.params.id) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  const profile = getUserProfile(req.params.id);
  res.json(profile);
});
```

### 9. 越权访问 🔓

**风险等级**: Critical

**问题模式**:
```typescript
// 只检查登录,未检查权限
async function deleteUser(userId) {
  await User.delete(userId); // ❌ 普通用户也能删除
}

// 通过 ID 直接访问其他用户数据
const order = await Order.findById(req.params.orderId);
// 未检查 order.userId === req.user.id
```

**建议**:
```typescript
async function deleteUser(userId, currentUser) {
  if (!currentUser.isAdmin) {
    throw new Error('Unauthorized');
  }
  await User.delete(userId);
}

// 检查资源所属
const order = await Order.findById(req.params.orderId);
if (order.userId !== req.user.id && !req.user.isAdmin) {
  throw new Error('Forbidden');
}
```

### 10. JWT 使用不当 🎫

**风险等级**: High

**问题模式**:
```typescript
// 使用弱密钥
jwt.sign(payload, 'secret'); // ❌

// 未验证 token
const decoded = jwt.decode(token); // ❌ 应该用 verify

// 永不过期的 token
jwt.sign(payload, secret); // 未设置 expiresIn
```

**建议**:
```typescript
// 使用强密钥
jwt.sign(payload, process.env.JWT_SECRET, { 
  expiresIn: '1h',
  algorithm: 'HS256'
});

// 验证 token
try {
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
} catch (err) {
  // 处理无效 token
}
```

## CSRF 和会话安全

### 11. CSRF 防护缺失 🎯

**风险等级**: High

**问题模式**:
```typescript
// POST 请求未检查 CSRF token
app.post('/api/transfer', (req, res) => {
  transferMoney(req.body.to, req.body.amount); // ❌
});
```

**建议**:
```typescript
// 使用 CSRF 中间件
app.use(csrf());

// 或检查 Origin/Referer
app.post('/api/transfer', (req, res) => {
  const origin = req.headers.origin;
  if (!isAllowedOrigin(origin)) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  // 处理请求
});
```

### 12. Cookie 安全属性缺失 🍪

**风险等级**: High

**问题模式**:
```typescript
// 不安全的 cookie 设置
res.cookie('sessionId', value); // ❌
res.cookie('token', value, { httpOnly: false }); // ❌
```

**建议**:
```typescript
res.cookie('sessionId', value, {
  httpOnly: true,  // 防止 JS 访问
  secure: true,    // 只通过 HTTPS
  sameSite: 'strict', // 防 CSRF
  maxAge: 3600000  // 设置过期时间
});
```

## 数据验证

### 13. 输入验证缺失 ✅

**风险等级**: High

**问题模式**:
```typescript
// 直接使用用户输入
app.post('/api/user', (req, res) => {
  const user = new User(req.body); // ❌ 未验证
  user.save();
});
```

**建议**:
```typescript
import { z } from 'zod';

const userSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
});

app.post('/api/user', (req, res) => {
  const validated = userSchema.parse(req.body);
  const user = new User(validated);
  user.save();
});
```

### 14. 类型混淆攻击 🎭

**风险等级**: Medium

**问题模式**:
```typescript
// 弱类型比较
if (req.body.isAdmin == 'true') { // ❌ 可传入 boolean true
  grantAdminAccess();
}

// 对象注入
if (user.role === 'admin') { // 可传入 { role: 'admin' }
```

**建议**:
```typescript
// 严格类型检查
if (req.body.isAdmin === true) {
  grantAdminAccess();
}

// 验证数据类型
if (typeof user.role === 'string' && user.role === 'admin') {
```

## 依赖和配置

### 15. 使用有漏洞的依赖 📦

**风险等级**: High

**检查项**:
```bash
# 检查已知漏洞
npm audit
yarn audit

# 查看过时的包
npm outdated
```

**建议**:
- 定期更新依赖
- 使用 `npm audit fix`
- 关注安全公告
- 移除不必要的依赖

### 16. 敏感配置文件被提交 📄

**风险等级**: Critical

**问题模式**:
- `.env` 文件未加入 `.gitignore`
- `config.json` 包含生产环境密钥
- 私钥文件被提交

**建议**:
```gitignore
# .gitignore
.env
.env.local
.env.production
config/production.json
*.key
*.pem
secrets/
```

### 17. 调试模式在生产环境开启 🐛

**风险等级**: High

**问题模式**:
```typescript
app.set('debug', true); // ❌ 生产环境
process.env.NODE_ENV = 'development'; // ❌
```

**风险**:
- 暴露内部错误信息
- 性能下降
- 额外的攻击面

**建议**:
```typescript
const isDev = process.env.NODE_ENV === 'development';
app.set('debug', isDev);

// 生产环境使用通用错误消息
if (!isDev) {
  app.use((err, req, res, next) => {
    res.status(500).json({ error: 'Internal server error' });
  });
}
```

## CORS 和网络安全

### 18. CORS 配置过于宽松 🌍

**风险等级**: High

**问题模式**:
```typescript
// 允许所有来源
app.use(cors({ origin: '*' })); // ❌

// 反射 Origin
app.use((req, res) => {
  res.header('Access-Control-Allow-Origin', req.headers.origin); // ❌
});
```

**建议**:
```typescript
const allowedOrigins = [
  'https://myapp.com',
  'https://www.myapp.com'
];

app.use(cors({
  origin: (origin, callback) => {
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true
}));
```

### 19. 不安全的重定向 ↪️

**风险等级**: Medium

**问题模式**:
```typescript
// 未验证的重定向 URL
app.get('/redirect', (req, res) => {
  res.redirect(req.query.url); // ❌ 钓鱼风险
});
```

**建议**:
```typescript
const allowedDomains = ['myapp.com', 'subdomain.myapp.com'];

app.get('/redirect', (req, res) => {
  const url = new URL(req.query.url);
  if (allowedDomains.includes(url.hostname)) {
    res.redirect(url.toString());
  } else {
    res.status(400).json({ error: 'Invalid redirect URL' });
  }
});
```

### 20. 速率限制缺失 ⏱️

**风险等级**: Medium

**问题模式**:
```typescript
// 登录接口无限制
app.post('/api/login', loginHandler); // ❌ 可暴力破解
```

**建议**:
```typescript
import rateLimit from 'express-rate-limit';

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 分钟
  max: 5, // 最多 5 次尝试
  message: 'Too many login attempts'
});

app.post('/api/login', loginLimiter, loginHandler);
```

