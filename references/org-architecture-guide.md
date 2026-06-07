# 致远CTP组织架构开发完整指南

基于官方SeeyonAPI事件索引 + 平台源码 + 真实客开项目实战总结。

## 一、组织数据模型

### 1.1 核心实体

| 实体 | 平台类 | 数据库表 | 说明 |
|------|--------|---------|------|
| 单位 | `V3xOrgAccount` | `org_unit` (type='Account') | 顶层组织节点 |
| 部门 | `V3xOrgDepartment` | `org_unit` (type='Department') | 单位下部门 |
| 人员 | `V3xOrgMember` | `org_member` | 组织成员 |
| 岗位 | `V3xOrgPost` | `org_post` | 人员所属岗位 |
| 职级 | `V3xOrgLevel` | `org_level` | 人员职级 |
| 组 | `V3xOrgTeam` | `org_team` | 虚拟团队 |
| 兼职岗 | `V3xOrgRelationship` | - | 跨单位兼职关系 |
| 凭证 | `V3xOrgPrincipal` | `org_principal` | 登录名/密码 |

### 1.2 关键API（平台Manager）

| Bean名 | 类 | 用途 |
|--------|---|------|
| `orgManagerDirect` | `OrgManagerDirectImpl` | 组织数据增删改 + 事件分发 |
| `orgManager` | `OrgManagerImpl` | 组织数据查询 |
| `memberManager` | `MemberManager` | 人员管理 |
| `roleManager` | `RoleManager` | 角色管理 |
| `conPostManager` | `ConcurrentPostManager` | 兼职管理 |
| `principalManager` | `PrincipalManagerImpl` | 登录凭证管理 |
| `orgMemberRoleManager` | `OrgMemberRoleManager` | 人员角色关系 |

## 二、平台事件分发机制

### 2.1 源码触发点（OrgManagerDirectImpl）

平台在数据操作完成后自动派发事件，客开无需手动触发：

```java
// 示例：新增人员后自动派发
public void addMembers(List<V3xOrgMember> members) {
    for (V3xOrgMember member : members) {
        // ... 执行新增逻辑 ...
        AddMemberEvent event = new AddMemberEvent(this);
        event.setMember(member);
        EventDispatcher.fireEvent(event);
    }
}
```

### 2.2 完整事件清单（39个组织事件）

#### 单位事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddAccountEvent` | 新增单位后 | `account` |
| `UpdateAccountEvent` | 更新单位后 | `account`, `oldAccount` |
| `DeleteAccountEvent` | 删除单位后 | `account` |

#### 部门事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddDepartmentEvent` | 新增部门后 | `dept` |
| `UpdateDepartmentEvent` | 更新部门后 | `dept`, `oldDept` |
| `DeleteDepartmentEvent` | 删除部门后 | `dept` |
| `MoveDepartmentEvent` | 移动部门后 | `department`, `oldDepartment` |
| `UpdateDeptRoleEvent` | 部门角色变更后 | `newRoleList`, `oldRoleList`, `department` |

#### 人员事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddMemberEvent` | 新增人员后 | `member`, `isBatch` |
| `AddBatchMemberEvent` | 批量新增后 | `batchMembers`(List) |
| `AddJoinMemberEvent` | 新增vJoin人员后 | `member`, `isBatch` |
| `UpdateMemberEvent` | 更新人员后 | `member`, `oldMember` |
| `DeleteMemberEvent` | 删除人员后 | `member` |
| `ChangePwdEvent` | 修改密码后 | `member` |
| `MemberAccountChangeEvent` | 人员跨单位调动后 | `account`, `oldAccount` |
| `MemberUpdateDeptEvent` | 人员部门变更后 | `member`, `newDepartmentId`, `oldDepartmentId` |
| `MemberInnerToOuterEvent` | 内部转编外后 | `member` |
| `UpdateMemberRoleEvent` | 人员角色变更后 | `member`, `oldMemberRole`, `newMemberRole` |
| `AdminRoleMemberChangeEvent` | 管理员角色变动后 | `roleName`, `addMember`, `cancelMember` |

#### 岗位事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddPostEvent` | 新增岗位后 | `post` |
| `UpdatePostEvent` | 更新岗位后 | `post`, `oldPost` |
| `DeletePostEvent` | 删除岗位后 | `post` |

#### 职级事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddLevelEvent` | 新增职级后 | `level` |
| `UpdateLevelEvent` | 更新职级后 | `level`, `oldLevel` |
| `DeleteLevelEvent` | 删除职级后 | `level` |
| `AddDutyLevelEvent` | 新增政务职级后 | `dutyLevel` |
| `UpdateDutyLevelEvent` | 更新政务职级后 | `dutyLevel`, `oldDutyLevel` |

#### 组事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddTeamEvent` | 新增组后 | `team` |
| `UpdateTeamEvent` | 更新组后 | `team`, `oldTeam` |
| `DeleteTeamEvent` | 删除组后 | `team` |

#### 兼职/关系事件

| 事件类 | 触发时机 | 关键属性 |
|--------|---------|---------|
| `AddConCurrentPostEvent` | 新增兼职后 | `rel` |
| `UpdateConCurrentPostEvent` | 更新兼职后 | `oldRel`, `newRel` |
| `DeleteConCurrentPostEvent` | 删除兼职后 | `rel` |
| `ChangeDepartMemberEvent` | 多维组织部门人员变更 | `newDept`, `deptMember` |
| `ChangeDepartRoleEvent` | 多维组织部门角色变更 | `newDept`, `rolelist` |

## 三、组织同步实战模式（REST API驱动）

### 3.1 架构设计

```
外部HR/主数据系统
      │
      ▼
POST /seeyon/rest/orgData/manage/xxx  (BaseResource + JAX-RS)
      │
      ▼
OrgDataResource (控制器层)
  ├── 参数解析：JSON → VO
  ├── 去重检查：OrgCacheUtil.tryInsert(key) 60秒
  ├── 实体查询：getEntity(code) → 判断新增/更新
      │
      ▼
OrgManagerDirect (平台API层)
  ├── addMember / updateMember / addDepartment ...
  ├── addSecondPost / cancelMemberByAccountId ...
      │
      ▼
EventDispatcher.fireEvent(Event)  (事件层)
      │
      ▼
@ListenEvent 监听者 (其他模块响应)
```

### 3.2 REST API完整接口

```java
@Path("orgData")
public class OrgDataResource extends BaseResource {

    // ===== 查询接口 =====

    @POST @Path("org")
    public List<AccountVo> getOrg() { }  // 查所有单位

    @POST @Path("dept")
    public List<DepartmentVo> getDept() { }  // 查部门(需传accountCode)

    @POST @Path("member")
    public List<MemberVo> getMember() { }  // 查人员(需传accountCode)

    @POST @Path("post")
    public List<PostVo> getPost() { }  // 查所有岗位

    @POST @Path("level")
    public List<LevelVo> getLevel() { }  // 查所有职级

    // ===== 管理接口 =====

    @POST @Path("manage/account")
    public Map<String, Object> createOrUpdateAccount(JSONObject json) { }

    @POST @Path("manage/department")
    public Map<String, Object> createOrUpdateDepartment(JSONObject json) { }

    @POST @Path("manage/member")
    public Map<String, Object> createOrUpdateMember(JSONObject json) { }

    @POST @Path("manage/post")
    public Map<String, Object> createOrUpdatePost(JSONObject json) { }

    @POST @Path("manage/level")
    public Map<String, Object> createUpdateOrLevel(JSONObject json) { }

    @POST @Path("manager/partJob")
    public Map<String, Object> createOrUpdatePartJob(JSONObject json) { }

    @POST @Path("manage/department/role")
    public Map<String, Object> createOrUpdateRole(JSONObject json) { }
}
```

### 3.3 人员同步核心流程

```java
// 入参JSON结构
{
    "code": "EMP001",           // 人员编码（唯一标识）
    "name": "张三",
    "accountCode": "COMPANY_A",
    "deptCode": "DEPT_SALES",
    "postCode": "POST_MANAGER",
    "levelCode": "L5",
    "loginName": "zhangsan",    // 登录名
    "email": "zhangsan@xx.com",
    "telNumber": "13800138000",
    "gender": "1",              // 1=男 2=女
    "status": "0"               // 0=在职 1=离职
}

// 处理流程
1. OrgCacheUtil.tryInsert(code)           // 60秒去重
2. getEntity(V3xOrgMember.class, code)    // 判断是否存在
3. 不存在 → 新建 V3xOrgMember + V3xOrgPrincipal
   存在 → 更新 V3xOrgMember，检查 loginName 是否变更
4. 设置基本属性: name, email, gender, sort, status
5. 关联部门: dept(by code) → member.setOrgDepartmentId()
6. 关联岗位: post(by code) → member.setOrgPostId()
7. 关联职级: level(by code) → member.setOrgLevelId()
8. 关联汇报人: reporter(by code) → 更新关系表
9. 扩展属性: OrgExtAttrUtil.updateExtAttr() → extAttr1-10
10. orgManagerDirect.addMember(member) 或 updateMember(member)
    → 平台自动派发 AddMemberEvent / UpdateMemberEvent
11. 跨单位调动: cancelMemberByAccountId + 删除旧关系 + 新建
    → 平台自动派发 MemberAccountChangeEvent
```

### 3.4 部门同步核心流程

```java
// 入参JSON结构
{
    "code": "DEPT_SALES",
    "name": "销售部",
    "accountCode": "COMPANY_A",
    "superiorCode": "DEPT_PARENT",  // 上级部门编码
    "sort": "1"
}

// 处理流程
1. OrgCacheUtil.tryInsert(code)
2. 判断新增/更新
3. 设置基本属性
4. 关联单位: account(by code) → dept.setOrgAccountId()
5. 关联上级: superior(by code) → dept.setSuperiorId()
   (如果superiorCode等于accountCode → 挂到单位根下)
6. orgManagerDirect.addDepartment(dept) 或 updateDepartment(dept)
7. 处理部门角色(depManager/depLeader):
   查找对应人员 → addRels/delRels → fireEvent(UpdateDeptRoleEvent)
```

### 3.5 兼职处理

```java
// 同单位兼职（副岗）
member.addSecondPost(deptId, postId);
memberManager.updateMember(member);

// 跨单位兼职
conPostManager.createOne(concurrentPostBO);
// 参数: cMemberId, cAccountId, cDeptId, cPostId, cLevelId, cRoleIds, sortId, code
```

### 3.6 辅助工具类

#### OrgCacheUtil — 防重复推送
```java
public class OrgCacheUtil {
    private static final ConcurrentHashMap<String, Long> CACHE = new ConcurrentHashMap<>();
    private static final long TTL_MILLIS = 60_000; // 60秒

    public static boolean tryInsert(String key) {
        long now = System.currentTimeMillis();
        Long lastTime = CACHE.get(key);
        if (lastTime != null && now - lastTime < TTL_MILLIS) {
            return false;  // 重复请求，跳过
        }
        CACHE.put(key, now);
        return true;
    }
}
```

#### OrgExtAttrUtil — 扩展属性更新
```java
public class OrgExtAttrUtil {
    // 将 map 中的值按顺序写入 extAttr1 ~ extAttr10
    public static void updateExtAttr(Long entityId,
        Map<String, Object> attrMap,
        AddressBookCustomerFieldInfoManager fieldInfoManager) {
        // 遍历 attrMap，按fieldName匹配extAttrN字段
        // 调用 fieldInfoManager 更新
    }
}
```

## 四、数据库查询模式

### 4.1 核心表

```sql
-- 单位/部门（同一张表，type区分）
SELECT * FROM org_unit WHERE type = 'Account' AND is_internal = 1  -- 单位
SELECT * FROM org_unit WHERE type = 'Department' AND is_internal = 1 -- 部门

-- 人员（含联表查询）
SELECT m.id, m.code, d.code AS dept_code, m.name, p.login_name,
       m.is_enable, m.is_deleted, m.org_post_id, m.org_level_id
FROM org_member m
LEFT JOIN org_principal p ON p.member_id = m.id
LEFT JOIN org_unit d ON d.id = m.org_department_id
WHERE m.is_internal = 1 AND m.is_admin = 0

-- 岗位
SELECT * FROM org_post

-- 职级
SELECT * FROM org_level

-- 唯一标识查实体（通用方法）
SELECT id FROM {tableName} WHERE code = ? AND is_deleted = 0
```

### 4.2 数据查询VO

```java
// 利用extends层级减少重复
OrgDataVo         // 基类: id, code, name, is_deleted, is_enable, sort
├── AccountVo     // + 无额外字段
├── DepartmentVo  // + accountId, accountName, superiorCode
├── MemberVo      // + deptCode, deptId, accountId, loginName,
│                 //   telNumber, email, sex, postName, levelName
├── PostVo        // + 无额外字段
└── LevelVo       // + 无额外字段
```

## 五、组织主数据扩展（CAP4公式引擎）

### 5.1 注册扩展字段

```java
// 启动时向通讯录增加扩展字段
public class P251121PluginInitializer extends AbstractSystemInitializer {
    @Override
    public void initialize() {
        // 增加 EXT_ATTR_71 ~ EXT_ATTR_80 共10个扩展字段到通讯录
        // 字段类型: 单行文本/下拉框等
    }
}
```

### 5.2 扩展公式变量

```java
// 在CAP4公式设置中显示为可选变量
org_meta_currentUser_extAttr71         // 当前登录人员.扩展属性71
org_meta_currentUserAccount_extAttr71  // 当前登录人员单位.扩展属性71
org_meta_currentUserDepartment_extAttr71 // 当前登录人员部门.扩展属性71
// ... 最多到 extAttr80
```

### 5.3 取值实现

```java
public class P251121Utils {
    // 反射调用入口
    public String org_meta_currentUser_extAttr71() {
        return getMetaValue("EXT_ATTR_71", "COLUMN_FIELD_NAME");
    }

    private String getMetaValue(String extAttrName, String columnName) {
        Long currentUserId = AppContext.currentUser().getId();
        // 通过 AddressBookCustomerFieldInfoManager 查询扩展字段值
    }
}
```

## 六、监听组织事件（@ListenEvent）

```java
public class OrgSyncToExternalListener {

    @ListenEvent(event = AddMemberEvent.class, async = true)
    public void onMemberAdd(AddMemberEvent event) {
        V3xOrgMember member = event.getMember();
        externalApi.syncMember(member, "add");
    }

    @ListenEvent(event = UpdateMemberEvent.class, async = true)
    public void onMemberUpdate(UpdateMemberEvent event) {
        V3xOrgMember member = event.getMember();
        if (member.isValid()) {
            externalApi.syncMember(member, "update");
        } else {
            externalApi.syncMember(member, "del");  // 人员离职 → 通知外部删除
        }
    }

    @ListenEvent(event = DeleteMemberEvent.class, async = true)
    public void onMemberDelete(DeleteMemberEvent event) {
        externalApi.syncMember(event.getMember(), "del");
    }

    @ListenEvent(event = AddDepartmentEvent.class, async = true)
    public void onDeptAdd(AddDepartmentEvent event) {
        externalApi.syncDepartment(event.getDept(), "add");
    }

    @ListenEvent(event = UpdateDepartmentEvent.class, async = true)
    public void onDeptUpdate(UpdateDepartmentEvent event) {
        externalApi.syncDepartment(event.getDept(), "update");
    }

    @ListenEvent(event = DeleteDepartmentEvent.class, async = true)
    public void onDeptDelete(DeleteDepartmentEvent event) {
        externalApi.syncDepartment(event.getDept(), "del");
    }

    @ListenEvent(event = ChangePwdEvent.class, async = true)
    public void onChangePwd(ChangePwdEvent event) {
        externalApi.syncPassword(event.getMember().getId(),
                                  event.getMember().getLoginName());
    }
}
```

## 七、常见组织操作API速查

### 7.1 平台API

```java
// ===== OrgManagerDirect =====
orgManagerDirect.addAccount(account);           // 新增单位
orgManagerDirect.updateAccounts(list);          // 更新单位
orgManagerDirect.deleteAccount(id);             // 删除单位
orgManagerDirect.addDepartment(dept);           // 新增部门
orgManagerDirect.updateDepartment(dept);        // 更新部门
orgManagerDirect.deleteDepartment(id);          // 删除部门
orgManagerDirect.addMember(member);             // 新增人员(单个)
orgManagerDirect.addMembers(list);              // 新增人员(批量)
orgManagerDirect.updateMember(member);          // 更新人员
orgManagerDirect.updateMembers(list);           // 更新人员(批量)
orgManagerDirect.deleteMember(id);              // 删除人员
orgManagerDirect.addPosts(list);                // 新增岗位
orgManagerDirect.updatePost(post);              // 更新岗位
orgManagerDirect.deletePost(id);                // 删除岗位
orgManagerDirect.addLevels(list);               // 新增职级
orgManagerDirect.updateLevel(level);            // 更新职级
orgManagerDirect.deleteLevel(id);               // 删除职级

// ===== MemberManager =====
memberManager.addSecondPost(memberId, deptId, postId); // 添加副岗

// ===== OrgManager (查询) =====
orgManager.getAccountById(id);                  // 按ID查单位
orgManager.getDepartmentById(id);               // 按ID查部门
orgManager.getMemberById(id);                   // 按ID查人员
orgManager.getPostById(id);                     // 按ID查岗位
orgManager.getLevelById(id);                    // 按ID查职级
orgManager.getAccountsByCode(code);             // 按编码查单位
orgManager.getDepartmentsByCode(code);          // 按编码查部门
orgManager.getMembersByCode(code);              // 按编码查人员
```

### 7.2 人员状态判断

```java
member.isValid()           // 是否在职
member.isDeleted()         // 是否已删除
member.isEnable()          // 是否启用
member.getLoginName()      // 获取登录名
member.getEmail()          // 获取邮箱
member.getMobileNumber()   // 获取手机号
```

## 八、注意事项

1. **编码作为唯一键**：code字段是组织数据的外部唯一标识，通过`getEntity()`按code+is_deleted=0查询
2. **60秒去重**：使用`ConcurrentHashMap`+TTL防止短时间内重复同步
3. **async=true推荐**：组织事件监听务必异步，避免影响平台自身的组织操作事务
4. **跨单位调动**：涉及`cancelMemberByAccountId`+`MemberAccountChangeEvent`事件，与原单位逻辑不同
5. **部门角色触发事件**：`createOrUpdateRole`中直接操作关系表并手动触发`UpdateDeptRoleEvent`
6. **扩展属性**：通过`AddressBookCustomerFieldInfoManager`更新extAttr字段，最多支持extAttr1-10（可扩展至extAttr80）
7. **职级查询注意**：某些实现中职级查询的是`org_post`表（疑似bug），应以`org_level`表为准
