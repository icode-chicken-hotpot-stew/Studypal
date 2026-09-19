---
title: 测试与验收
updated: 2026-09-19
sources:
  - test/app_controller_xp_test.dart
  - test/app_controller_audio_test.dart
  - test/app_controller_supervisor_test.dart
  - test/chat_bubble_test.dart
  - test/test_doubles.dart
---

# 测试与验收

## 命令

安装锁定依赖后执行：

```bash
flutter analyze --no-pub
flutter test --no-pub
```

## 现有测试

| 文件 | 验证内容 |
| :--- | :--- |
| app_controller_xp_test.dart | XP 公式／上限／跨日、等级候选、部分对话中断与排队 |
| app_controller_audio_test.dart | BGM 偏好、暂停／恢复、切歌、音量与音效防重复 |
| app_controller_supervisor_test.dart | 首次权限请求、有效监督会话、取消与调度拒绝 |
| chat_bubble_test.dart | 打字与自动下一句定时 |

SharedPreferences 使用 mock 初始值，音频／通知通过 `test_doubles.dart` 注入 fake 实现。测试聚焦 [Controller](../modules/controller.md) 的决策，不加载真实平台播放器或通知。

## 设备验证

主舞台、WebView、纹理、真实音频及通知送达需 Android 手动验证。具体步骤见 [开发指南](../../docs/dev-guide.md)。

番茄状态机与多阶段恢复仍有专门的测试验收任务未完成；不可根据现有测试全部通过推断 OpenSpec 所有任务已验收。任务状态统一见 [维护说明](../../docs/maintenance.md)。

## See Also

- [环境与构建](setup.md)
- [平台服务](../modules/services.md)
- [Wiki 索引](../_index.md)
