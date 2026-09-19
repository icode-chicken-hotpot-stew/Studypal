<div align="center">

# Studypal

**这次，有人陪你一起学。**

把一段专注时间，变成与伙伴一起度过的日常。

[![Release](https://img.shields.io/github/v/release/icode-chicken-hotpot-stew/Studypal?style=flat-square&color=8A5747&label=release)](https://github.com/icode-chicken-hotpot-stew/Studypal/releases/latest)
![Platform](https://img.shields.io/badge/platform-Android-6A7B58?style=flat-square)
![Flutter](https://img.shields.io/badge/Flutter-3.38.6-5D7F95?style=flat-square&logo=flutter&logoColor=white)
![Dart](https://img.shields.io/badge/Dart-3.10.7-5D7F95?style=flat-square&logo=dart&logoColor=white)

**[下载 Android APK](https://github.com/icode-chicken-hotpot-stew/Studypal/releases/download/v0.1.0/app-release.apk)** ·
[版本说明](docs/releases/v0.1.0.md) ·
[开发指南](docs/dev-guide.md) ·
[文档导航](docs/README.md)

<img src="docs/media/studypal-cover.webp" alt="Studypal：这次，有人陪你一起学。应用图标与暖色书房场景。" width="100%">

<sub>封面使用项目内的书房场景与应用图标。</sub>

</div>

## 一间书房，一位伙伴，一段专注

Studypal 是一款基于 **Flutter + Live2D** 的横屏陪伴式学习应用。书桌、唱片机、卷轴和小黑板构成了一个复古学习空间：你开始计时，伙伴进入学习状态；你完成一轮专注，收获经验，也收到一句鼓励。

番茄钟负责节奏，角色与音乐提供陪伴，等级记录每一次积累。

> **当前版本：v0.1.0（0.1.0+1）**，已交付 Android 安装包。当前大版本开发已告一段落，短期暂无新功能迭代计划；仓库用于版本留存、使用说明与问题反馈。

## 在这里，你可以

| | 功能 | 当前版本体验 |
| :---: | :--- | :--- |
| 🍅 | **按自己的节奏专注** | 默认 25 分钟专注、5 分钟休息；支持自定义时长、有限循环、暂停、继续和重置。 |
| 🌿 | **和伙伴一起学习** | Live2D 角色随学习／休息状态切换动作，支持出场、点击互动与对话联动。 |
| 💬 | **在合适的时候听到回应** | 启动、开始专注、完成、返回、点击、闲置六类本地对话；打字机气泡、快进与等级解锁。 |
| ✨ | **看见积累的分量** | 完成专注获得 XP，逐步成长至 Lv.10；卷轴展示升级进度，黑板展示 XP 折算的学习时长。 |
| 🎵 | **让书房有一点声音** | 内置 3 首背景音乐；播放／暂停、切歌、音量调节，以及阶段与面板交互音效。 |
| 🔔 | **离开后也记得回来** | 专注中切后台后安排本地提醒；回到应用时同步计时，重启后读取本地快照恢复。 |

主要数据保存在设备本地，无需注册账号或配置服务器。角色使用内置对话文案，开箱即可体验。

## 开始你的第一轮专注

1. 从 **[Releases](https://github.com/icode-chicken-hotpot-stew/Studypal/releases/latest)** 下载 `app-release.apk`，安装到 Android 设备。
2. 横屏打开应用，点击左上角 **番茄按钮** 展开计时卡片。
3. 使用默认 25/5 节奏，或点击编辑按钮调整专注、休息和循环次数，再按播放键开始。
4. 需要放松时，使用左下角唱片机控制音乐；完成专注后，打开卷轴查看成长进度。

<details>
<summary><strong>使用细节</strong></summary>

- 专注可设置 **5–300 分钟**，休息可设置 **1–300 分钟**。
- 循环设为 `0` 表示只完成一轮「专注 + 休息」；`1–100` 表示本次计划的总轮数。
- 运行中不能编辑配置。配置加减即时生效，「保存」和「取消」都会关闭面板。
- XP 按已完成专注的分钟数结算，每分钟 10 XP、每天最多 2,000 XP。黑板时长由 XP 折算，不是完整的历史计时记录。
- 首次启动会请求通知权限；后台提醒的实际到达时间受 Android 通知权限和系统调度影响。
- 音乐切到后台时暂停，返回前台时按播放偏好恢复。

</details>

## 从源码运行

推荐使用 **Flutter 3.38.6 / Dart 3.10.7**、JDK 17 和 Android SDK。仓库目前提供 Android 工程；开发前运行 `flutter doctor` 检查环境。

```bash
git clone https://github.com/icode-chicken-hotpot-stew/Studypal.git
cd Studypal
flutter pub get
flutter devices
flutter run -d <android-device-id>
```

检查与构建：

```bash
flutter analyze
flutter test
flutter build apk --release
```

APK 输出位置：`build/app/outputs/flutter-apk/app-release.apk`。Android 构建、签名配置和排障方式见 [开发指南](docs/dev-guide.md)。

## 项目地图

```text
lib/
├── main.dart                 # 启动、初始化与应用生命周期
├── app_controller.dart       # 番茄钟、成长、对话与状态持久化
├── ui_widgets.dart           # 复古书房、计时卡、卷轴、黑板与唱片机
├── character_view.dart       # Live2D WebView 与 Flutter / JS 桥接
└── services/
    ├── audio_service.dart    # 背景音乐与音效
    └── supervisor_notification_service.dart
                              # Android 本地监督提醒

assets/                       # 场景、角色、文案、字体与音频
android/                      # Android 宿主与 Gradle 配置
test/                         # Controller 与对话气泡测试
docs/                         # 使用开发文档、版本说明与历史设计
openspec/                     # 开发过程中的设计契约与验收任务
scripts/                      # 图片处理与 Flutter 运行诊断
.llm-wiki/                    # 按源码整理的架构与模块索引
```

状态流保持轻量：**View → Controller 方法 → Notifier → View**。番茄钟、XP、音乐偏好与恢复快照通过 `SharedPreferences` 存储；音频使用 `just_audio`，通知使用 `flutter_local_notifications`，角色由 WebView 承载 Live2D。

## 文档与版本留存

| 想了解什么 | 从这里开始 |
| :--- | :--- |
| 下载与版本内容 | [v0.1.0 发布说明](docs/releases/v0.1.0.md) |
| 搭建环境、调试、构建 | [开发指南](docs/dev-guide.md) |
| 当前能力边界与待验证事项 | [维护状态](docs/maintenance.md) |
| 模块接口与状态流 | [接口规范](docs/interface_spec.md) · [源码 Wiki](.llm-wiki/_index.md) |
| 角色对话与文案配置 | [实现说明](docs/talking_interface.md) · [对话契约](docs/talking_interface_spec.md) |
| 资源目录与历史设计 | [资源说明](assets/README.md) · [文档导航](docs/README.md) |

反馈问题时，请在 [Issues](https://github.com/icode-chicken-hotpot-stew/Studypal/issues) 附上应用版本、设备型号、Android 版本和复现步骤。由于短期暂停功能迭代，反馈与 PR 的处理时间不作承诺。

---

<div align="center">

由 **鸡公煲队** 制作 · 感谢 Flutter、Live2D、PixiJS 与相关依赖的贡献者

<sub>从一个番茄钟开始，把今天过得更专注一点。</sub>

</div>
