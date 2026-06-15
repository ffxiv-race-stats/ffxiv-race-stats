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

### Step 4: 修改并提交（到预览分支）

**注意：此时修改只是推到了 `content/*` 分支，生产站还没有生效。**

```bash
git checkout -b content/update-broadcaster-<名称>
git add data.js
git commit -m "content: update broadcaster - <名称>"
git push -u origin content/update-broadcaster-<名称>
```

### Step 5: 汇报 + 索要合并确认 ⚠️ 必须执行

**这一步绝对不能跳过。** Push 完成后，必须向运营输出以下内容：

```
✅ 转播方已推送，预览生效：
   https://<commit>.ffxiv-race-stats.pages.dev

⚠️ 生产站 https://ffxiv-race-stats.pages.dev 还没有更新。
   请打开预览链接确认内容无误后，回复"合并"或"merge"。
   我会帮你把改动合入 main 分支并自动部署到生产站。
```

**在没有收到运营的"合并"确认之前，不要继续。**

### Step 6: 合并（需运营确认）

运营确认后执行：
```bash
git checkout main
git merge content/update-broadcaster-<名称>
git push
git branch -D content/update-broadcaster-<名称>
git push origin --delete content/update-broadcaster-<名称>
```

然后汇报：
```
✅ 已合并到 main，生产站即将自动更新：https://ffxiv-race-stats.pages.dev
```
