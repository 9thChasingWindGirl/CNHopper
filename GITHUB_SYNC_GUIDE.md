# GitHub Actions 自动同步上游使用指南

## 概述

已配置 GitHub Actions 工作流 `sync-upstream.yml`，用于自动同步上游代码，同时保护你的自定义修改不会被覆盖。

---

## 工作原理

```
上游仓库 (main) → 自动同步 → 你的 main 分支
                                   ↓
                          rebase 到 custom/main 分支
                                   ↓
                    无冲突 → 自动推送 custom/main
                    有冲突 → 创建 PR 等待手动解决
```

---

## 已配置的功能

### 1. 自动同步（定时任务）
- **时间**: 每天 UTC 0:00（北京时间 8:00）自动运行
- **行为**: 自动拉取上游更新并 rebase 到 `custom/main` 分支

### 2. 手动触发
在 GitHub 仓库页面 → Actions → "Sync Upstream and Protect Custom Branch" → "Run workflow"

可手动指定：
- 上游仓库地址
- 上游分支
- 是否强制 rebase

### 3. 冲突处理
- 如果自动 rebase 无冲突 → 自动更新 `custom/main`
- 如果有冲突 → 创建 Pull Request，等待你手动解决

---

## 使用步骤

### 1. 确保 custom/main 分支已推送
你当前已经有 `custom/main` 分支了，确保已推送到 GitHub：

```bash
git checkout custom/main
git push -u origin custom/main
```

### 2. 日常开发工作流
```bash
# 始终在 custom/main 分支工作
git checkout custom/main

# 进行你的修改
git add .
git commit -m "your changes"
git push origin custom/main
```

### 3. 同步上游（自动或手动）
- **自动**: 每天早上会自动同步
- **手动**: 在 GitHub Actions 页面点击 "Run workflow"

---

## 解决冲突的步骤

如果 GitHub Actions 创建了冲突 PR：

### 方法一：在本地解决

```bash
# 1. 拉取最新代码
git fetch --all

# 2. checkout 到同步分支
git checkout sync-upstream-YYYYMMDD-HHMMSS

# 3. 解决冲突
git status  # 查看冲突文件
# 编辑冲突文件...

# 4. 标记冲突已解决
git add .
git rebase --continue

# 5. 推送到 custom/main
git checkout custom/main
git merge sync-upstream-YYYYMMDD-HHMMSS
git push origin custom/main

# 6. 关闭 PR（如果不需要）
```

### 方法二：在 GitHub 网页上解决

1. 打开 Pull Request
2. 点击 "Resolve conflicts"
3. 在网页编辑器中解决冲突
4. 标记为已解决
5. 合并 PR

---

## 仓库分支说明

| 分支 | 用途 | 可直接修改？ |
|------|------|-------------|
| `main` | 跟踪上游代码 | ❌ 不要直接修改 |
| `custom/main` | 你的自定义分支，用于部署 | ✅ 在这里工作 |

---

## 配置上游仓库（可选）

如果需要从其他上游仓库同步（而不是自己的 main）：

### 方法一：通过 GitHub Actions 手动触发
在 "Run workflow" 时填写上游仓库地址。

### 方法二：修改工作流文件
编辑 `.github/workflows/sync-upstream.yml`，修改默认值：

```yaml
upstream_repo:
  description: '上游仓库地址'
  default: 'https://github.com/ORIGINAL_OWNER/ORIGINAL_REPO.git'
```

---

## 部署到 Cloudflare Workers

始终从 `custom/main` 分支部署：

```bash
git checkout custom/main
wrangler deploy
```

---

## 常见问题

### Q: 我可以修改 main 分支吗？
A: 不建议！main 分支应该只用于跟踪上游代码，你的修改都在 custom/main。

### Q: 同步后我的修改会丢失吗？
A: 不会！工作流会先尝试 rebase，保留你的修改。如果有冲突，会创建 PR 让你手动解决。

### Q: 如何临时停止自动同步？
A: 在 GitHub 仓库 → Settings → Actions → 选择该工作流 → Disable workflow

---

## 相关文件

- [sync-upstream.yml](.github/workflows/sync-upstream.yml) - 同步工作流
- [GIT_WORKFLOW.md](GIT_WORKFLOW.md) - Git 工作流详细说明
- [DEPLOY.md](DEPLOY.md) - 部署指南
