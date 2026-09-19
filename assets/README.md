# 资源目录说明

本目录同时保留当前游戏资源与开发阶段的参考素材。运行时资产以 [`pubspec.yaml`](../pubspec.yaml) 声明和实际代码引用为准。

## 当前主流程

| 资源 | 用途／消费方 |
| :--- | :--- |
| `background_back.png`、`background_front.png` | `UIWidgets` 舞台前后景 |
| `images/` | 番茄、卷轴、黑板、唱片机、图标与团队标识 |
| `fonts/` | ZCOOLKuaiLe-Regular 主 UI 字体、ZhuoKai 黑板字体 |
| `dialogues/dialogues.json` | `AppController` 对话候选组与等级条件 |
| `music/bgm1.mp3`–`bgm3.mp3` | `JustAudioService` 的 3 首背景音乐 |
| `sfx/` | 开始／结束专注与打开／返回面板音效 |
| `live2d/hiyori_viewer.html` | `CharacterView` 加载的 WebView 入口 |
| `live2d/libs/` | PixiJS、Cubism Core、Live2D display 库；页面实际加载其中的三个 min.js 文件 |
| `live2d/hiyori_pro/` | 默认 Hiyori 模型、物理、动作与纹理 |

当前模型的 `Talk` 动作引用 `motion/talk.son`。这是模型 JSON 中的实际路径，修改扩展名须同步引用。

## 保留素材与第三方源码

- `background.webp`、`images/background.webp`：完整场景图；主舞台目前使用分层图片。
- `live2d/hiyori/`：早期模型及动作，仍有部分目录在资源清单中注册；默认模型已切换为 `hiyori_pro`。
- `New/`、`hiyori_movie_pro_t03.4096/`、根目录 `live2dcubismcore.min.js`：开发时保留的原始／重复资源，不是当前默认加载路径。
- `live2d/load_textures.dart.txt`：纹理加载参考片段。
- `CubismWebFramework-5-r.5-beta.3.1/`：第三方框架源码留存，不在 Flutter 运行时资产清单中；附带的 [LICENSE](CubismWebFramework-5-r.5-beta.3.1/LICENSE.md) 和上游说明保留在原目录。

## 修改资源

1. 查清 Flutter、HTML 和模型 JSON 中的引用。
2. 更新 `pubspec.yaml`，需要的子目录单独声明。
3. Live2D 纹理和图标不要直接套用普通图片的 WebP 转换流程。
4. 在 Android 设备核对打包后加载效果。

README 封面素材来源见 [封面说明](../docs/media/README.md)。更多工作流见 [开发指南](../docs/dev-guide.md)。
