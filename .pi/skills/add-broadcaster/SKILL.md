---
name: add-broadcaster
description: >
  管理赛事转播方。当运营要求添加、更新、删除转播方时触发。
  触发词：转播方、添加转播、删除转播、更新直播间、broadcaster。
---

# add-broadcaster

## 输入

运营自然语言，例如：
- "添加转播方：老陈 bilibili https://live.bilibili.com/12345"
- "删除转播方 青豆"
- "更新转播方 老陈 的直播间地址"

## 工作流

### Step 1: 读取当前广播列表

从 `data.js` 获取 `broadcasters[]` 当前内容。

### Step 2: 确认操作

向运营确认：
```
"确认 [添加/删除/更新] 转播方：
  名称：[name]
  平台：[platform]
  链接：[url]
是否执行？"
```

### Step 3: 校验

- `platform` ∈ `{ bilibili, douyu, huya, twitch, youtube }`
- `url` 格式正确（以 `https://` 开头）
- 添加时：`id` 基于最大已有 id + 1
- 无重复名称

### Step 4: 提交到预览分支

**重要：此时只是推到 `content/*` 分支——生产站还不会变。**

```bash
git checkout -b content/update-broadcaster-<名称>
git add data.js
git commit -m "content: update broadcaster - <名称>"
git push -u origin content/update-broadcaster-<名称>
```

### Step 5: ⚠️ 输出合并提醒（必须执行，不可跳过）

Push 完成后，必须输出：

```
✅ 转播方已推送，预览生效：https://<commit>.ffxiv-race-stats.pages.dev

⚠️ 生产站 https://ffxiv-race-stats.pages.dev 还没有更新。
   请打开预览链接确认无误后，回复"合并"。
   我会把改动合入 main 并自动部署到生产站。
```

**不收到运营回复"合并"，绝对不继续。**

### Step 6: 合并（收到运营确认后执行）

```bash
git checkout main
git merge content/update-broadcaster-<名称>
git push
git branch -D content/update-broadcaster-<名称>
git push origin --delete content/update-broadcaster-<名称>
```

然后汇报：
```
✅ 已合并到 main，生产站即将更新：https://ffxiv-race-stats.pages.dev
```
