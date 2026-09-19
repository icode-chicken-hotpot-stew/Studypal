---
title: Flutter 与 Live2D JS 桥接
updated: 2026-09-19
sources:
  - lib/character_view.dart
  - assets/live2d/hiyori_viewer.html
---

# Flutter 与 Live2D JS 桥接

桥接由 CharacterView 注册。HTML 为应用内置页面，资源请求由 Flutter asset bundle 响应。

## JS → Flutter

| 通道 | 消息 | Flutter 行为 |
| :--- | :--- | :--- |
| AssetLoader | `assetPath\|callbackId` | `rootBundle.loadString()`，回调 `_assetLoaded` |
| BinaryAssetLoader | `assetPath\|callbackId` | `rootBundle.load()`，Base64 编码后回调 `_binaryAssetLoaded` |
| Live2DController | JSON 对象，带 `type` | 状态同步或调用 Widget 回调 |

来源：`lib/character_view.dart:L170-L234`。

角色事件类型：

- `ready`：标记桥接就绪，同步状态和视口。
- `character_tap`：调用 `onCharacterTap`。
- `entrance_motion_started`：调用 `onEntranceMotionStarted`。

来源：`lib/character_view.dart:L316-L345`。

## Flutter → JS

- `window.setCharacterState("study" | "normal")`：主阶段状态。
- `window.playMotionByName("Talk", 0)`：对话动作；兼容回退到 `playMotion("Talk")`。
- `window.setViewportOffset(x, y)`：视口偏移。
- `window._assetLoaded(callbackId, content, error?)`：文本响应。
- `window._binaryAssetLoaded(callbackId, dataUrl, error?)`：二进制响应。

来源：`lib/character_view.dart:L103-L156`、`L178-L222`。

## See Also

- [Live2D 模块](../modules/live2d.md)
- [数据流](../architecture/data-flow.md)
- [Wiki 索引](../_index.md)
