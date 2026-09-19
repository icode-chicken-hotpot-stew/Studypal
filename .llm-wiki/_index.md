---
title: Studypal 源码 Wiki
updated: 2026-09-19
sources:
  - lib/main.dart
  - pubspec.yaml
---

# Studypal 源码 Wiki

基于已发布的 `v0.1.0` 源码整理。用户体验与安装请看 [README](../README.md)，当前边界请看 [维护状态](../docs/maintenance.md)。

## 架构

- [系统总览](architecture/overview.md)：入口、Controller、UI 和平台服务。
- [数据流](architecture/data-flow.md)：启动、计时、生命周期与对话事件。
- [目录结构](architecture/directory-structure.md)：运行代码、资源、工具与设计留存。

## 模块

- [Controller](modules/controller.md)：番茄钟、XP、对话、服务协调。
- [书房 UI](modules/study-room.md)：面板、气泡与角色层组合。
- [Live2D](modules/live2d.md)：WebView 资源加载、模型与动作同步。
- [平台服务](modules/services.md)：BGM / SFX、本地通知及测试替身。

## 概念与接口

- [状态与持久化](concepts/state-persistence.md)：阶段语义、时间快照与本地存储。
- [Flutter / JS 桥接](apis/live2d-bridge.md)：通道、事件及资源回调。
- [Controller 详细接口](../docs/interface_spec.md)：公共方法与状态表。
- [对话契约](../docs/talking_interface_spec.md)：触发、仲裁与文案结构。

## 维护指南

- [环境与构建](guides/setup.md)：运行与依赖锁定入口。
- [测试与验收](guides/testing.md)：现有覆盖、设备验证与任务记录。

## See Also

- [文档导航](../docs/README.md)
- [Wiki 约定](_schema.md)
- [更新日志](_log.md)
