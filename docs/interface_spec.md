# 陪伴学习软件 - 项目接口规范手册

> **版本**: v0.1.0 实现基线
> **最后更新**: 2026-09-19
> **适用对象**: 开发团队成员、AI 助手 (Claude)
> **协作原则**: 接口契约优先，模块内部实现自治
>
> **状态说明**: 本文描述已发布版本的接口。当前行为以源码为准；OpenSpec 记录设计目标与验收进度，差异见 [维护状态](maintenance.md)。

---

## 1. 项目概述

这是一个以横屏陪伴学习场景为核心的 Flutter 应用。默认入口是 `lib/main.dart`，由 `MainStage` 创建共享 `AppController`，并将其注入 UI 与其他消费方。

当前仓库应优先相信以下事实：
- 番茄钟核心状态、恢复与配置逻辑以 `lib/app_controller.dart` 为准。
- 主界面交互与番茄钟消费逻辑以 `lib/ui_widgets.dart` 为准。
- 主入口初始化与生命周期恢复以 `lib/main.dart` 为准。
- 角色动画层 `lib/character_view.dart` 已通过 WebView 接入本地 Live2D 模型、动作与交互桥接。

---

## 2. 当前权威来源

### 2.1 番茄钟

以下文件用于核对番茄钟的设计与实现；发生冲突时，以源码确认当前行为：
- `openspec/changes/improve-pomodoro-functionality/proposal.md`
- `openspec/changes/improve-pomodoro-functionality/design.md`
- `openspec/changes/improve-pomodoro-functionality/specs/`
- `openspec/changes/improve-pomodoro-functionality/tasks.md`
- `lib/app_controller.dart`
- `lib/ui_widgets.dart`
- `lib/main.dart`

### 2.2 对话系统

以下文件构成当前对话系统主要依据：
- `lib/app_controller.dart`
- `lib/ui_widgets.dart`
- `assets/dialogues/dialogues.json`
- `docs/talking_interface.md`

---

## 3. 当前架构职责

### 3.1 `lib/main.dart`
- 创建共享 `AppController`
- 在 `MainStage.initState()` 中尽早调用 `controller.initialize()`
- 用 `FutureBuilder` 保证恢复完成后再进入正常 UI
- 转发生命周期事件到 controller

### 3.2 `lib/app_controller.dart`
- 维护番茄钟、对话、XP、音乐、监督提醒等状态
- 负责番茄钟状态机、持久化、恢复与时间同步
- 通过 `ValueNotifier` 暴露高频/简单状态，通过 `ChangeNotifier` 暴露复合状态
- 对外提供显式行为接口，UI 不应直接改内部状态

### 3.3 `lib/ui_widgets.dart`
- 负责主界面展示与交互转发
- 通过 `ValueListenableBuilder` / `ListenableBuilder` 消费 controller 状态
- 顶部进度、倒计时、配置输入与控制按钮都应尽量只消费 controller contract

### 3.4 `lib/character_view.dart`
- 接收 `pomodoroState` 和 `isTalking`，加载 `assets/live2d/hiyori_viewer.html` 与默认 `hiyori_pro` 模型。
- 将 `studying` 映射为 `study`，`resting` 映射为 `normal`；对话开始时请求 `Talk` 动作。
- `onCharacterTap` 回调触发点击对话，`onEntranceMotionStarted` 回调安排冷启动对话。
- 通过 AssetLoader / BinaryAssetLoader 加载文本与二进制资源，Live2DController 接收 JS 事件。

---

## 4. 当前番茄钟状态契约

### 4.1 核心状态

当前番茄钟相关核心状态包括：

| 状态 | 类型 | 说明 |
| :--- | :--- | :--- |
| `remainingSeconds` | `ValueNotifier<int>` | 当前阶段剩余秒数 |
| `pomodoroState` | `ValueNotifier<PomodoroState>` | 业务阶段语义：`resting` / `studying` |
| `phaseStatus` | `ValueNotifier<PomodoroPhaseStatus>` | 运行控制语义：`ready` / `running` / `paused` |
| `focusDurationSeconds` | `ValueNotifier<int>` | 专注时长配置，默认 `1500` |
| `restDurationSeconds` | `ValueNotifier<int>` | 休息时长配置，默认 `300` |
| `cycleCount` | `ValueNotifier<int?>` | 有限循环次数，`null` 表示不循环 |
| `completedFocusCycles` | `ValueNotifier<int>` | 当前 session 已完成的专注轮数 |
| `isDrawerOpen` | `ValueNotifier<bool>` | UI 抽屉/面板状态 |
| `currentDate` | `ValueNotifier<String>` | 顶部日期显示 |

### 4.2 状态职责边界

- `pomodoroState`：只表达业务阶段语义，供动画、对话、陪伴行为消费。
- `phaseStatus`：只表达运行控制语义，供按钮态、恢复、持久化与控制逻辑消费。
- `remainingSeconds`：表达当前阶段剩余时间。
- `isActive`：当前代码中仍存在，但对番茄钟正式控制 contract 来说属于兼容性状态；新逻辑应优先依赖 `phaseStatus`。

### 4.3 固定组合解释

| 组合 | 语义 |
| :--- | :--- |
| `resting + ready` | 待开始 / 下一轮专注未开始 |
| `studying + running` | 学习中 |
| `studying + paused` | 学习暂停 |
| `resting + running` | 休息中 |
| `resting + paused` | 休息暂停 |

---

## 5. 当前番茄钟公开接口

### 5.1 核心方法

| 方法 | 说明 |
| :--- | :--- |
| `initialize()` | 启动时恢复配置与番茄钟运行快照 |
| `startTimer()` | 从 ready 启动专注，或从 paused 恢复当前阶段 |
| `pauseTimer()` | 暂停当前运行阶段 |
| `resetTimer()` | 保留用户配置，回到 ready 状态并清零本次已完成轮数 |
| `updateFocusDuration(int seconds)` | 更新专注时长配置 |
| `updateRestDuration(int seconds)` | 更新休息时长配置 |
| `updateCycleCount(int? count)` | 更新循环次数配置 |
| `restoreDefaultDurations()` | 恢复默认 25/5 配置 |
| `synchronizeWithCurrentTime()` | 生命周期恢复后同步时间与阶段 |
| `handleLifecycleStateChanged(...)` | 处理应用生命周期变化 |
| `handleAppBackgrounded()` | 标记后台态并停止前台相关行为 |
| `fetchHistoryData()` | 统计入口兼容方法；黑板实际消费 XP 折算值，尚无历史明细查询 |

### 5.2 兼容性方法

| 方法 | 说明 |
| :--- | :--- |
| `toggleTimer()` | 兼容性封装：运行中时委托 `pauseTimer()`，否则委托 `startTimer()` |

---

## 6. 当前 UI / Controller 协作规则

- UI 只读 controller 状态。
- UI 只通过 controller 公共方法表达用户意图。
- UI 不应再维护第二套“真实剩余时间”或“真实进度”。
- 顶部进度应由 `remainingSeconds` 与 `currentPhaseDurationSeconds` 推导。
- 配置输入应统一走 `updateFocusDuration` / `updateRestDuration` / `updateCycleCount`。
- 按钮态与恢复语义应优先读取 `phaseStatus`，不要再把 `isActive` 作为新 contract 的唯一依据。
- 当前 UI 是播放／暂停切换按钮加独立重置按钮；配置加减即时生效，「取消」不回滚配置。
- 专注范围 5–300 分钟，休息范围 1–300 分钟；循环 UI 的 `0` 转为 `null`，有限总轮数为 1–100。

---

## 7. 当前启动与恢复路径

### 7.1 启动

```dart
class _MainStageState extends State<MainStage> with WidgetsBindingObserver {
  late final AppController controller;
  late final Future<void> _initialization;

  @override
  void initState() {
    super.initState();
    controller = AppController();
    _initialization = controller.initialize();
  }
}
```

### 7.2 首帧保护

```dart
FutureBuilder<void>(
  future: _initialization,
  builder: (context, snapshot) {
    if (snapshot.connectionState != ConnectionState.done) {
      return const Center(child: CircularProgressIndicator());
    }
    return UIWidgets(controller: controller);
  },
)
```

### 7.3 生命周期恢复

- app 切回前台后，`MainStage` 会把生命周期事件转发给 controller。
- controller 负责重新同步当前时间、剩余秒数和阶段，不由 UI 自己推算恢复值。

---

## 8. 当前对话与角色联动口径

- 对话触发、队列、仲裁与文案加载以 `lib/app_controller.dart` 为准。
- 角色主动作语义应优先读取 `pomodoroState`：
  - `studying` → 学习动作
  - `resting` → 休息/待机动作
- 若未来需要区分 `resting + ready` 与 `resting + running`，必须再结合 `phaseStatus`。
- 对话变化由 `ChangeNotifier` 通知 UI；角色通过 `isTalking` 触发 Talk 动作。桥接细节见 [Live2D 模块](../.llm-wiki/modules/live2d.md)。

---

## 9. 当前已知非目标 / 非权威内容

以下内容当前不应被当作番茄钟正式 contract：
- `startFocus()` / `finishFocus()` 一类旧方法名
- `focusStartTime` 单字段式旧恢复模型
- `SharedPreferences / Hive` 并列作为当前正式持久化决策
- 用 `isActive` 单独表达全部番茄钟运行语义
- 历史统计、分享卡片最终数据模型

---

## 10. 成长、音频与通知接口

| 分组 | 状态／方法 | 说明 |
| :--- | :--- | :--- |
| 成长 | `totalXp`、`dailyXp`、`level`、`justLeveledUp` | XP 与等级 Notifier |
| 成长 | `grantFocusXp({required int effectiveFocusSeconds, DateTime? occurredAt})` | 返回 `Future<int>`；按完成时间入账，每分钟 10 XP，低于 5 分钟不发放，每日上限 2,000 |
| 成长 | `xpToNextLevel`、`minutesToNextLevel` | 卷轴展示所需的只读派生值 |
| 对话 | `canUnlockDialogue(int requiredLevel)` | 按候选组的等级门槛判断解锁 |
| 音乐 | `isMusicPlaying`、`musicAutoPlayEnabled`、`currentTrackIndex`、`musicVolume` | 播放偏好与状态 |
| 音乐 | `playOrPauseMusic()`、`playNextTrack()`、`playPreviousTrack()` | 返回 `Future<void>`，通过 AudioService 控制播放 |
| 音乐 | `setMusicVolume(double volume)`、`toggleMuteMusic()` | 音量限制在 0.0–1.0 |
| 音效 | `triggerUiOpenSfx()`、`triggerUiBackSfx()` | 面板交互语义事件，带防重复处理 |
| 通知 | `requestNotificationPermissionOnFirstLaunch()` | 初始化后请求权限，保存已提示标记 |
| 通知 | `handleLifecycleStateChanged(...)` | 专注运行时切后台创建监督会话；回前台取消，音乐随生命周期暂停／恢复 |

平台实现位于 `lib/services/`，测试通过构造参数注入 fake 服务。

## 11. 快速参考

### 11.1 读取状态

```dart
controller.remainingSeconds.value;
controller.pomodoroState.value;
controller.phaseStatus.value;
controller.focusDurationSeconds.value;
controller.restDurationSeconds.value;
controller.cycleCount.value;
controller.completedFocusCycles.value;
```

### 11.2 调用接口

```dart
await controller.initialize();
controller.startTimer();
controller.pauseTimer();
controller.resetTimer();
controller.updateFocusDuration(25 * 60);
controller.updateRestDuration(5 * 60);
controller.updateCycleCount(4);
```

---

> 如需确认当前仓库真实状态，请直接回到 `lib/app_controller.dart`、`lib/ui_widgets.dart`、`lib/main.dart` 与 OpenSpec 变更目录核对。

返回 [文档导航](README.md) · [Controller 模块](../.llm-wiki/modules/controller.md)
