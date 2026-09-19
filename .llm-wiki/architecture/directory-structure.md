---
title: 项目目录结构
updated: 2026-09-19
sources:
  - pubspec.yaml
  - .gitignore
  - .gitattributes
---

# 项目目录结构

```text
Studypal/
├── lib/            Flutter 运行代码：入口、Controller、UI、角色、服务
├── android/        Android 宿主、通知桥接与构建配置
├── assets/         运行素材、旧素材、Live2D 依赖源码
├── test/           4 个测试文件与 test_doubles.dart
├── docs/           当前接口、开发指南、维护信息与版本说明
│   ├── archive/    历史 PRD 与文案草稿
│   ├── media/      README 封面
│   └── releases/   版本说明
├── openspec/       设计与验收任务
├── scripts/        图片处理、运行诊断
├── .claude/        仓库内开发辅助命令与技能
├── .llm-wiki/      源码索引
├── pubspec.yaml    Dart 依赖、版本和运行资产声明
└── pubspec.lock    固定依赖解析结果
```

## 资源辨别

`assets/CubismWebFramework-5-r.5-beta.3.1/` 是第三方源码；实际角色入口使用 `assets/live2d/libs/`。`.gitattributes` 标记这些依赖为 vendored，使 GitHub 语言统计聚焦应用自身代码。

全部素材分组见 [资源目录](../../assets/README.md)。文件出现在 `assets/` 中不等于已经被 Flutter 打包，须核对 `pubspec.yaml`。

## 本地文件

`build/`、`.dart_tool/`、日志、IDE 状态和 Android 本机 SDK 路径属于本地运行产物，由 `.gitignore` 管理。

## See Also

- [系统总览](overview.md)
- [文档导航](../../docs/README.md)
- [Wiki 约定](../_schema.md)
