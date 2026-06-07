# 致远CTP二开技能库

基于真实客开项目（安徽建工、徽商集团、数字安徽、规划设计研究院、国资集团等）提炼的AI辅助开发技能库。

## 目录结构

```
zhiyuan-skills/
├── README.md                       # 本文件
├── CHANGELOG.md                    # 变更记录
├── zhiyuanskill-yw/                # 汇总索引Skill（加载此Skill可使用所有子技能）
│   ├── SKILL.md
│   ├── agents/openai.yaml
│   └── references/
│       ├── zhiyuan-sources.md
│       ├── cap4-guide.md
│       ├── api-integration.md
│       ├── jsp-guide.md
│       └── ...
├── skills/                         # 独立子技能（按功能拆分，持续补充）
│   ├── ctp-new-plugin/             # 新建插件规范
│   ├── ctp-cap4-form/              # CAP4表单操作
│   ├── ctp-event-listener/         # 事件监听
│   ├── ctp-super-node/             # 超级节点
│   ├── ctp-rest-api/               # REST资源
│   ├── ctp-http-client/            # 第三方API集成
│   ├── ctp-jsp-page/               # JSP维护页面
│   ├── ctp-sso/                    # SSO单点登录
│   └── ctp-db-access/              # 数据访问
├── templates/                      # 代码模板（可直接复制使用）
└── projects/                       # 已分析项目索引
    └── project-index.md
```

## 使用方式

在Claude Code中加载 `zhiyuanskill-yw` Skill后，AI会自动：
- 识别致远CTP项目版本
- 遵循插件开发规范生成代码
- 使用正确的事件监听模式
- 优先低侵入实现方案

## 贡献方式

1. 从新项目中发现模式 → 提Issue
2. 确认后更新对应Skill文件
3. 更新CHANGELOG.md
4. Push到仓库

## 已分析项目

| 项目 | 版本 | 核心模式 |
|------|------|---------|
| 安徽建工_CTP_V5 | V9.0SP1 | 20+插件、群杰印章、国资委报送、水印 |
| 徽商_CTP_V5 | V8.2SP1 | 银行付款节点、办结分类、银企直连 |
| 徽商合同_CTP_V5 | V10.0SP1 | 26个合同超级节点、动态字段映射、补偿机制 |
| 合规院_CTP_V5 | V9.0SP1 | 金蝶云凭证/资产、批量打印 |
| 数字安徽_CTP_V5 | V10.0SP1 | 金蝶+招采推送、SSO、SM4加密 |
| 安徽国资_CTP_V5 | V8.2SP1 | 用友YONBIP、友空间SSO、门户定制 |
