# RuiC-PoseAtlas：本地使用、上游同步与发布说明

## 项目位置与来源
- 本地工作目录：`/Users/sky/Desktop/EXM/Code/RuiC-PoseAtlas`
- 上游：`https://github.com/HRuiCcc/RuiC-PoseAtlas`
- 目标 GitHub 用户：`iamskyonline`
- 本地网页：`http://127.0.0.1:8123/`（启动服务后）

## 在 Mac 上使用
在终端执行：

```bash
cd /Users/sky/Desktop/EXM/Code/RuiC-PoseAtlas
npm run verify
npm run start
```

请不要直接双击 index.html：data/poses.json 需要通过 HTTP 加载。

## 同步机制
`.github/workflows/sync-upstream.yml` 每天约日本时间 10:27 检查上游 main 分支，也支持手动运行：
1. 使用普通 Git merge（禁止 force push/reset），保留本地定制；
2. 检查网站文件与上游 main 完全一致（HTML、JS、CSS、图片与数据等）；
3. 执行 `npm run verify` 校验姿态索引、图片、翻译；
4. 全部通过后才推送 fork 的 main；
5. 冲突/校验失败不推送，旧版保持原状。

网站文件比较（本机手动）：

```bash
git remote add upstream https://github.com/HRuiCcc/RuiC-PoseAtlas.git  # 已添加则跳过
git fetch upstream main
git diff --stat upstream/main -- index.html assets data img thumbs og-cover.jpg robots.txt .nojekyll
git rev-parse HEAD
git rev-parse upstream/main
```

## GitHub Pages（2026-10-29 已发布）
- 在线地址：`https://iamskyonline.github.io/RuiC-PoseAtlas/`
- GitHub Pages Source：GitHub Actions（`build_type=workflow`）。
- 原有 `deploy-pages.yml` 已启用，可通过 Actions 手动重新发布；网站文件改动推送到 `main` 也会触发部署。
- 仓库 Actions 变量 `ENABLE_PUBLIC_PAGES=true`，每日上游同步校验通过后可在同一条同步工作流内重新发布，避免依赖 GitHub Actions 机器人推送触发其他工作流。
- 验收：第一次部署工作流 `40888591797` 成功，页面访问 HTTP 200。
- 注意：这是公共 GitHub Pages 地址，具有网址的互联网用户均可访问，而 `127.0.0.1:8123` 仅在本机提供服务。
- 关闭公开部署时，应禁用 `deploy-pages.yml`、移除或设为 false 的 `ENABLE_PUBLIC_PAGES`，并在 GitHub Pages 设置中取消发布站点。

## GitHub 授权与维护
GitHub 登录使用 Mac 上的 `gh auth login --web`，在 GitHub 浏览器页面批准，不把账号密码或令牌放进仓库。
同步工作流需要 GitHub Actions 在 fork 仓库启用以及 `contents: write` 权限。
出现红色失败时，从 Actions 中查看 `Sync upstream (validated)` 的步骤日志，通常是 Git merge 冲突、校验失败、网络或推送权限问题。

## 本地一致性验收
- 文件数：`img/` 应有 1,439 张 PNG；`thumbs/` 应有 1,439 张 JPEG。
- 执行 `npm run verify`，结果为成功。
- `git diff upstream/main -- index.html assets data img thumbs og-cover.jpg robots.txt .nojekyll` 应为空。
- 浏览器检查分类、搜索、五视角切换、大图查看、下载和多语言切换。

生成说明：本文件仅记录当前部署策略与操作步骤，不改变上游网站内容。
