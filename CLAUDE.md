# Studypal 开发约定

## 当前基线

- 横屏陪伴式学习 Flutter 应用，Android 版本 `v0.1.0`（`0.1.0+1`）已发布。
- 当前大版本开发已告一段落，短期暂无新功能迭代计划。维护信息见 `docs/maintenance.md`。
- 默认入口是 `lib/main.dart`：`MainStage` 创建 `AppController`，等待初始化并转发生命周期。
- 番茄钟、Live2D、对话、XP、音乐与本地通知均有实际实现。
- 以当前源码和测试为事实依据；OpenSpec 记录设计目标与验收进度，未勾选项不等于功能全部未实现，也不能直接视为已验收。

## 技术栈与目录

- Flutter / Dart；推荐环境 Flutter 3.38.6、Dart 3.10.7、JDK 17。
- 状态管理：`ValueNotifier` + `ValueListenableBuilder`，复合对话状态使用 `ChangeNotifier`。
- 存储：`shared_preferences`；音频：`just_audio`；通知：`flutter_local_notifications`。
- 角色：`webview_flutter` + 本地 Live2D / PixiJS 资源。
- Android：Kotlin DSL，AGP 8.11.1、Kotlin 2.2.20；平台目录为 `android/`。
- `lib/app_controller.dart`：番茄钟、恢复、XP、对话、音频与监督会话协调。
- `lib/ui_widgets.dart`：书房 UI、计时配置、成长卷轴、统计黑板、唱片机、对话气泡。
- `lib/character_view.dart`：实际 Live2D 角色渲染和 JS 桥接。
- `lib/services/`：音频与通知平台服务。
- `test/`：XP／对话仲裁、音频、监督会话和气泡交互测试。
- `docs/README.md`：文档导航；`.llm-wiki/_index.md`：源码架构索引。

## 编码规范

- 保持 Controller / View 单向状态流：View 读取状态，通过 Controller 方法触发变更。
- 不要在 UI 层直接写 Controller 的 `ValueNotifier`。
- 新业务逻辑优先放在 `lib/app_controller.dart`，平台行为沿用 `lib/services/`。
- `pomodoroState` 表示学习／休息；`phaseStatus` 表示待开始／运行／暂停。
- 计时进度由 `remainingSeconds` 与 `currentPhaseDurationSeconds` 推导。
- 不在 `build()` 中引入副作用；计时器与监听器在生命周期中管理。
- 只做任务所需的改动，避免额外抽象和顺手重构。

## 开发与验证

```bash
flutter pub get
flutter run -d <android-device-id>
flutter analyze
flutter test
flutter build apk --release
```

- 修改后优先执行 `flutter analyze` 和 `flutter test`。
- 涉及 UI、WebView、音频、通知或生命周期时，在 Android 设备手动验证主流程；无设备时明确记录未验证项。
- 测试使用 fake 平台服务，不代替 Android 通知送达、Live2D 与真实音频验证。
- 运行问题参考 `docs/flutter-run-30s-emergency-card.md`。

## 当前边界

- `fetchHistoryData()` 保留为兼容入口；黑板时长由 XP 折算，尚无完整历史明细。
- UI 采用播放／暂停切换按钮加独立重置按钮；OpenSpec 中显式三按钮目标仍待对齐。
- 通知计划是 3／6 分钟，现有通知文案写作 5／10 分钟，属于已记录差异。
- Release 构建当前使用 debug 签名；发行配置见 `android/app/build.gradle.kts`。
- Dart 包名仍是 `mvp_app`，Android 应用 ID 是 `com.icode.studypal`。不要为了文档整理改动导入路径或存储标识。

## 资源规范

- 舞台使用 `assets/background_back.png` 与 `assets/background_front.png` 分层。
- 当前角色模型：`assets/live2d/hiyori_pro/`；入口：`assets/live2d/hiyori_viewer.html`。
- 文案：`assets/dialogues/dialogues.json`；资源完整地图见 `assets/README.md`。
- 新增运行时资源须更新 `pubspec.yaml`；目录声明不自动递归包含所有子目录。
- pre-commit 会转换已暂存且路径含 `assets` 的 PNG/JPG，删除原图并更新暂存区。图片路径和模型 JSON 引用须一起检查，尤其不要机械转换 Live2D 纹理或启动图标。
- hook 修改资源并拒绝提交后，审阅变更并重新提交，不跳过 hook。

## Git 与文档

- 开始维护前检查 `git status`，从最新 `origin/main` 建分支，通过 PR 合入。
- 仅在用户明确要求时提交、推送或执行远端操作；只暂存本次文件。
- 提交消息沿用 `feat`、`fix`、`docs`、`refactor`、`test`、`chore` 前缀。
- 保留 `pubspec.lock`；本地日志、构建缓存、机器配置和线下比赛材料不纳入发布。
- 更新公共行为时同步接口文档；历史需求保留在 `docs/archive/`，不要当作当前版本承诺。
- 不因阶段性暂停开发而自动归档 GitHub 仓库，也不把 OpenSpec 未完成任务批量勾选。
