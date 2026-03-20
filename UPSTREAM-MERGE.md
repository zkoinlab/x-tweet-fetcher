# x-tweet-fetcher 上游合并指南

_配置于 2026-03-20 | zkoinlab fork_

---

## 📦 Remote 配置

```bash
# 当前 remote
origin     https://github.com/openclaw/skills.git          # OpenClaw 官方技能库
upstream   https://github.com/ythx-101/x-tweet-fetcher.git # 原始作者
zkoinlab   https://github.com/zkoinlab/x-tweet-fetcher.git # 你的 fork
```

---

## 🔄 合并上游更新的流程

### 步骤 1：获取上游最新代码

```bash
cd ~/.openclaw/workspace/skills/openclaw-skills-temp/skills/hjw21century/x-tweet-fetcher
git fetch upstream
git fetch zkoinlab
```

### 步骤 2：检查上游变更

```bash
# 查看上游最新 commit
git log upstream/main --oneline -10

# 查看差异统计
git diff main upstream/main --stat

# 查看核心脚本变更
git diff main upstream/main -- scripts/fetch_tweet.py | head -100
```

### 步骤 3：创建合并分支

```bash
# 基于当前 main 创建合并分支
git checkout -b merge/upstream-$(date +%Y%m%d)

# 合并上游 main
git merge upstream/main
```

### 步骤 4：解决冲突（如有）

**可能冲突的文件：**
- `SKILL.md` - 我们的 Gotchas vs 上游文档更新
- `scripts/fetch_tweet.py` - 核心逻辑变更

**解决策略：**
```bash
# 如 SKILL.md 冲突，保留我们的 Gotchas 章节
git checkout --ours SKILL.md
# 然后手动合并上游的文档更新到我们的 Gotchas 后面

# 如脚本冲突，优先保留上游逻辑
git checkout --theirs scripts/fetch_tweet.py
```

### 步骤 5：测试验证

```bash
# 测试基本功能
python3 scripts/fetch_tweet.py --url "https://x.com/elonmusk/status/123456" --text-only

# 测试长文章
python3 scripts/fetch_tweet.py --url "https://x.com/user/status/123456" --pretty

# 检查 Gotchas 是否仍然有效
cat SKILL.md | grep -A50 "## ⚠️ Gotchas"
```

### 步骤 6：推送到你的 fork

```bash
# 推送到 zkoinlab remote
git push zkoinlab merge/upstream-$(date +%Y%m%d)

# 在 GitHub 上创建 PR 到 zkoinlab/main
# 或直接合并（如你有权限）
```

### 步骤 7：更新本地 main

```bash
git checkout main
git merge zkoinlab/main
git push origin main  # 如需同步到 OpenClaw 官方库
```

---

## 🤖 自动化检查（可选）

创建 Cron 任务每月检查上游更新：

```bash
# 检查上游是否有新 commit
git fetch upstream
LOCAL=$(git rev-parse main)
UPSTREAM=$(git rev-parse upstream/main)

if [ $LOCAL != $UPSTREAM ]; then
    echo "⚠️ 上游有新更新！"
    git log --oneline $LOCAL..$UPSTREAM
fi
```

---

## 📊 当前版本状态

| 项目 | 版本/状态 |
|------|----------|
| **本地版本** | 基于 openclaw/skills (旧) |
| **上游版本** | v1.9.0 + auto-fix 分支 |
| **你的 fork** | 刚创建，待同步 |
| **我们的增强** | Gotchas (8 条) + Verification |

---

## ⚠️ 注意事项

1. **Gotchas 保护** - 合并时优先保留我们的 Gotchas 章节
2. **核心脚本** - `fetch_tweet.py` 优先保留上游逻辑
3. **测试优先** - 合并后必须测试基本功能
4. **备份** - 合并前创建备份分支 `backup/pre-merge-$(date)`

---

## 🎯 下一步行动

1. [ ] 同步上游最新代码到你的 fork
2. [ ] 合并 Gotchas 增强到 fork
3. [ ] 测试完整功能
4. [ ] 更新本地技能指向 fork

---

_配置完成时间：2026-03-20 15:45 | 麦铁柱_
