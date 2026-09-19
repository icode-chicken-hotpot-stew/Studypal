---
title: 环境与构建入口
updated: 2026-09-19
sources:
  - pubspec.yaml
  - pubspec.lock
  - android/app/build.gradle.kts
  - android/settings.gradle.kts
---

# 环境与构建入口

完整操作说明维护在 [开发指南](../../docs/dev-guide.md)。

## 工具链

- Flutter 3.38.6 / Dart 3.10.7。
- JDK 17，Android SDK 与真机／模拟器。
- AGP 8.11.1、Kotlin 2.2.20。
- Gradle 配置从 `android/local.properties` 获取本机 Flutter SDK。

## 依赖锁定

当前 `pubspec.lock` 使用 `https://pub.flutter-io.cn`。精确复现时在终端设置相同 `PUB_HOSTED_URL`，执行 `flutter pub get --enforce-lockfile`；检查阶段使用 `--no-pub` 避免再次解析。

## 常用入口

- `flutter run -d <android-device-id>`：运行默认 `lib/main.dart`。
- `flutter build apk --release`：产物位于 `build/app/outputs/flutter-apk/app-release.apk`。
- `pwsh ./scripts/diagnose_flutter_run.ps1`：生成 `build/diagnostics/` 运行诊断。

Android 应用 ID 为 `com.icode.studypal`；当前 release 使用 debug 签名。发行信息见 [版本说明](../../docs/releases/v0.1.0.md)。

## See Also

- [系统总览](../architecture/overview.md)
- [测试与验收](testing.md)
- [开发指南](../../docs/dev-guide.md)
