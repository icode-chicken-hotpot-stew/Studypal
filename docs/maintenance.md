# 维护状态

## 版本基线

- 已发布版本：**v0.1.0**，应用版本 `0.1.0+1`。
- 发布日期：2026-05-26。
- 目标平台：Android，横屏运行。
- 当前大版本开发已告一段落，短期暂无新功能迭代计划。
- 仓库继续用于源码与版本留存、下载、问题反馈和后续维护。

本文描述发布基线的实际边界，不作为新的迭代排期。

## 已交付能力

番茄钟及本地快照恢复、有限循环与自定义时长、Live2D 角色与本地对话、XP／等级、音乐与交互音效、本地监督提醒均已接入默认应用入口。完整体验说明见 [README](../README.md)。

## 当前行为与已知差异

| 项目 | 实际行为／边界 | 代码依据 |
| :--- | :--- | :--- |
| 历史统计 | 黑板以 `dailyXp / 10`、`totalXp / 10` 折算时长；受每日 XP 上限影响，不能当成全部实际学习时长。`fetchHistoryData()` 尚未接入历史明细。 | `lib/ui_widgets.dart`、`lib/app_controller.dart` |
| 计时控制 | 当前是播放／暂停切换按钮与独立重置按钮；OpenSpec 中的显式三按钮目标尚未对齐。 | `lib/ui_widgets.dart` |
| 配置保存 | 加减按钮即时写入；「取消」与「保存」都关闭面板，不回滚调整。 | `lib/ui_widgets.dart` |
| 通知时点 | 服务安排在离开后的 3／6 分钟，现有通知正文写作 5／10 分钟；非精确通知还受 Android 调度影响。 | `lib/services/supervisor_notification_service.dart` |
| 对话来源 | 读取本地 JSON 与内置兜底文案，按等级筛选候选；没有接入云端生成式对话。 | `lib/app_controller.dart` |
| 数据保存 | 配置、计时快照、XP 和音乐偏好存于设备本地；清除应用数据会清除这些记录。 | `lib/app_controller.dart` |
| 发行配置 | Release 使用 debug 签名，当前定位为内测分发包。 | `android/app/build.gradle.kts` |

## 验证留存

现有测试涵盖 XP／等级、部分对话仲裁、音频协调、监督会话与气泡时序。测试通过不代表所有设备场景均已验收。

以下验收记录仍有未完成项：

- [番茄钟 tasks](../openspec/changes/improve-pomodoro-functionality/tasks.md)：专门的状态机／持久化边界测试、显式控制目标、设备主流程验证。
- [成长与音频 tasks](../openspec/changes/add-retention-level-and-audio-systems/tasks.md)：真实通知送达、打包资源运行时加载、前后台音频和回滚验证。

已存在的音乐、音效和角色资产可在 [资源目录](../assets/README.md) 核对。设计任务中包含“资源 + 运行验证”的组合项，不能仅凭文件存在就整体勾选。

### 2026-09-19 仓库整理检查

- Flutter 3.38.6 / Dart 3.10.7，使用原始 lockfile 与相同 Pub 源安装依赖。
- `flutter test --no-pub`：31 项通过。
- `flutter analyze --no-pub`：3 条既有 info（`audio_service.dart:69` 的 deprecated API，`ui_widgets.dart:142` 的两处字符串插值提示）；无 error / warning，命令因 info 返回非零。
- 当前环境无 Android 真机或模拟器，本次未重新验证设备主流程。

## 后续接手方式

1. 从 [开发指南](dev-guide.md) 复现当前版本，运行检查与 Android 主流程。
2. 以当前源码为事实源，核对需要接续的 OpenSpec 任务。
3. 使用维护分支和 PR 记录小范围改动；行为变动同步文档。
4. 提交问题时附应用版本、Android 版本、设备型号、复现步骤和必要日志。

返回 [文档导航](README.md)。
