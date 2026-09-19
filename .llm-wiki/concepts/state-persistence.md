---
title: 状态与本地持久化
updated: 2026-09-19
sources:
  - lib/app_controller.dart
  - lib/main.dart
---

# 状态与本地持久化

## 两个独立维度

| 组合 | 语义 |
| :--- | :--- |
| resting + ready | 待开始 |
| studying + running | 专注计时中 |
| studying + paused | 专注暂停 |
| resting + running | 休息计时中 |
| resting + paused | 休息暂停 |

`isActive` 是运行状态的兼容展示值，新控制逻辑应优先消费 `phaseStatus`。来源：`lib/app_controller.dart:L53-L55`、`L1258-L1280`。

## 快照

`_PomodoroSnapshot` 包括业务阶段、运行状态、startedAt、当前阶段总时长／剩余量、配置时长、循环次数与已完成轮数。

运行时用起点和实际经过时间计算剩余量；暂停后清除起点，保留剩余秒数。重启读取快照，规整无效配置后推进过期阶段。来源：`lib/app_controller.dart:L66-L167`、`L577-L608`、`L1050-L1139`。

## 存储键

| 前缀／键 | 用途 |
| :--- | :--- |
| `pomodoro.snapshot` | JSON 计时快照 |
| `xp.total`、`xp.daily`、`xp.lastDate`、`xp.level` | 成长与跨日计算 |
| `music.*` | 自动播放、播放偏好、曲目、音量 |
| `supervisor.*` | 监督会话、后台时间、阶段标记、首次权限提示记录 |

来源：`lib/app_controller.dart:L22-L37`。

数据保存在当前设备的 SharedPreferences 中。包名、存储键与原生通道标识均有兼容意义，文档整理不需要更改它们。

## See Also

- [Controller](../modules/controller.md)
- [数据流](../architecture/data-flow.md)
- [详细接口](../../docs/interface_spec.md)
