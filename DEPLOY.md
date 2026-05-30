# 部署 CFnew Worker 到 Cloudflare Workers

## 准备工作

### 1. 安装 wrangler CLI（如果还没有安装）

```bash
npm install -g wrangler
```

### 2. 登录 Cloudflare

```bash
wrangler login
```

## 部署方式

### 方式一：使用 wrangler 命令行部署（推荐）

1. **部署 Worker**

```bash
wrangler deploy
```

2. **部署后配置环境变量**

   - 访问 [Cloudflare 控制台](https://dash.cloudflare.com/)
   - 进入 **Workers & Pages** → 选择 `cfnew-worker`
   - 点击 **设置** → **变量**
   - 添加以下环境变量：
     - `u`: 你的 UUID（必需）
     - `d`: 自定义路径（可选，如 `/mypath`）
     - 其他可选变量（详见 README.md）

### 方式二：在 Cloudflare 控制台手动部署

1. 访问 [Cloudflare 控制台](https://dash.cloudflare.com/)
2. 进入 **Workers & Pages**
3. 点击 **Create application** → **Create Worker**
4. 输入 Worker 名称（如 `cfnew-worker`）
5. 将 `index.js` 或 `少年你相信光吗` 的内容复制粘贴到编辑器中
6. 点击 **Save and Deploy**
7. 部署完成后，点击 **设置** → **运行时**
8. 设置 **兼容性日期** 为 `2026-01-20`
9. 配置环境变量（如 `u` 和可选变量）

### 方式三：使用 Pages 部署

1. 访问 [Cloudflare 控制台](https://dash.cloudflare.com/)
2. 进入 **Workers & Pages** → **Create application** → **Pages**
3. 上传包含项目文件的文件夹
4. 部署后配置兼容性日期和环境变量

## 图形化管理配置（推荐）

要使用图形化管理界面，需要创建并绑定 KV 命名空间：

1. 在 Cloudflare Workers 页面，点击 **KV** → **Create namespace**
2. 创建一个命名空间（例如 `cfnew-config`）
3. 回到你的 Worker 设置，点击 **变量** → **KV 命名空间绑定**
4. 添加绑定，变量名设为 `C`，选择刚才创建的命名空间
5. 重新部署 Worker

## 部署后访问

部署成功后，访问 `https://[your-worker].workers.dev/[your-uuid]` 使用图形化配置界面。

## 注意事项

- 兼容性日期必须设置为 `2026-01-20`
- `u` 变量（UUID）是必需的
- 可选配置更多变量可在 README.md 中查看
