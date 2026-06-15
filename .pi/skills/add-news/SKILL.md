---
name: add-news
description: >
  添加赛事速报。当运营要求发布新闻、速报、公告时触发。
  触发词：发速报、发布新闻、公告、快讯、播报。
---

# add-news

## 输入

运营自然语言，例如：
- "发一条速报：Neverland 世界首杀，用时 38 小时"
- "公告：服务器维护，明天下午 2 点恢复"

## 工作流

### Step 1: 提取信息

从运营输入中提取：
- `title`：简洁标题（≤50 字）
- `urgent`：是否标记为紧急（首杀/重大事件）
- `time`：自动生成当前时间

### Step 2: 确认

向运营确认：
```
"确认添加速报：
  标题：[提取的标题]
  时间：2026-06-15 18:30
  紧急：是/否
是否发布？"
```

### Step 3: 修改

在 `data.js` 的 `news[]` 数组头部插入新条目。

### Step 4: 提交到预览分支

**重要：此时只是推到 `content/*` 分支——生产站还不会变。**

```bash
git checkout -b content/add-news-<id>
git add data.js
git commit -m "content: add news - <标题>"
git push -u origin content/add-news-<id>
```

### Step 5: ⚠️ 输出合并提醒（必须执行，不可跳过）

Push 完成后，必须输出：

```
✅ 速报已推送，预览生效：https://<commit>.ffxiv-race-stats.pages.dev

⚠️ 生产站 https://ffxiv-race-stats.pages.dev 还没有更新。
   请打开预览链接确认无误后，回复"合并"。
   我会把改动合入 main 并自动部署到生产站。
```

**不收到运营回复"合并"，绝对不继续。**

### Step 6: 合并（收到运营确认后执行）

```bash
git checkout main
git merge content/add-news-<id>
git push
git branch -D content/add-news-<id>
git push origin --delete content/add-news-<id>
```

然后汇报：
```
✅ 已合并到 main，生产站即将更新：https://ffxiv-race-stats.pages.dev
```
