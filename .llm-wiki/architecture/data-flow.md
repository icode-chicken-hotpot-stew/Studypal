---
title: 数据与事件流
updated: 2026-09-19
sources:
  - lib/main.dart
  - lib/app_controller.dart
  - lib/ui_widgets.dart
---

# 数据与事件流

## 启动

`main()` → `MainStage.initState()` → `AppController.initialize()` → `FutureBuilder` 展示书房。

Controller 依次恢复 XP、音乐偏好、监督会话和番茄快照，初始化服务，恢复时间线、清理旧监督会话，并按偏好播放 BGM、预热对话资产。来源：`lib/app_controller.dart:L345-L419`。

首次通知权限请求等待初始化与首帧完成后执行。来源：`lib/main.dart:L37-L56`。

## 计时

```text
播放按钮 → toggleTimer() → startTimer() / pauseTimer()
                          ↓
              状态、时间起点、快照更新
                          ↓
                每秒按真实时间计算剩余量
                          ↓
              完成专注 → 休息 → 下一轮或 ready
                          ├─ XP 结算
                          ├─ 阶段音效
                          └─ 对话与监督会话处理
```

阶段推进不是简单的“剩余秒数减一”。`_advanceSnapshotAfterElapsed()` 可以跨过多个已过期阶段，返回新快照与完成事件；恢复时不会补放历史音效。来源：`lib/app_controller.dart:L969-L1280`。

## 生命周期

- 后台：UI 入口转发生命周期；音乐暂停，前台 idle 计时停止；专注运行时安排监督会话。
- 返回：取消待发送监督通知，按偏好恢复音乐，再调用 `synchronizeWithCurrentTime()` 同步计时与对话。
- 重启：从持久化快照恢复并推进过期阶段。

来源：`lib/main.dart:L59-L79`、`lib/app_controller.dart:L476-L532`、`L742-L817`。

## 对话

事件 → `triggerDialogue()` → 可触发判断 → 仲裁／排队 → 加载并筛选等级候选 → 通知 UI → 气泡与角色动作。

来源：`lib/app_controller.dart:L667-L734`、`L1282-L1479`。角色事件另见 [JS 桥接](../apis/live2d-bridge.md)。

## See Also

- [系统总览](overview.md)
- [Controller](../modules/controller.md)
- [状态与持久化](../concepts/state-persistence.md)
