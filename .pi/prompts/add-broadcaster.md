---
description: 管理转播方信息
argument-hint: "<添加|删除|更新> <转播方名>"
---

# 管理转播方

对 `data.js` 的 `broadcasters[]` 数组进行增删改。

## 操作流程

1. 读取当前 `data.js`
2. 根据操作类型（添加/删除/更新）定位或创建 broadcaster 记录
3. 必填字段：`name`、`platform`、`url`
4. 自检：platform 使用标准名称（bilibili/douyu/huya/twitch/youtube），url 可访问
5. 推送到 `content/update-broadcaster-<名称>` 分支
6. 告知运营预览链接
