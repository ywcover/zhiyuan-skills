# 变更记录

## [0.3.0] - 2026-06-07

### 新增
- `references/seeyonapi-event-index.md` — SeeyonAPI 8.2 事件完整索引
  - 覆盖20个模块、180+事件
  - 每个事件列出完整类路径和关键属性
  - 附场景→事件映射速查表
  - 数据来源：https://open.seeyoncloud.com/seeyonapi/8.2/event/

## [0.2.0] - 2026-06-07

### 新增
- `references/bpm-event-guide.md` — 流程事件开发完整指南
  - 基于致远官方文档 + 项目实战代码
  - 覆盖 AbstractWorkflowEvent 和 @ListenEvent 两套机制
  - 包含前端事件拦截 `$.ctp.bind()`
  - 完整的外部集成实战模式（含日志、防重复、补偿）

## [0.1.1] - 2026-06-07

### 安全修复
- 移除所有客户名称、系统编码、URL路径等敏感信息
- 项目名称全部替换为版本号+模式描述

## [0.1.0] - 2026-06-07

### 初始版本
- 基于多个致远CTP客开项目分析生成
- 覆盖V8.2SP1/V9.0SP1/V10.0SP1三个版本
- 创建汇总Skill：插件规范、CAP4表单、事件监听、超级节点、API集成、JSP页面
