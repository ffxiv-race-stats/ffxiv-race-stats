# Cloudflare Pages 预览链接无内容

**发现时间**: 2026-06-21  
**发现者**: PI Agent (内容运营)  
**状态**: ✅ 已解决 — 分支别名格式有效，但子域名有长度截断

## 问题描述

PR #6 (`content/reorder-ranks-clear-first`) 的 Cloudflare Pages 预览部署未生效。

- **分支**: `content/reorder-ranks-clear-first`
- **Commit**: `4fd6976`
- **预期预览 URL（git hash 格式 — 错误）**: `https://4fd6976.ffxiv-race-stats.pages.dev`
- **预期预览 URL（分支别名格式 — 同样无效）**: `https://content-reorder-ranks-clear-first.ffxiv-race-stats.pages.dev`
- **现象**: 链接打开无内容（空白页）

## 根因分析

**设计文档 §5.4 的 URL 格式有误。** Cloudflare Pages 预览 URL 的 `<hash>` 是 Cloudflare 随机分配的**部署 ID**，不是 git commit hash。

Cloudflare Pages 实际提供两种预览 URL 格式：

| 格式 | 示例 | 可预测？ |
|------|------|---------|
| 分支别名 | `content-reorder-ranks-clear-first.ffxiv-race-stats.pages.dev` | ✅ 可根据分支名推算 |
| 部署 hash | `<random>.<project>.pages.dev` | ❌ 随机，只能从 API 获取 |

Agent 用 git short hash (`4fd6976`) 作为子域名构造 URL，Cloudflare 不认识这个子域名，返回空白页。

## 尝试修复：分支别名格式 — ✅ 有效，但有截断

分支别名完整名称为 `content-reorder-ranks-clear-first`，但 Cloudflare Pages 子域名存在长度限制，
实际会被截断为 `content-reorder-ranks-clear`（末尾 `-first` 被截掉）。

✅ 正确预览 URL：
```
https://content-reorder-ranks-clear.ffxiv-race-stats.pages.dev
```

❌ 完整分支名（预览无效）：
```
https://content-reorder-ranks-clear-first.ffxiv-race-stats.pages.dev
```

### 根因总结

1. **URL 格式错误**（设计文档 §5.4）：误用 git hash 而非分支名
2. **子域名长度截断**：Cloudflare Pages 分支别名截断到 **28 字符**（通过 API 实测确认）

| 分支 | 净化后 | 长度 | 实际别名 |
|------|--------|------|---------|
| `content/reorder-ranks-clear-first` | `content-reorder-ranks-clear-first` | 35 | `content-reorder-ranks-clear` ❌ |
| `chore/remove-claude-from-repo` | `chore-remove-claude-from-repo` | 30 | `chore-remove-claude-from-rep` ❌ |

`content-` 前缀占 8 字符，因此 `content/` 后面的部分 ≤ 20 字符即可避免截断。

### 后续改进

1. **分支命名约束**：`content/*` 分支名（`content/` 后部分）≤ 20 字符
2. `.pi/extensions/preview-url.ts` 已实现 28 字符截断逻辑
3. 设计文档 §3.2 和 §5.4 已更新

### 影响文件

1. `docs/operations-system-design.md` §5.4 — 修正 URL 格式描述
2. `.pi/extensions/preview-url.ts` — 更新骨架为分支别名格式
3. 所有 Skill 文件中的预览链接构造逻辑
4. Org README 中的预览说明
