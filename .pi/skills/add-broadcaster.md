# add-broadcaster

管理赛事转播方的 Skill。支持添加、更新、删除转播方信息。

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

### Step 4: 修改并提交

```bash
git checkout -b content/update-broadcaster-<名称>
git add data.js
git commit -m "content: update broadcaster - <名称>"
git push -u origin content/update-broadcaster-<名称>
```

### Step 5: 汇报

```
转播方已更新，预览链接：https://<commit>.ffxiv-race-stats.pages.dev
```
