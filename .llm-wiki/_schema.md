---
title: Wiki 约定
updated: 2026-09-19
sources:
  - pubspec.yaml
  - lib/main.dart
---

# Studypal Wiki 约定

| 字段 | 值 |
| :--- | :--- |
| 基线 | v0.1.0 / 0.1.0+1 |
| 语言 | Dart；Android Kotlin；Live2D 页面 JavaScript |
| 框架 | Flutter 3.38.6 / Dart 3.10.7 |
| 构建 | Flutter + Android Gradle Kotlin DSL |
| 入口 | `lib/main.dart` |
| 测试 | `flutter_test`，fake 音频与通知服务 |
| 依赖管理 | pub，保留 `pubspec.lock` |

目录地图见 [项目结构](architecture/directory-structure.md)。本文档集为当前源码的可导航摘要；公共接口的详细契约继续维护在 `docs/`，避免重复维护两套接口手册。

## 写作约定

- 使用中文；文件名采用小写 kebab-case。
- 页面开头记录 `updated` 和仓库相对 `sources`。
- 引用具体行为时记录源文件与行范围；源码移动后重新核对。
- 当前源码行为、设计目标与未验证事项分别描述。
- 每页从 [_index.md](_index.md) 可到达，末尾保留相关页面链接。
- 修改后检查内部链接和来源文件，并追加 [_log.md](_log.md)。

## See Also

- [Wiki 索引](_index.md)
- [更新日志](_log.md)
