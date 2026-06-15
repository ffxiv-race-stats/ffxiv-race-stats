---
name: update-team
description: >
  更新队伍攻略进度。当运营要求修改某支队伍的 phase、bossHP、isLive 状态时触发。
  触发词：更新队伍、修改进度、打到P几、血量、通关、灭团、isLive。
---

# update-team

## 输入

运营自然语言，例如：
- "队伍1 打到 P5 了，血量 12%"
- "Neverland 已经通关了"
- "把 t3 的 isLive 改成 false"

## 工作流

### Step 1: 读取当前状态

读取 `data.js` → 定位目标队伍 → 返回当前 phase / bossHP / isLive

### Step 2: 确认意图

向运营确认理解：
```
"确认：将 [队伍名]（当前 P3 / HP 45.8%）更新为 P5 / HP 12.0%，是否正确？"
```

### Step 3: 校验

- phase 是否在 PHASE_ORDER 中？新 phase 是否 ≥ 当前 phase？
- bossHP 是否 ≤ 当前 HP？（进度不倒退）
- 如果 HP = 0 且 phase = FINAL：自动标记 isLive = false，该队已通关
- 如果 HP < 30 且 phase 仍为 P1：提请运营确认是否数据有误

### Step 4: 修改

直接编辑 `data.js` 中该队伍的对应字段。

### Step 5: 提交到预览分支

**重要：此时只是推到 `content/*` 分支——生产站 `ffxiv-race-stats.pages.dev` 还不会变。**

```bash
git checkout -b content/update-<队伍id>-<新phase>
git add data.js
git commit -m "content: update <队伍名> to <新phase> (HP <新HP>%)"
git push -u origin content/update-<队伍id>-<新phase>
```

### Step 6: ⚠️ 输出合并提醒（必须执行，不可跳过）

Push 完成后，必须输出：

```
✅ 修改已推送，预览生效：https://<commit>.ffxiv-race-stats.pages.dev

⚠️ 生产站 https://ffxiv-race-stats.pages.dev 还没有更新。
   请打开预览链接确认无误后，回复"合并"。
   我会把改动合入 main 并自动部署到生产站。
```

**不收到运营回复"合并"，绝对不继续。**

### Step 7: 合并（收到运营确认后执行）

```bash
git checkout main
git merge content/update-<队伍id>-<新phase>
git push
git branch -D content/update-<队伍id>-<新phase>
git push origin --delete content/update-<队伍id>-<新phase>
```

然后汇报：
```
✅ 已合并到 main，生产站即将更新：https://ffxiv-race-stats.pages.dev
```
