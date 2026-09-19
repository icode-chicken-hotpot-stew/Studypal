# 开发指南

本文面向接手或复现 Studypal `v0.1.0` 的开发者。应用功能与维护状态见 [仓库首页](../README.md) 和 [维护状态](maintenance.md)。

## 1. 环境准备

| 工具 | 基线 |
| :--- | :--- |
| Flutter | 推荐 3.38.6 stable |
| Dart | 3.10.7；`pubspec.yaml` 约束为 `^3.10.7` |
| Java | JDK 17 |
| Android 构建 | AGP 8.11.1、Kotlin 2.2.20；Gradle 版本由 wrapper 配置固定 |
| 运行目标 | Android 真机或模拟器，启用可用的 Android System WebView |
| 可选工具 | `uv`、`pre-commit`；PowerShell 7 用于诊断脚本 |

安装 Flutter 和 Android SDK 后，在仓库根目录执行：

```bash
flutter doctor
flutter pub get
flutter devices
flutter run -d <android-device-id>
```

使用已提交的 `pubspec.lock` 安装依赖。Android SDK 路径由本地 `android/local.properties` 保存，该文件不提交。仓库目前只提供 Android 平台工程，设备列表中的 Windows 或浏览器不能直接代替 Android 主流程验证。

### 精确复现发布基线

现有 lockfile 使用 `https://pub.flutter-io.cn`。如果当前 Pub 源不同，普通 `pub get` 可能重新解析版本。精确复现时，在当前终端使用相同源并强制锁文件：

```powershell
# PowerShell
$env:PUB_HOSTED_URL = 'https://pub.flutter-io.cn'
flutter pub get --enforce-lockfile
flutter analyze --no-pub
flutter test --no-pub
```

在 Bash 中对应为 `export PUB_HOSTED_URL=https://pub.flutter-io.cn`。此设置只需作用于当前终端；不应把安装依赖产生的意外版本变动混入文档提交。

## 2. 先认识入口与状态流

```text
main.dart / MainStage
  ├─ 创建 AppController，等待 initialize()
  ├─ 向 Controller 转发生命周期
  └─ UIWidgets
       ├─ 计时卡片、配置、卷轴、黑板、唱片机
       ├─ CharacterView → WebView → 本地 Live2D
       └─ ChatBubble

用户操作 → Controller 方法 → Notifier → UI 重建
                          ├─ SharedPreferences
                          ├─ AudioService
                          └─ SupervisorNotificationService
```

### Controller

`lib/app_controller.dart` 是状态中枢，负责番茄钟、XP、对话及平台服务协调：

- `pomodoroState`：`resting` / `studying`，表达陪伴业务阶段。
- `phaseStatus`：`ready` / `running` / `paused`，表达运行控制状态。
- `remainingSeconds`、`currentPhaseDurationSeconds`：供 UI 计算倒计时与进度。
- `startTimer()`、`pauseTimer()`、`resetTimer()`：计时控制；`toggleTimer()` 是当前 UI 使用的兼容封装。
- `updateFocusDuration()`、`updateRestDuration()`：接收秒；UI 展示与调整使用分钟。
- `updateCycleCount()`：`null` 表示单轮后停止，`1–100` 表示总轮数。
- `initialize()` 与 `synchronizeWithCurrentTime()`：恢复本地快照、按真实时间推进过期阶段。

不要在 UI 层直接改写 Notifier，也不要维护另一套真实计时状态。

### UI 与角色

`lib/ui_widgets.dart` 实际承载复古书房主界面。分层顺序是背景、角色、前景，再叠加面板与气泡。

`lib/character_view.dart` 已完整接入主入口：加载本地 HTML、JS、模型与纹理，并通过 JS 通道转发角色点击、出场事件和状态变化。

配置面板的加减操作即时写入 Controller；运行时禁止编辑。暂停时修改时长影响后续阶段，当前阶段使用自己的时长快照。

### 平台服务

- `lib/services/audio_service.dart`：独立 BGM / SFX 播放通道、循环播放与音效节流。
- `lib/services/supervisor_notification_service.dart`：通知初始化、权限、3／6 分钟调度与取消。
- `android/app/src/main/kotlin/com/icode/studypal/`：Android 宿主与监督提醒的原生桥接。

更多接口见 [项目接口规范](interface_spec.md) 和 [源码 Wiki](../.llm-wiki/_index.md)。

## 3. 资源与文案

详见 [资源目录说明](../assets/README.md)。常用入口：

| 内容 | 路径 |
| :--- | :--- |
| 舞台分层 | `assets/background_back.png`、`assets/background_front.png` |
| UI 图片 | `assets/images/` |
| 当前 Live2D 模型 | `assets/live2d/hiyori_pro/` |
| WebView 页面与库 | `assets/live2d/hiyori_viewer.html`、`assets/live2d/libs/` |
| 对话文案 | `assets/dialogues/dialogues.json` |
| 音乐／音效 | `assets/music/`、`assets/sfx/` |
| 字体 | `assets/fonts/` |

新增运行时资源时检查 `pubspec.yaml`，并显式注册需要的子目录。修改角色文件名时同步模型 JSON、HTML 与 Flutter 中的引用。

对话文案支持等级和多句候选组，具体格式见 [对话契约](talking_interface_spec.md)。文案草稿位于 [历史设计](archive/README.md)，运行时读取的是资产 JSON。

### 图片 hook

仓库使用 `.pre-commit-config.yaml` 调用 `uv run scripts/check_images.py`。hook 会把已暂存、路径含 `assets` 的 PNG/JPG 转成 WebP，删除原文件并更新暂存区，随后阻止当次提交。

检查转换后的代码、`pubspec.yaml` 和模型引用，再重新提交。Live2D 纹理、应用图标等可能依赖固定格式，不能按普通 UI 图片直接更换扩展名。

手动检查普通图片：

```bash
uv run scripts/compress_images.py --to-webp --dry-run
```

## 4. 自动检查与真机验证

```bash
flutter analyze
flutter test
```

| 测试文件 | 覆盖范围 |
| :--- | :--- |
| `test/app_controller_xp_test.dart` | XP 结算、日上限、跨日、等级解锁与对话仲裁 |
| `test/app_controller_audio_test.dart` | 自动播放、偏好、前后台恢复、音效防重复及失败处理 |
| `test/app_controller_supervisor_test.dart` | 首次权限请求、监督会话开启／取消及拒绝处理 |
| `test/chat_bubble_test.dart` | 打字机与自动下一句时序 |
| `test/test_doubles.dart` | 音频、通知 fake 服务 |

涉及平台能力时，在 Android 设备额外验证：

1. 冷启动，确认书房、角色出场、对话和背景音乐正常。
2. 开始、暂停、继续、重置计时，调整专注／休息／循环。
3. 完成专注，检查休息切换、XP 与卷轴反馈。
4. 切后台、回前台、重启，检查时间恢复和音乐偏好。
5. 分别检查允许／拒绝通知权限后的行为。
6. 点击角色、气泡、快进和各面板，检查联动及音效。

自动测试中的 fake 服务不能证明真实 WebView、通知送达或音频资源加载。既有设计的未完成验证项见 [维护状态](maintenance.md)。

## 5. 构建与版本

```bash
flutter build apk --release
```

- 产物：`build/app/outputs/flutter-apk/app-release.apk`。
- 版本：`pubspec.yaml` 中的 `0.1.0+1`。
- Android 应用 ID：`com.icode.studypal`；显示名：`Studypal`。
- Dart 包名保留为 `mvp_app`，现有 `package:mvp_app/...` 导入据此工作。
- 当前 `release` 构建使用 `signingConfigs.getByName("debug")`，供当前版本内测分发；若另行正式发行，需配置和保管稳定的发行密钥。不同机器的 debug 签名可能不同。
- 已发布安装包和版本说明位于 [v0.1.0 Release](https://github.com/icode-chicken-hotpot-stew/Studypal/releases/tag/v0.1.0)。

## 6. 遇到运行问题

优先看 [Flutter Run 应急卡片](flutter-run-30s-emergency-card.md)。在 Windows 上可生成诊断：

```powershell
pwsh ./scripts/diagnose_flutter_run.ps1
```

结果位于 `build/diagnostics/`。先定位第一条上游错误，再判断是 SDK、Gradle 下载、插件版本还是运行时资源问题。

## 7. 维护协作

从最新 `origin/main` 建立维护分支，小范围提交并通过 PR 合入。保留 lockfile，提交前核对 diff 与相关测试结果；行为变更同步接口文档和版本说明。

- 当前维护入口：[维护状态](maintenance.md)。
- 仓库约定：[CLAUDE.md](../CLAUDE.md)。
- 设计与任务留存：[OpenSpec 导航](../openspec/README.md)。
- 完整文档目录：[文档导航](README.md)。
