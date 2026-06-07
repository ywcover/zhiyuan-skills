# SeeyonAPI 8.2 事件完整索引

基于 [SeeyonAPI 8.2 Event](https://open.seeyoncloud.com/seeyonapi/8.2/event/) 官方文档整理，共覆盖 **20个模块，180+事件**。

## 模块速查表

| 编号 | 模块 | 事件数 | 典型场景 |
|------|------|--------|---------|
| 68 | 讨论模块 | 2 | 讨论发布、回复 |
| 69 | 公告模块 | 2 | 公告发布、审核 |
| 70 | 任务管理 | 7 | 日程增删改、任务增删完成改 |
| 71 | 协同模块 | 24 | 发起/处理/结束/回退/撤销/取回/跳过/移交等 |
| 72 | 知识管理 | 11 | 文档增删改、评论、评分、收藏、归档、文档库操作 |
| 73 | 公文模块 | 24 | 与协同对应 + 签收/交换/转版式/模板保存 |
| 74 | 调查模块 | 4 | 发布、待办生成、审核、投票 |
| 75 | 会议模块 | 14 | 发起/邀请/回执/更新/撤销/结束、无纸化会议 |
| 76 | 新闻模块 | 3 | 发布、审核、删除 |
| 77 | 综合办公 | 1 | 待办生成 |
| 78 | 组织模型 | 39 | 单位/部门/人员/岗位/职级/组/兼职岗增删改 |
| 79 | 计划模块 | 5 | 新建、删除、回复、总结、更新 |
| 80 | 项目管理 | 3 | 新建、删除、修改 |
| 81 | 督办模块 | 1 | 数据变更 |
| 82 | 小智模块 | 1 | AI应用增减 |
| 83 | 全局模块 | 8 | 代理增删改、飞书推送、密码修改、工作时间 |
| 84 | 日程模块 | 3 | 领导日程增删改 |
| 85 | CIP集成 | 10 | 电子签章、DEE任务、超级节点、三方待办、套件/插件变更 |
| 86 | cap4模块 | 12 | 表单提交前后、触发、批量操作、业务配置变更 |
| 87 | 表单模块 | 11 | 表单增删改停启、另存、触发起 |
| 88 | 门户模块 | 10 | 门户增删、空间增删改、数据刷新 |

---

## 一、讨论模块 (68)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 讨论-发布 | `com.seeyon.apps.bbs.event.BbsAddEvent` | `BbsArticleBO` |
| 讨论-回复 | `com.seeyon.apps.bbs.event.BbsReplyEvent` | `memberId`, `BbsArticleBO` |

## 二、公告模块 (69)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 公告-发布 | `com.seeyon.apps.bulletin.event.BulletinAddEvent` | `BulDataBO` |
| 公告-审核 | `com.seeyon.apps.bulletin.event.BulletinAuditEvent` | `BulDataBO` |

## 三、任务管理模块 (70)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 日程-新建 | `com.seeyon.apps.calendar.event.CalEventAddEvent` | `CalEventBO`, `sharedMembers`, `curMemberList` |
| 日程-删除 | `com.seeyon.apps.calendar.event.CalEventDeleteEvent` | `CalEventBO` |
| 日程-更新 | `com.seeyon.apps.calendar.event.CalEventUpdateEvent` | `CalEventBO`, `relatedUser`, `sharedMembers` |
| 任务-新建 | `com.seeyon.apps.taskmanage.event.TaskInfoAddEvent` | `TaskInfoBO` |
| 任务-删除 | `com.seeyon.apps.taskmanage.event.TaskInfoDeleteEvent` | `TaskInfoBO` |
| 任务-完成 | `com.seeyon.apps.taskmanage.event.TaskInfoFinishEvent` | `TaskInfoBO` |
| 任务-更新 | `com.seeyon.apps.taskmanage.event.TaskInfoUpdateEvent` | `TaskInfoBO`, `oldTask` |

## 四、协同模块 (71)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 添加评论 | `CollaborationAddCommentEvent` | `commentId` |
| 添加跟踪人员 | `CollaborationAddTrackMemberEvent` | `objectId`(summaryId), `affairId` |
| 打印事件 | `CollaborationAffairPrintEvent` | `affairs` (List\<CtpAffair\>) |
| 流程处理(事项分配) | `CollaborationAffairsAssignedEvent` | `currentAffair`, `affairs` |
| 指定回退 | `CollaborationAppointStepBackEvent` | `templateId`, `bodyType`, `currentAffair`, `senderId`, `userId`, `canceledAffairs`, `selectTargetNodeId` |
| 流程自动提交返回 | `CollaborationAutoCommitEvent` | `resultBO` |
| 自动跳过 | `CollaborationAutoSkipEvent` | `queues` (Queue\<Map\>) |
| 流程撤销 | `CollaborationCancelEvent` | `type`, `message`, `templateId`, `bodyType`, `senderId`, `userId`, `affectAffairList` |
| 取消跟踪 | `CollaborationCancelTrackEvent` | `objectId`, `affairId` |
| 协同处理完成 | `CollaborationDealEvent` | `contextBO`(ColFinishContextBO) |
| 删除事项 | `CollaborationDelEvent` | `affair` |
| 协同结束(含终止) | `CollaborationEndEvent` | `operationType`, `currentUser` |
| 协同结束(不含终止) | `CollaborationFinishEvent` | `currentAffairId`, `affairId`, `bodyType`, `affair`, `summary`, `mainProcessId` |
| 节点超期 | `CollaborationNodeOverdueEvent` | `comment`, `affair`, `summary`, `assignedAffairs` |
| 流程处理 | `CollaborationProcessEvent` | `comment`, `templateId`, `bodyType`, `affair`, `isFormAudited`, `isVouched`, `senderId`, `userId`, `assignedAffairs` |
| 待办接收时间改变 | `CollaborationReceivetimeChangeEvent` | `affairId` |
| 保存待发/草稿 | `CollaborationSaveDraftEvent` | `affair`, `colSummary` |
| 流程发起 | `CollaborationStartEvent` | `affair`, `summary`, `referId`, `referType`, `sendtype`, `from`, `assignedAffairs` |
| 流程回退 | `CollaborationStepBackEvent` | `templateId`, `bodyType`, `affair`, `senderId`, `userId`, `canceledAffairs` |
| 流程终止 | `CollaborationStopEvent` | `templateId`, `bodyType`, `affair`, `senderId`, `userId`, `mainProcessId` |
| 流程取回 | `CollaborationTakeBackEvent` | `affair` |
| 暂存待办 | `CollaborationTemporaryEvent` | `comment`, `templateId`, `bodyType`, `affair`, `senderId`, `userId` |
| 节点替换/移交 | `CollaborationTransferEvent` | `comment`, `originalAffair`, `toAffairs` |
| 模板删除 | `com.seeyon.ctp.common.template.event.TemplateDeleteEvent` | `processId`, `formId`, `templateId` |
| 模板保存 | `com.seeyon.ctp.common.template.event.TemplateSaveEvent` | `ctpTemplate`, `ctpTemplateAuth`, `payload`, `pt`(ProcessTemplete) |

## 五、知识管理模块 (72)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 文档新建 | `DocAddEvent` | `DocResourceBO`, `createType` |
| 文档批量删除 | `DocBatchCancelFavoriteEvent` | `sourceIdCollection` |
| 文档评论 | `DocCommentEvent` | `DocResourceBO`, `memberId` |
| 文档删除 | `DocDeleteEvent` | `sourcesID`, `appKey` |
| 文档收藏 | `DocFavoriteEvent` | `sourceId` |
| 文档归档 | `DocFiledEvent` | `sourcesID`, `docId`, `appKey` |
| 文档评分 | `DocGradeEvent` | `DocResourceBO`, `memberId` |
| 新增文档库 | `DocLibAddEvent` | `doclib`(DocLibBO) |
| 删除文档库 | `DocLibDeleteEvent` | `doclib`(DocLibBO) |
| 更新文档库 | `DocLibUpdateEvent` | `doclib`, `olddoclib` |
| 文档修改 | `DocUpdateEvent` | `DocResourceBO` |

## 六、公文模块 (73)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 新增意见 | `EdocAddCommentEvent` | `commentId` |
| 添加跟踪人 | `EdocAddTrackMemberEvent` | `objectId`, `affairId` |
| 打印 | `EdocAffairPrintEvent` | `affairs` |
| 待办生成 | `EdocAffairsAssignedEvent` | `currentAffair`, `affairs`, `isStart` |
| 指定回退 | `EdocAppointStepBackEvent` | `templateId`, `bodyType`, `currentAffair`, `senderId`, `userId`, `selectTargetNodeId` |
| 自动跳过 | `EdocAutoSkipEvent` | `queues` |
| 流程撤销 | `EdocCancelEvent` | `type`, `message`, `templateId`, `bodyType`, `summaryId`, `senderId`, `userId` |
| 取消跟踪 | `EdocCancelTrackEvent` | `objectId`, `affairId` |
| 删除事项 | `EdocDelEvent` | `affair` |
| 流程结束 | `EdocFinishEvent` | `affair`, `userId`, `finishTime` |
| 节点超期 | `EdocNodeOverdueEvent` | `comment`, `affair` |
| 流程处理 | `EdocProcessEvent` | `comment`, `templateId`, `bodyType`, `affair`, `isFormAudited`, `isVouched`, `senderId`, `userId`, `assignedAffairs` |
| 接收时间改变 | `EdocReceivetimeChangeEvent` | `affairId` |
| 流程发起 | `EdocStartEvent` | `affair`, `assignedAffairs` |
| 回退 | `EdocStepBackEvent` | `templateId`, `bodyType`, `affair`, `senderId`, `userId`, `selectTargetNodeId` |
| 终止 | `EdocStopEvent` | `summaryId`, `affair`, `userId`, `finishTime` |
| 取回 | `EdocTakeBackEvent` | `affair` |
| 暂存待办 | `EdocTemporaryEvent` | `comment`, `templateId`, `bodyType`, `affair`, `senderId`, `userId` |
| 移交 | `EdocTransferEvent` | `affair`, `toAffairs` |
| 公文交换 | `com.seeyon.apps.exchange.bo.ExchangeEvent` | `user`, `exchangeDataManager`, `bizExchangeData` |
| WPS转版式 | `com.seeyon.apps.wpstrans.listener.WpsTransEvent` | `summary`, `affair` |
| 签收 | `com.seeyon.v3x.edoc.event.EdocSignEvent` | `edocSignReceipt`, `sendDetailId` |
| 保存模板 | `EdocTemplateSaveEvent` | `templateId` |

## 七、调查模块 (74)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 发布 | `InquiryAddEvent` | `InquirySurveybasicBO` |
| 生成待办 | `InquiryAffairsAssignedEvent` | `affairs`(List\<CtpAffair\>) |
| 审核 | `InquiryAuditEvent` | `InquirySurveybasicBO` |
| 投票 | `InquiryVoteEvent` | `InquirySurveybasicBO`, `memberId` |

## 八、会议模块 (75)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 待办生成 | `MeetingAffairsAssignedEvent` | `affairs`, `isStart` |
| 撤销 | `MeetingDeleteEvent` | `meeting`(MeetingBO), `currentUserId`, `createTime` |
| 结束 | `MeetingFinishEvent` | `meeting`, `currentUserId`, `createTime` |
| 邀请 | `MeetingInviteEvent` | `meeting`, `currentUserId` |
| 回执 | `MeetingReplyEvent` | `role`, `replyState`, `meeting`, `replyTime`, `currentUserId` |
| 发起 | `MeetingSendEvent` | `meeting`, `currentUserId`, `createTime` |
| 更新 | `MeetingUpdateEvent` | `meeting`, `currentUserId`, `createTime` |
| 创建会议组件 | `MeetingComponentEvent` | `mcDataItem`, `mcEventType`(McEventType) |
| 检查设备绑定 | `CheckMeetingDeviceBindEvent` | `meetingId` |
| 登录类型变更 | `LoginTypeChangeEvent` | `meetingId`, `oldType`, `newType` |
| 合并批注 | `MergeAnnotationEvent` | `meetingId`, `materialId`, `annotationIds`, `currentUserId` |
| OFD转换 | `TransToOfdEvent` | `meetingId`, `agendaId`, `fileIdList` |
| 会议投票 | `VoteEvent` | `meetingId`, `methodName` |
| 枚举删除 | `com.seeyon.ctp.common.ctpenum.events.EnumDeleteEvent` | `enumBean`(CtpEnumBean) |

## 九、新闻模块 (76)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 发布 | `NewsAddEvent` | `NewsDataBO` |
| 审核 | `NewsAuditEvent` | `NewsDataBO` |
| 删除 | `NewsDeleteEvent` | `NewsDataBO` |

## 十、综合办公模块 (77)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 待办生成 | `OfficeAffairsAssignedEvent` | `affairs`, `isStart` |

## 十一、组织模型模块 (78)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 个人信息更新 | `PersonalInfoUpdateEvent` | - |
| M3离线初始化 | `M3OfflineInitEvent` | `params`, `accountLastMap`, `currentAccountId`, `departmentLastMap`, `relatedMemberMap`, `memberLastMap`, `postLastMap`, `levelLastMap`, `teamListMap` |
| 人员关联信息变更 | `com.seeyon.v3x.peoplerelate.event.RelateEvent` | - |
| 元数据列变更 | `MetadataColumnChangedEvent` | `CtpMetadataColumn` |
| 元数据列失效 | `MetadataColumnDisableEvent` | `disabledLabels`, `CtpMetadataColumn`, `tableId`, `orgAccountId` |
| 新增单位 | `AddAccountEvent` | `account`(V3xOrgAccount) |
| 新增管理员 | `AddAdminMemberEvent` | `member`, `isBatch` |
| 批量新增人员 | `AddBatchMemberEvent` | `batchMembers`(List\<V3xOrgMember\>) |
| 新增兼职岗 | `AddConCurrentPostEvent` | `rel`(V3xOrgRelationship) |
| 新增部门 | `AddDepartmentEvent` | `dept`(V3xOrgDepartment) |
| 新增职级(政务) | `AddDutyLevelEvent` | `dutyLevel`(V3xOrgDutyLevel) |
| 新增vJoin人员 | `AddJoinMemberEvent` | `member`, `isBatch` |
| 新增职级 | `AddLevelEvent` | `level`(V3xOrgLevel) |
| 新增人员 | `AddMemberEvent` | `member`(V3xOrgMember), `isBatch` |
| 新增岗位 | `AddPostEvent` | `post`(V3xOrgPost) |
| 新增组 | `AddTeamEvent` | `team`(V3xOrgTeam) |
| 管理员角色人员变动 | `AdminRoleMemberChangeEvent` | `roleName`, `v3xOrgAccount`, `cancelMember`, `addMember` |
| 变更多维组织部门人员 | `ChangeDepartMemberEvent` | `newDept`, `deptMember` |
| 变更多维组织部门角色 | `ChangeDepartRoleEvent` | `newDept`, `rolelist` |
| 修改密码 | `ChangePwdEvent` | `member`, `userAgentFromEnum` |
| 删除单位 | `DeleteAccountEvent` | `account` |
| 删除兼职岗 | `DeleteConCurrentPostEvent` | `rel`(V3xOrgRelationship) |
| 删除部门 | `DeleteDepartmentEvent` | `dept`(V3xOrgDepartment) |
| 删除政务职级 | `DeleteDutyLevelEvent` | `dutyLevel`(V3xOrgDutyLevel) |
| 删除人员 | `DeleteMemberEvent` | `member` |
| 删除岗位 | `DeletePostEvent` | `post` |
| 删除组 | `DeleteTeamEvent` | `team`, `entityTypeIdes` |
| 人员跨单位调整 | `MemberAccountChangeEvent` | `oldAccount`, `account` |
| 人员内部转编外 | `MemberInnerToOuterEvent` | `member` |
| 更新人员部门 | `MemberUpdateDeptEvent` | `member`, `newDepartmentId`, `oldDepartmentId` |
| 移动部门 | `MoveDepartmentEvent` | `oldDepartment`, `department` |
| 更新单位 | `UpdateAccountEvent` | `oldAccount`, `account` |
| 更新兼职岗 | `UpdateConCurrentPostEvent` | `oldRel`, `newRel` |
| 更新部门 | `UpdateDepartmentEvent` | `oldDept`, `dept` |
| 更新部门角色 | `UpdateDeptRoleEvent` | `oldRoleList`, `newRoleList`, `department` |
| 更新政务职级 | `UpdateDutyLevelEvent` | `oldDutyLevel`, `dutyLevel` |
| 更新职级 | `UpdateLevelEvent` | `level`, `oldLevel` |
| 更新人员 | `UpdateMemberEvent` | `member`, `oldMember` |
| 更新人员角色 | `UpdateMemberRoleEvent` | `member`, `oldMemberRole`, `newMemberRole` |
| 更新岗位 | `UpdatePostEvent` | `post`, `oldPost` |
| 更新组 | `UpdateTeamEvent` | `oldTeam`, `team` |

## 十二、计划模块 (79)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 新建 | `PlanAddEvent` | `PlanBO` |
| 删除 | `PlanDeleteEvent` | `PlanBO` |
| 回复 | `PlanReplyEvent` | `PlanBO`, `currentUserId` |
| 总结 | `PlanSummaryEvent` | `PlanBO` |
| 更新 | `PlanUpdateEvent` | `PlanBO` |

## 十三、项目管理模块 (80)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 新建 | `ProjectAddEvent` | `ProjectBO` |
| 删除 | `ProjectDeleteEvent` | `projectId`, `projectPhaseIds` |
| 修改 | `ProjectUpdateEvent` | `ProjectBO`, `oldProjectBO`, `addPhases`, `updatePhases`, `deletePhaseIds` |

## 十四、督办模块 (81)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 数据变更 | `SupervisionDataChangeEvent` | `operateEnum`, `supervisionId` |

## 十五、小智模块 (82)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| AI应用增减 | `XiaozAiAppChangeEvent` | `handleType`, `xiaozAiAppList` |

## 十六、全局模块 (83)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 飞书审批推送 | `ExtIntegrationEvent` | `extSummary` |
| V5统一待办推送 | `ExtIntegrationExtendEvent` | - |
| 代理-新建 | `AgentAddEvent` | `agent` |
| 代理-删除缓存 | `AgentDeleteCacheEvent` | `agent` |
| 代理-取消 | `AgentDeleteEvent` | `agent` |
| 代理-修改 | `AgentUpdateEvent` | `agent`, `oldAgent` |
| 修改REST密码 | `ChangePasswordEvent` | - |
| 工作时间设置 | `WorkTimeSetEvent` | - |

## 十七、日程模块 (84)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 领导日程-新建 | `LeaderAgendaAddEvent` | - |
| 领导日程-删除 | `LeaderAgendaDeleteEvent` | - |
| 领导日程-更新 | `LeaderAgendaUpdateEvent` | - |

## 十八、CIP集成模块 (85)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 电子签章用印记录 | `SealSignRecordAddEvent` | `sealSignRecord`(SealSignRecordDTO) |
| 印章同步 | `SealSynEvent` | `enterpriseConfig` |
| DEE任务绑定通知 | `BusinessDeeTaskChangeEvent` | `delProcessIdList`, `deeTaskId`, `processId`, `handleType`, `formId` |
| 删除超级节点 | `DeleteSuperNodeEvent` | `workflowDBSuperNode`, `deeBindIdList`, `processId`, `formId` |
| 保存超级节点 | `SaveSuperNodeEvent` | 同上 |
| 三方待办 | `ThirdPendingEvent` | `addList`, `deleteList`, `modifyList` |
| 套件变更 | `SuiteChangeEvent` | `suite`, `suiteId`, `handleType` |
| 三方应用接入变更 | `ThirdpartyAppChangeEvent` | `fromAppLink`, `thirdpartyPortalVo`, `handleType` |
| 插件变更 | `SeeyonConfigChangeEvent` | `pluginOldMap`, `pluginId`, `handleType` |
| 用户绑定信息 | `SaveUserConfigEvent` | `userBindingConfig` |

## 十九、cap4模块 (86)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 表单触发回调 | `CAP4FormTriggerFireEvent` | `taskEvent`, `param`, `masterId`, `moduleId`, `moduleType` |
| 业务配置变更 | `CapBizConfigChangeEvent` | `handleType`, `bizConfigBeanList` |
| 表单触发成功后 | `CAPFormTriggerAfterEvent` | - |
| 表单保存后 | `FormBeanAfterSaveEvent` | - |
| **表单数据入库后** | `FormDataAfterSubmitEvent` | - |
| **表单数据入库前** | `FormDataBeforeSubmitEvent` | - |
| 删除表单触发 | `FormDelete4WorkflowEvent` | `formId` |
| 触发保存数据 | `FormTriggerAfterSaveDataEvent` | - |
| 无流程应用绑定删除 | `UnflowFormBindAuthDeleteEvent` | `formId`, `bindAuthId` |
| 批量全文索引 | `CAPBatchOperationIndexEvent` | `masterIds` |
| 批量操作日志 | `CAPBatchOperationLogEvent` | `formId`, `successNum`, `errorNum`, `capLogType` |
| 明细表导入 | `CAPDetailImportEvent` | - |

## 二十、表单模块 (87)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 表单移除缓存 | `FormBeanRemoveCacheEvent` | `userId`, `formId` |
| 表单另存复制 | `FlowFormOtherSaveEvent` | `orginalformDefinitionId`, `newFormDefinitionID` |
| 流程表单保存全部(后) | `FlowFormSaveAllAfterEvent` | `formDefinitionId` |
| 流程表单保存全部 | `FlowFormSaveAllEvent` | `formDefinitionId`, `formTemplateSaveAllParam` |
| 表单数据入库后 | `FormDataAfterSubmitEvent` | `ctpContentAll`(CtpContentAllBean), `parma` |
| 表单列表删除 | `FormListDeleteEvent` | `formDefinitionId`(List\<Long\>) |
| 表单列表停用 | `FormListDisableEvent` | `formIds`(List\<Long\>) |
| 表单列表启用 | `FormListEnableEvent` | `formIds`(List\<Long\>) |
| 表单修改 | `FormUpdateEvent` | `formId` |
| 触发器 | `FormTriggerFireEvent` | `param`, `formBean`, `masterId`, `moduleId`, `moduleType` |
| 表单数据入库前 | `FormDataBeforeSubmitEvent` | - |

## 二十一、门户模块 (88)

| 事件名 | 事件类 | 关键属性 |
|--------|--------|---------|
| 删除门户 | `DeletePortalEvent` | `portalId` |
| 新建门户 | `ModifyPortalEvent` | `portalSets`, `portalSet` |
| 新建空间 | `AddSpaceEvent` | `spaceFix`, `spaceSecurities` |
| 删除空间 | `DeleteSpaceEvent` | `spaceFix`, `spaceSecurities` |
| 刷新所有空间数据 | `RefreshAllSpaceAndDataEvent` | - |
| 刷新栏目数据 | `RefreshSectionDataEvent` | - |
| 刷新空间详情 | `RefreshSpaceDetailDataEvent` | - |
| 刷新空间列表 | `RefreshSpaceListDataEvent` | - |
| 刷新门户空间 | `RefreshVPortalDataEvent` | - |
| 更新空间 | `UpdateSpaceEvent` | `spaceFix`, `spaceSecurities` |

---

## 使用建议

### 场景 → 事件映射

| 业务需求 | 推荐监听事件 |
|---------|------------|
| 流程审批后推数据 | `CollaborationFinishEvent` / `EdocFinishEvent` |
| 流程发起时校验 | `AbstractWorkflowEvent.onBeforeStart()` |
| 流程处理实时通知 | `CollaborationProcessEvent` / `EdocProcessEvent` (async=true) |
| 人员入职同步 | `AddMemberEvent` |
| 部门调整同步 | `UpdateDepartmentEvent` / `MoveDepartmentEvent` |
| CAP4表单提交后处理 | `FormDataAfterSubmitEvent` |
| 文档发布通知 | `DocAddEvent` |
| 会议变更通知 | `MeetingUpdateEvent` / `MeetingInviteEvent` |
| 公告发布推消息 | `BulletinAddEvent` |
| 电子签章回调 | `SealSignRecordAddEvent` |
| 门户/空间变更 | `ModifyPortalEvent` / `AddSpaceEvent` |

### 事件类命名规律

- 协同：`com.seeyon.apps.collaboration.event.CollaborationXxxEvent`
- 公文：`com.seeyon.apps.edoc.event.EdocXxxEvent`
- 组织：`com.seeyon.ctp.organization.event.XxxMemberEvent` / `XxxDepartmentEvent` 等
- cap4：`com.seeyon.cap4.form.modules.event.XxxEvent`
- 表单：`com.seeyon.ctp.form.modules.event.XxxEvent`

> 官方完整文档：https://open.seeyoncloud.com/seeyonapi/8.2/event/
