---
title: Live2D 角色模块
updated: 2026-09-19
sources:
  - lib/character_view.dart
  - assets/live2d/hiyori_viewer.html
  - assets/live2d/hiyori_pro/hiyori_movie_pro_t03.model3.json
---

# Live2D 角色模块

`CharacterView` 是主流程中的实际角色渲染组件，使用透明 WebView 承载本地 Live2D 模型。

## 加载顺序

1. 创建 WebViewController，Android 设置透明背景并允许无需手势的媒体播放。
2. 注册文本资产、二进制资产和角色事件通道。
3. 加载本地 HTML，预读模型纹理并编码为 data URL。
4. 读取三个本地 JS 库，将 HTML 中的 script 引用替换为内联代码。
5. 注入纹理字典，用模型目录作为 baseUrl 加载页面。

来源：`lib/character_view.dart:L158-L313`。

## 默认模型

- 路径：`assets/live2d/hiyori_pro/`。
- 描述文件：`hiyori_movie_pro_t03.model3.json`。
- 纹理：`hiyori_movie_pro_t03.4096/texture_00.png`。
- 动作组：Start、Study、Smile、Talk、Angry、Normal。
- Talk 的真实文件名是 `motion/talk.son`。

来源：`lib/character_view.dart:L47-L58`、模型 JSON `L3-L53`。

## 状态同步

`studying` 映射到 `study`，其他业务阶段映射到 `normal`。页面加载完成后同步状态与视口偏移；桥接 ready 后，对话状态开始时请求 Talk 动作。

热重载和 Widget 更新会再次尝试同步。来源：`lib/character_view.dart:L73-L156`。

## 交互

页面发送 `ready`、`character_tap`、`entrance_motion_started`。Flutter 将点击和出场消息交给 UI 回调，再触发 Controller 对话行为。

来源：`lib/character_view.dart:L316-L345`、`assets/live2d/hiyori_viewer.html:L964-L987`。

## See Also

- [桥接接口](../apis/live2d-bridge.md)
- [书房 UI](study-room.md)
- [系统总览](../architecture/overview.md)
- [资源目录](../../assets/README.md)
