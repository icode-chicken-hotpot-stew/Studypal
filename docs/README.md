# 文档导航

Studypal 当前发布基线为 **v0.1.0**。初次访问请先看 [项目首页](../README.md)；准备维护代码请先看 [开发指南](dev-guide.md)。

## 使用与维护

| 文档 | 用途 |
| :--- | :--- |
| [v0.1.0 发布说明](releases/v0.1.0.md) | 安装入口、功能范围、版本信息 |
| [开发指南](dev-guide.md) | 环境、运行、测试、构建与资源工作流 |
| [维护状态](maintenance.md) | 当前版本边界与待验证事项 |
| [Flutter Run 应急卡片](flutter-run-30s-emergency-card.md) | Android 启动与构建问题排查 |

## 代码与接口

| 文档 | 用途 |
| :--- | :--- |
| [项目接口规范](interface_spec.md) | Controller / View 状态与行为约定 |
| [对话实现说明](talking_interface.md) | 触发、仲裁、文案与气泡交互 |
| [对话契约](talking_interface_spec.md) | 可调用接口、文案格式与时序 |
| [资源目录](../assets/README.md) | 运行时资源与历史素材的位置 |
| [源码 Wiki](../.llm-wiki/_index.md) | 架构、模块、持久化和 JS 桥接索引 |
| [开发约定](../CLAUDE.md) | 面向维护者和编码助手的仓库规则 |

## 开发过程留存

- [历史需求与文案草稿](archive/README.md)：保留设计过程，明确区分原始计划和已发布行为。
- [OpenSpec 导航](../openspec/README.md)：番茄钟与成长／音频／通知的设计契约及验收任务。
- [封面资源说明](media/README.md)：README 图片的素材来源。

阅读优先级：**当前代码与测试 → 当前版本文档 → 设计契约 → 历史草稿**。测试覆盖范围和未完成设备验证需分别看待。
