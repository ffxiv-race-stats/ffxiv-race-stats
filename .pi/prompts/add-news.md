---
description: 添加赛事新闻/速报
argument-hint: "<标题> <内容>"
---

# 添加赛事速报

向 `data.js` 的 `news[]` 数组添加一条新速报。

## 操作流程

1. 读取当前 `data.js`
2. 生成唯一 `id`（基于已有最大 id + 1）
3. 生成 `time`（当前时间，格式 `YYYY-MM-DD HH:mm`）
4. 确认 `urgent` 标记（默认 false，重大事件为 true）
5. 按时间降序插入 `news[]`
6. 自检：标题不超过 50 字，内容无敏感信息
7. 推送到 `content/add-news-<id>` 分支
8. 创建 PR（`gh pr create --base main`）
9. 告知运营预览链接 + PR 链接，等待确认
10. 收到确认后，CI 通过则 `gh pr merge --squash --delete-branch`
