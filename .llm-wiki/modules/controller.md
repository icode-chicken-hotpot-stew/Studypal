---
title: Controller 模块
updated: 2026-09-19
sources:
  - lib/app_controller.dart
  - test/app_controller_xp_test.dart
  - test/app_controller_audio_test.dart
  - test/app_controller_supervisor_test.dart
---

# Controller 模块

`AppController` 维护业务状态并协调本地存储、音频和监督提醒。构造函数允许注入时间函数、AudioService、SupervisorNotificationService，供测试使用。

## 番茄钟

- 默认专注 1,500 秒、休息 300 秒。
- 时长边界：专注 5–300 分钟，休息 1–300 分钟。
- `cycleCount == null`：一轮专注和休息完成后回到 ready；有限次数最大为 100。
- 运行中时长保存在阶段快照里，后续修改配置不应直接重置当前阶段。
- `startTimer()`、`pauseTimer()`、`resetTimer()` 维护运行状态；`toggleTimer()` 为当前 UI 适配入口。

来源：`lib/app_controller.dart:L11-L20`、`L538-L665`、`L1141-L1228`。

## 成长

专注阶段完成后调用 `grantFocusXp()`：整分钟 × 10，低于 5 分钟为零，每日上限 2,000 XP；按累计阈值映射 Lv.1–Lv.10。

`xpToNextLevel` 与 `minutesToNextLevel` 为卷轴展示派生值。黑板只消费 XP 折算值，`fetchHistoryData()` 保留为兼容入口。

来源：`lib/app_controller.dart:L40-L51`、`L736-L740`、`L819-L878`。

## 对话

优先级从高到低：`completed`、`start_focus`、`resume`、`cold_start`、`clicked`、`idle`。

同级忽略，低级去重排队；只有前三类可以打断低优先级对话。资产文案按候选组等级筛选，每次选择一组；返回空候选时不展示。

来源：`lib/app_controller.dart:L230-L237`、`L1282-L1395`。详细格式见 [对话契约](../../docs/talking_interface_spec.md)。

## 服务协调

音乐方法持久化曲目与播放偏好。前后台生命周期控制音乐暂停／恢复，并在有效专注后台状态安排监督会话。返回、暂停、重置和退出专注阶段会取消监督提醒。

来源：`lib/app_controller.dart:L742-L817`、`L880-L950`。

## See Also

- [详细接口](../../docs/interface_spec.md)
- [数据流](../architecture/data-flow.md)
- [状态与持久化](../concepts/state-persistence.md)
- [书房 UI](study-room.md)
- [平台服务](services.md)
- [测试](../guides/testing.md)
