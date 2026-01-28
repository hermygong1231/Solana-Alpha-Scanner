# 🚀 部署指南

本文档介绍如何将 Solana Alpha Scanner 部署到各种平台。

---

## 📋 目录

- [GitHub Pages](#github-pages)
- [Vercel](#vercel)
- [Netlify](#netlify)
- [自定义服务器](#自定义服务器)

---

## GitHub Pages

### 方法一：通过 GitHub 网页操作

1. **创建 GitHub 仓库**
   ```bash
   # 在 GitHub 上创建新仓库：Solana-Alpha-Scanner
   ```

2. **推送代码**
   ```bash
   cd Solana-Alpha-Scanner
   git init
   git add .
   git commit -m "Initial commit: Solana Alpha Scanner"
   git branch -M main
   git remote add origin https://github.com/你的用户名/Solana-Alpha-Scanner.git
   git push -u origin main
   ```

3. **启用 GitHub Pages**
   - 进入仓库设置：`Settings` → `Pages`
   - Source: 选择 `main` 分支
   - 点击 `Save`
   - 等待 1-2 分钟

4. **访问网站**
   ```
   https://你的用户名.github.io/Solana-Alpha-Scanner
   ```

### 方法二：使用 GitHub Actions（可选）

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./
```

---

## Vercel

### 步骤

1. **安装 Vercel CLI**
   ```bash
   npm i -g vercel
   ```

2. **部署**
   ```bash
   cd Solana-Alpha-Scanner
   vercel
   ```

3. **按提示操作**
   - 登录 Vercel 账号
   - 选择项目配置
   - 等待部署完成

4. **访问网站**
   Vercel 会自动分配一个域名：`https://你的项目名.vercel.app`

### 配置文件（可选）

创建 `vercel.json`：

```json
{
  "version": 2,
  "builds": [
    {
      "src": "index.html",
      "use": "@vercel/static"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/$1"
    }
  ]
}
```

---

## Netlify

### 方法一：通过 Web 界面

1. 访问 [Netlify](https://netlify.com)
2. 点击 `Add new site` → `Deploy manually`
3. 拖拽整个 `Solana-Alpha-Scanner` 文件夹
4. 等待部署完成
5. 访问分配的域名

### 方法二：通过 Git 连接

1. 在 Netlify 选择 `Import from Git`
2. 连接你的 GitHub 仓库
3. 配置构建设置：
   - Build command: 留空
   - Publish directory: `.`
4. 点击 `Deploy site`

### 配置文件（可选）

创建 `netlify.toml`：

```toml
[build]
  publish = "."

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

---

## 自定义服务器

### Nginx

1. **上传文件**
   ```bash
   scp -r Solana-Alpha-Scanner/* user@your-server:/var/www/html/solana-scanner/
   ```

2. **配置 Nginx**
   ```nginx
   server {
       listen 80;
       server_name your-domain.com;
       root /var/www/html/solana-scanner;
       index index.html;

       location / {
           try_files $uri $uri/ /index.html;
       }

       # 启用 gzip 压缩
       gzip on;
       gzip_types text/html text/css application/javascript;
   }
   ```

3. **重启 Nginx**
   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### Apache

创建 `.htaccess`：

```apache
RewriteEngine On
RewriteBase /
RewriteRule ^index\.html$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.html [L]

# 启用压缩
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/css application/javascript
</IfModule>
```

---

## 🌐 自定义域名

### GitHub Pages

在仓库根目录创建 `CNAME` 文件：

```
your-domain.com
```

在域名服务商添加 DNS 记录：

```
Type: CNAME
Name: www (或 @)
Value: 你的用户名.github.io
```

### Vercel/Netlify

1. 进入项目设置 → Domains
2. 添加自定义域名
3. 按提示配置 DNS 记录

---

## ⚡ 性能优化

### 1. 启用 CDN

使用 [Cloudflare](https://cloudflare.com) 加速：

1. 添加你的域名到 Cloudflare
2. 修改 DNS 服务器为 Cloudflare 提供的服务器
3. 开启自动缓存和压缩

### 2. 压缩资源

```bash
# 压缩 HTML
npm install -g html-minifier
html-minifier --collapse-whitespace --remove-comments index.html -o index.min.html

# 如果项目有独立的 CSS/JS
npm install -g uglify-js clean-css-cli
uglifyjs script.js -o script.min.js
cleancss style.css -o style.min.css
```

### 3. 启用缓存

在服务器配置中添加缓存头：

```nginx
location ~* \.(html|css|js)$ {
    expires 1d;
    add_header Cache-Control "public, immutable";
}
```

---

## 🔧 环境变量配置

如果你使用付费 RPC 节点，建议使用环境变量：

### 本地开发

创建 `.env.local`（不要提交到 Git）：

```bash
SOLANA_RPC_URL=https://your-helius-or-quicknode-url
```

在 `index.html` 中读取（需要简单修改）：

```javascript
// 如果使用构建工具
const rpcUrl = process.env.SOLANA_RPC_URL || 'https://api.mainnet-beta.solana.com';
```

### Vercel 环境变量

在 Vercel Dashboard → Settings → Environment Variables 中添加：

```
SOLANA_RPC_URL = https://your-custom-rpc-url
```

---

## 🐛 常见问题

### 1. 页面部署后无法访问
- 检查文件名是否为 `index.html`
- 确认服务器配置正确
- 查看浏览器控制台错误

### 2. RPC 连接失败
- 更换 RPC 节点
- 检查网络连接
- 确认浏览器允许 HTTPS → HTTP 请求（使用 HTTPS RPC）

### 3. GitHub Pages 404 错误
- 确认仓库设置中 Pages 已启用
- 等待 1-2 分钟让部署生效
- 检查分支名称是否正确

---

## 📊 监控和分析

### Google Analytics

在 `index.html` 的 `<head>` 标签中添加：

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=YOUR_GA_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'YOUR_GA_ID');
</script>
```

### Vercel Analytics

Vercel 项目自动包含分析功能，在 Dashboard 中查看。

---

## 🔐 安全建议

1. **不要暴露私钥**：永远不要在前端代码中硬编码私钥
2. **使用 HTTPS**：确保网站使用 HTTPS
3. **CSP 头**：添加 Content Security Policy
4. **定期更新依赖**：检查 @solana/web3.js 版本

---

## 📞 需要帮助？

遇到部署问题？

- 查看 [GitHub Issues](https://github.com/hermygong/Solana-Alpha-Scanner/issues)
- 发送邮件到：your-email@example.com

---

**祝你部署成功！🎉**
