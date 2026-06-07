# CAP4表单开发指南

基于数字安徽、徽商合同、安徽建工、合规院等项目的CAP4表单实战总结。

## 表单数据获取

### 在超级节点中获取表单数据
```java
@Override
public SuperNodeResponse executeAction(Map<String, Object> params) {
    // 标准获取链路
    Map<String, Object> formData = (Map<String, Object>) params.get("CTP_FORM_DATA");
    FormDataMasterBean masterBean = (FormDataMasterBean) formData.get("formDataBean");
    FormTableBean table = masterBean.getFormTable();

    // 通过显示名获取字段
    FormFieldBean field = table.getFieldBeanByDisplay("合同名称");
    String contractName = masterBean.getFieldValue(field.getName());
}
```

### 通过CAP4FormManager获取
```java
// 通过模板编码获取表单定义
CAP4FormManager formManager = ...; // Spring注入
FormBean formBean = FormCap4Kit.getFormBean(formManager, "dhdtz");
```

## 字段取值方法

### FormCap4Kit / CAP4FormKit 完整方法

```java
// 基础取值
Object getFieldValue(FormDataBean bean, String displayName);
String getFieldStrValue(FormDataBean bean, String displayName);
Long getFieldLongValue(FormDataBean bean, String displayName);
Double getFieldDouble(FormDataBean bean, String displayName);

// 枚举转换
String getEnumvalue(Long enumId);    // 枚举数据库Value
String getEnumName(Long enumId);     // 枚举显示名
String getEnumCode(Long enumId);     // 枚举编码

// 人员/部门/岗位
String getDeptCode(FormDataBean bean, String fieldName);
String getDeptName(FormDataBean bean, String fieldName);
String getMemberCode(FormDataBean bean, String fieldName);
String getMemberName(FormDataBean bean, String fieldName);
String getMemberLoginName(FormDataBean bean, String fieldName);
String getPostCode(FormDataBean bean, String fieldName);
String getPostName(FormDataBean bean, String fieldName);

// 日期格式化
String getFieldTime(FormDataBean bean, String displayName);

// 字段赋值
void setCellValue(FormDataBean bean, String displayName, Object value);
```

## 子表遍历

### 单子表模式
```java
List<FormDataSubBean> subBeans = FormCap4Kit.getSubBeans(masterBean);
if (subBeans != null) {
    for (FormDataSubBean subBean : subBeans) {
        Object value = FormCap4Kit.getFieldValue(subBean, "产品名称");
        // 处理...
    }
}
```

### 多子表模式
```java
List<List<FormDataSubBean>> allSubBeans = FormCap4Kit.getAllSubBeans(masterBean);
for (List<FormDataSubBean> subTable : allSubBeans) {
    FormTableBean subTableDef = subTable.get(0).getFormTable();
    String tableName = subTableDef.getTableName();
    for (FormDataSubBean row : subTable) {
        Object val = FormCap4Kit.getFieldValue(row, "字段显示名");
    }
}
```

## 附件处理

### 获取附件下载URL
```java
// 取上传控件第一个附件
FormFieldBean attachField = table.getFieldBeanByDisplay("附件");
String firstFileUrl = getAttachmentBoundUrl(attachField);
String firstFileName = getAttachmentBoundFilename(attachField);

// 取所有附件
List<Map<String, String>> attachments = getAttachmentList(attachField);
// 返回 [{name, filename, url}, ...]
```

### 附件下载到本地
```java
File file = FormCap4Kit.downloadFile(fieldBean);
```

## 自定义控件开发

### 后端控件类（FormFieldCustomCtrl子类）
```java
public class QccCtrl extends FormFieldCustomCtrl {
    // 实现必要方法
}
```

### 前端runtime.js
```javascript
// callBackendMethod 调用后端Spring Bean
var params = {
    templateCode: getFormTemplateCode(),
    formData: collectFormData()
};

callBackendMethod("qccManager", "checkCompany", params, function(resp) {
    if (resp.success && resp.data) {
        fillFormFields(resp.data);
    }
});
```

### 自定义按钮（CommonBtn子类）
```java
public class DataReporting2InspurButton extends CommonBtn {
    // 按钮key在控件配置中指定
}
```

### 控件配置目录结构
```
cfgHome/component/<componentId>/
├── pluginCfg.xml                    # 组件插件定义
├── spring/
│   └── spring-<componentId>-manager.xml  # 注册后端Bean
└── i18n/
    ├── export_to_js.xml             # 前端国际化导出
    └── <componentId>_zh_CN.properties
```

## 常见表单数据问题

### 字段找不到
- 确认使用的是**显示名**而非字段编码
- 检查表单模板是否已发布最新版本
- 子表字段需要通过`FormDataSubBean`而非`FormDataMasterBean`获取

### 枚举值取不到
- 使用`EnumKit`（如果项目有）或直接SQL查询`CTP_ENUM_ITEM`表
- 注意枚举值的数据库存储格式（通常为枚举ID）

### 日期格式不对
- 使用`DateKit`格式化，注意各项目DateKit实现可能不同
- 金蝶等外部系统可能要求特定日期格式（如`yyyy-MM-dd`）
