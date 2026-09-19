---
title: 系统总览
updated: 2026-09-19
sources:
  - lib/main.dart
  - lib/app_controller.dart
  - lib/ui_widgets.dart
  - lib/character_view.dart
  - pubspec.yaml
---

# 系统总览

Studypal 是 Android 横屏 Flutter 应用。核心体验由本地状态、素材和平台插件实现。

```text
main.dart / MainStage
   │ 创建、初始化、转发生命周期
   ▼
AppController ◄──── 用户意图 ───── UIWidgets
   │                                │
   ├─ ValueNotifier / ChangeNotifier ─┘
   ├─ SharedPreferences
   ├─ AudioService → just_audio
   └─ SupervisorNotificationService → Android 通知

UIWidgets → CharacterView → WebView / PixiJS / Live2D
          → ChatBubble
          → 计时卡、成长卷轴、黑板、唱片机
```

## 主要职责

- `lib/main.dart:L32-L103`：创建和销毁 Controller，等待初始化后展示 UI；监听应用生命周期。
- `lib/app_controller.dart:L191-L331`：[业务状态与协调](../modules/controller.md)。简单状态用 ValueNotifier，对话复合状态使用 ChangeNotifier。
- `lib/ui_widgets.dart:L346-L420`：[书房组合层](../modules/study-room.md)，按前后景顺序放置角色和交互入口。
- `lib/character_view.dart:L158-L313`：[角色渲染](../modules/live2d.md)，加载本地资源到 WebView。

## 技术边界

Android 宿主由 Kotlin / Gradle 构建。Dart 包名为 `mvp_app`，Android ID 为 `com.icode.studypal`。本地配置、XP 与计时快照由 SharedPreferences 保存；角色文案来自资产 JSON。

依赖版本以 `pubspec.lock` 为准。推荐工具链和精确安装方法见 [环境指南](../guides/setup.md)。

## See Also

- [数据流](data-flow.md)
- [目录结构](directory-structure.md)
- [平台服务](../modules/services.md)
- [Wiki 索引](../_index.md)
