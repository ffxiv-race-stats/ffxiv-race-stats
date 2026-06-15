# add-news

添加赛事速报的 Skill。当运营要求播报一条新闻或事件时触发。

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

在 `data.js` 的 `news[]` 数组头部插入新条目：
```js
{ id: "<max_id + 1>", time: "2026-06-15 18:30", title: "...", urgent: true/false }
```

### Step 4: 提交

```bash
git checkout -b content/add-news-<id>
git add data.js
git commit -m "content: add news - <标题>"
git push -u origin content/add-news-<id>
```

### Step 5: 汇报

```
速报已发布，预览链接：https://<commit>.ffxiv-race-stats.pages.dev
```
