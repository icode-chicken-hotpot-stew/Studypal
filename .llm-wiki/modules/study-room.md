---
title: 书房 UI 模块
updated: 2026-09-19
sources:
  - lib/ui_widgets.dart
  - test/chat_bubble_test.dart
---

# 书房 UI 模块

`UIWidgets` 用 Flutter Stack 组合书房。UI 本地保存展开、缩放、音量交互等展示状态，业务状态读取 [Controller](controller.md)。

## 舞台与面板

舞台层次为：`background_back.png` → CharacterView → `background_front.png` → 对话与控制面板。来源：`lib/ui_widgets.dart:L380-L418`。

| 入口 | 组件职责 |
| :--- | :--- |
| 左上番茄 | 计时、播放／暂停、重置与配置 |
| 成长卷轴 | 当前等级、升级所需 XP 与分钟数 |
| 右上黑板 | 今日／累计 XP 折算时长、关于我们 |
| 左下唱片机 | 播放、切歌、音量 |
| 右下气泡 | 场景对话、打字机、下一句与跳过 |

来源：`lib/ui_widgets.dart:L422-L1049`。顶部各面板展开时互斥，点击空白关闭面板并前进对话或登记用户活动。

## 配置与时间

进度由 `remainingSeconds` 与 `currentPhaseDurationSeconds` 推导。专注／休息配置以分钟展示，传入 Controller 前转为秒。

加减按钮即时生效；运行时禁止编辑，暂停时可调整后续阶段配置。「取消」与「保存」都只关闭面板。来源：`lib/ui_widgets.dart:L51-L91`、`L145-L199`、`L632-L750`。

## ChatBubble

- 每 80ms 显示一个字符。
- 点击未完成文本补全；完成后点击前进下一句。
- 正常完成或点击补全后启动 8 秒自动前进定时器。
- 快进按钮先补到下一句末标点，全文展示后调用 `onSkip`。
- 快进分支到末尾不额外启动自动前进定时器。

来源：`lib/ui_widgets.dart:L1422-L1629`。

## 角色联动

UI 将 `pomodoroState` 和 `isTalking` 传给 CharacterView；点击事件调用 `triggerDialogue('clicked')`，出场事件安排冷启动对话。

来源：`lib/ui_widgets.dart:L337-L378`。

## See Also

- [Controller](controller.md)
- [Live2D](live2d.md)
- [系统总览](../architecture/overview.md)
- [对话说明](../../docs/talking_interface.md)
