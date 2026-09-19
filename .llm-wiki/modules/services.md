---
title: 平台服务模块
updated: 2026-09-19
sources:
  - lib/services/audio_service.dart
  - lib/services/supervisor_notification_service.dart
  - android/app/src/main/kotlin/com/icode/studypal/MainActivity.kt
  - test/test_doubles.dart
---

# 平台服务模块

Controller 依赖两个抽象接口：AudioService 和 SupervisorNotificationService。默认注入真实平台实现，测试注入 fake。

## 音频

`JustAudioService` 维护独立 BGM 与 SFX AudioPlayer：

- 三首背景音乐组成循环播放列表。
- 支持播放指定曲目、原位置恢复、暂停、停止与音量调整。
- 四类 SFX：study_start、study_end、button_open、button_back。
- 音效设置 in-flight 防重、180ms 跨类型冷却和 360ms 同资源去重。

来源：`lib/services/audio_service.dart:L6-L37`、`L51-L84`、`L178-L225`。

Controller 还对 UI 语义音效做 180ms 跨类型及 320ms 同类型节流。来源：`lib/app_controller.dart:L63-L64`。

## 监督通知

`LocalSupervisorNotificationService` 使用 `flutter_local_notifications`：

- 创建 `pomodoro_supervisor` 通知通道并请求权限。
- 以后台会话时间安排 +3 分钟、+6 分钟的两条通知。
- ID 固定为 3101／3102，payload 携带 sessionId 和 stage。
- 采用 `inexactAllowWhileIdle`，实际到达由系统调度。
- 经 `mvp_app/supervisor_debug` MethodChannel 同步原生调试闹钟。

来源：`lib/services/supervisor_notification_service.dart:L9-L24`、`L41-L96`、`L133-L280`。

现有通知正文的分钟数与调度时点不同，详见 [维护状态](../../docs/maintenance.md)。

## 测试边界

Fake 服务记录调用和返回结果，验证 Controller 的决策与持久化行为。真实音乐播放、打包资源、通知权限 UI 和送达时间需 Android 验证。

## See Also

- [Controller](controller.md)
- [系统总览](../architecture/overview.md)
- [测试与验收](../guides/testing.md)
