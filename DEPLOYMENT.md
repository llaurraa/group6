# 部署到 GitHub Pages 說明

由於 GitHub Personal Access Token 需要 `workflow` 權限才能推送 GitHub Actions，這裡提供兩種部署方案：

## 方案一：使用 GitHub 網頁界面創建 Workflow（推薦）

1. 進入你的 GitHub repository: https://github.com/llaurraa/group6

2. 點擊 **Settings** → **Pages**

3. 在 **Build and deployment** 下:
   - Source: 選擇 **GitHub Actions**

4. 點擊倉庫頁面的 **Actions** 標籤

5. 搜索 "Vite" 並選擇 **"Deploy static content to Pages"** workflow

6. 點擊 **Configure**，使用以下配置:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build
        env:
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

7. 點擊 **Commit changes**

8. （可選）如果需要 Gemini API:
   - 進入 **Settings** → **Secrets and variables** → **Actions**
   - 新增 secret: `GEMINI_API_KEY`

9. 遊戲將自動部署到: `https://llaurraa.github.io/group6/`

## 方案二：手動構建並部署（本地操作）

如果你已經安裝好 Node.js 和 npm:

1. 安裝依賴:
```bash
npm install
```

2. 構建項目:
```bash
npm run build
```

3. 安裝 gh-pages:
```bash
npm install -D gh-pages
```

4. 手動部署:
```bash
npm run deploy
```

## 方案三：更新 GitHub Token 權限

1. 進入 https://github.com/settings/tokens
2. 找到你正在使用的 Personal Access Token
3. 編輯並勾選 `workflow` 權限
4. 保存後，重新推送代碼即可

---

## 當前配置

項目已經配置好以下設置:
- ✅ Vite base path 設置為 `/group6/`
- ✅ Package.json 包含部署腳本
- ✅ .gitignore 排除 node_modules 和 dist

只需要選擇上面任一方案完成部署即可！
