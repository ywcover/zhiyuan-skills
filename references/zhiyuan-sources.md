# 致远开发资源索引

## 官方文档

- 致远开放平台: https://open.seeyoncloud.com/
- V5 技术平台(CTP): https://open.seeyoncloud.com/v5devCTP/
- SeeyonAPI: https://open.seeyoncloud.com/seeyonapi/
- V5 集成平台(CIP): https://open.seeyoncloud.com/v5devCIP/
- V5 应用平台(CAP): https://open.seeyoncloud.com/v5devCAP/
- V5 移动平台(CMP): https://open.seeyoncloud.com/v5devCMP/
- V8 技术文档: https://open.seeyoncloud.com/v8dev/
- V8 产品文档: https://open.seeyoncloud.com/v8operate/
- V8 运维文档: https://open.seeyoncloud.com/v8doc/

## 官方开发优先级

1. CIP/配置 — 集成配置需求首选
2. 插件化开发 — 自定义功能扩展
3. REST/OpenAPI/SeeyonAPI — 远程系统集成
4. Event/@ListenEvent 监听 — 标准产品行为响应
5. APPS API / 动态接口 — 支持的扩展点
6. 源码修改 — 最后选项

## 本地模板库

- 模板库路径（用户本地）：V5插件、CAP4表单、SSO、水印、批量打印等模板
- 项目库路径（用户本地）：V8/UDC真实项目示例

## 搜索模式

```powershell
# V5插件配置
rg --files . -g 'pluginCfg.xml' -g 'pluginProperties.xml' -g 'spring-*.xml'

# V5后端代码
rg --files . -g '*.java' | rg 'BaseResource|@ListenEvent|CAP4|DBKit|BaseController|Manager'

# V5前端
rg --files . -g '*.jsp' -g '*.js' -g '*.html'

# V8/UDC
rg --files . -g '*.java' | rg 'RestApiOperation|extend|microflow|OpenAPI|UDC'
```

## 关键类和接口

### V5后端核心
| 符号 | 用途 |
|------|------|
| `BaseController` | Controller基类 |
| `BaseResource` | REST资源基类 |
| `BaseSuperNodeAction` | 流程超级节点基类 |
| `BasePO` | ORM实体基类 |
| `AbstractSystemInitializer` | 系统初始化器 |
| `@ListenEvent` | 事件监听注解 |
| `@NeedlessCheckLogin` | 免登录注解(V8+) |
| `@AjaxAccess` | AJAX访问注解(V8+) |
| `@RestInterfaceAnnotation` | 客开REST注解(V5) |
| `JDBCAgent` | JDBC操作类 |
| `DBAgent` | HQL/ORM操作类 |
| `CommonBtn` | CAP4自定义按钮基类 |
| `FormFieldCustomCtrl` | CAP4自定义控件基类 |
| `QuartzJob` | 定时任务接口 |
| `FlipInfo` | 分页信息 |

### CAP4表单核心
| 符号 | 用途 |
|------|------|
| `FormDataMasterBean` | 主表数据 |
| `FormDataSubBean` | 子表数据 |
| `FormDataBean` | 表数据基类 |
| `FormTableBean` | 表定义 |
| `FormFieldBean` | 字段定义 |
| `CAP4FormManager` | 表单管理服务 |
| `FormApi4Cap4` | CAP4表单API |

### 事件类
| 符号 | 用途 |
|------|------|
| `CollaborationStartEvent` | 协同发起 |
| `CollaborationProcessEvent` | 协同处理 |
| `CollaborationFinishEvent` | 协同结束 |
| `CollaborationStopEvent` | 协同终止 |
| `CollaborationCancelEvent` | 协同撤销 |
| `CollaborationStepBackEvent` | 协同回退 |
| `CollaborationTakeBackEvent` | 协同取回 |
| `FormDataAfterSubmitEvent` | CAP4表单提交后 |
| `FileDownloadEvent` | 文件下载 |
| `AddMemberEvent` | 人员新增 |
| `UpdateMemberEvent` | 人员修改 |
| `DeleteMemberEvent` | 人员删除 |
| `AddDepartmentEvent` | 部门新增 |
| `UpdateDepartmentEvent` | 部门修改 |
| `DeleteDepartmentEvent` | 部门删除 |

## 编码注意事项

- 老V5项目文件常为GBK编码，编辑前用`file -bi`检测
- JSP/XML/properties文件中的中文在UTF-8终端下可能显示乱码
- 建议在IDEA中设置项目编码后操作
