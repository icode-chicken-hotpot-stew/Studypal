# Studypal

<div align="center">
<p><em>这次，有人陪你一起学。</em></p>

</div>

当前项目已进入正式快速开发阶段，不应再按“当前仍处于 MVP 阶段”理解。

- 当前开发窗口：1 周
- 当前优先级：前端 + 基础后端主流程落地
- 次要优先级：复杂后端能力与非核心扩展
- 默认入口：`lib/main.dart`
- 番茄钟权威契约：`openspec/changes/improve-pomodoro-functionality/`

Studypal 是一款基于 Flutter 的横屏陪伴式专注学习应用。它围绕番茄工作法构建，将专注管理、情感陪伴与成长反馈融合为一个统一的学习空间。在沉浸式复古风格界面中，你可以启动专注计时，获得 Live2D 角色的对话陪伴与背景音乐氛围，并通过经验值与等级系统感受长期积累的价值。

---

## 功能概览

| 功能 | 说明 |
|------|------|
| **番茄钟计时** | 专注 / 休息阶段自动切换，支持自定义时长与循环次数；切后台、回前台、重启后均可恢复状态 |
| **等级与经验值** | 完成专注授予 XP，设置每日上限，升级时提供视觉与音效反馈 |
| **角色陪伴与对话** | Live2D 角色随状态切换表现，支持冷启动、开始专注、完成阶段、点击互动等多场景对话 |
| **背景音乐与音效** | 应用启动后自动播放背景音乐，支持播放 / 暂停、切歌、音量调节；阶段切换时触发对应音效 |
| **后台监管通知** | 专注中切至后台超过一定时间后，通过本地通知提醒返回 |
| **v2 复古风格 UI** | 横屏学习舞台形式呈现，视觉风格统一 |

---

## 技术栈

- **框架**：Flutter / Dart
- **状态管理**：`ValueNotifier` + `ValueListenableBuilder`
- **本地存储**：`SharedPreferences`
- **音频播放**：`just_audio`
- **本地通知**：`flutter_local_notifications`
- **角色渲染**：`webview_flutter`（承载 Live2D / WebView）
- **Android 构建**：Kotlin DSL（`build.gradle.kts`）

---

## 快速开始

```bash
# 安装依赖
flutter pub get

# 运行应用
flutter run
```

## 项目结构

```text
lib/
├── main.dart              # 应用入口，创建 MainStage 并注入 AppController
├── app_controller.dart    # 状态中枢，统一收口番茄钟、XP、音乐、对话和生命周期
├── ui_widgets.dart        # v2 复古风格主界面与大部分交互逻辑
├── character_view.dart    # Live2D 角色动画层
└── live2d.dart            # Live2D / WebView 独立原型（非默认入口）

docs/
├── dev-guide.md           # 面向新成员的开发说明
└── 软件需求文档.md         # 完整的产品需求与设计说明
```
