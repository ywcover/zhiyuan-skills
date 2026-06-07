# JSP维护页面开发指南

基于致远CTP客开项目的JSP维护页面实战总结。

## 页面结构模板

### 标准CRUD维护页面
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>页面标题</title>
    <!-- 内联CSS样式 -->
    <style type="text/css">
        .container { margin: 20px; }
        .search-bar { margin-bottom: 15px; }
        .search-bar input, .search-bar select { margin-right: 10px; }
        .data-table { width: 100%; border-collapse: collapse; }
        .data-table th, .data-table td { border: 1px solid #ddd; padding: 8px; text-align: center; }
        .dialog-overlay { display:none; position:fixed; top:0; left:0; width:100%; height:100%;
                          background:rgba(0,0,0,0.5); z-index:1000; }
        .dialog-box { position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
                      background:white; padding:20px; border-radius:5px; min-width:600px; }
        .btn { padding: 5px 15px; cursor: pointer; border: 1px solid #ccc; border-radius: 3px; }
        .btn-primary { background: #1890ff; color: white; border-color: #1890ff; }
        .btn-danger { background: #ff4d4f; color: white; border-color: #ff4d4f; }
    </style>
</head>
<body>
<div class="container">
    <!-- 搜索栏 -->
    <div class="search-bar">
        关键词: <input type="text" id="keyword" />
        <select id="status">
            <option value="">全部状态</option>
            <option value="1">启用</option>
            <option value="0">停用</option>
        </select>
        <button class="btn btn-primary" onclick="search()">查询</button>
        <button class="btn btn-primary" onclick="openAddDialog()">新增</button>
    </div>

    <!-- 表格容器 -->
    <div id="tableContainer"></div>

    <!-- 分页 -->
    <div id="pagination"></div>
</div>

<!-- 编辑弹窗 -->
<div class="dialog-overlay" id="editDialog">
    <div class="dialog-box">
        <h3 id="dialogTitle">新增记录</h3>
        <form id="editForm">
            <!-- 表单字段 -->
        </form>
        <div style="text-align:right; margin-top:15px;">
            <button class="btn" onclick="closeDialog()">取消</button>
            <button class="btn btn-primary" onclick="save()">保存</button>
        </div>
    </div>
</div>

<script type="text/javascript">
// 页面加载
$(function() { search(); });

// 分页信息
var currentPage = 1;
var pageSize = 10;

// 查询列表
function search(page) {
    if (page) currentPage = page;
    $.ajax({
        url: '/seeyon/ext/xxx.do?method=list',
        type: 'POST',
        data: {
            keyword: $('#keyword').val(),
            status: $('#status').val(),
            currentPage: currentPage,
            pageSize: pageSize
        },
        success: function(resp) {
            renderTable(resp.data);
            renderPagination(resp.total);
        }
    });
}

// 保存
function save() {
    var formData = $('#editForm').serializeJSON();
    $.ajax({
        url: '/seeyon/ext/xxx.do?method=save',
        type: 'POST',
        contentType: 'application/json',
        data: JSON.stringify(formData),
        success: function(resp) {
            if (resp.success) {
                alert('保存成功');
                closeDialog();
                search();
            } else {
                alert('保存失败: ' + resp.message);
            }
        }
    });
}
</script>
</body>
</html>
```

## 与Controller交互

### Controller返回JSP视图
```java
public class ContractMappingController extends BaseController {
    public ModelAndView list(HttpServletRequest req, HttpServletResponse res) {
        // 查询数据并放入Model
        ModelAndView mv = new ModelAndView("apps/plugin/contract/mappingList");
        mv.addObject("typeConfigs", contractMappingService.listTypes());
        return mv;
    }

    @AjaxAccess
    public ModelAndView save(HttpServletRequest req, HttpServletResponse res) {
        // AJAX处理，返回JSON
        Map<String, Object> result = new HashMap<>();
        try {
            contractMappingService.save(params);
            result.put("success", true);
        } catch (Exception e) {
            result.put("success", false);
            result.put("message", e.getMessage());
        }
        return renderJSON(result);
    }
}
```

### Controller Bean注册
```xml
<!-- spring-contract-controller.xml -->
<beans default-autowire="byName">
    <bean name="/ext/contractManage.do"
          class="com.seeyon.apps.contract.controller.ContractManageController"/>
    <bean name="/ext/contractMapping.do"
          class="com.seeyon.apps.contract.controller.ContractMappingController"/>
</beans>
```

### 免登录配置
```xml
<!-- needless_check_login_xxx.xml -->
<config>
    <path>/ext/contractManage.do</path>
    <method>download</method>
</config>
```

## 常用前端技巧

### 分页组件
使用致远CTP标准分页或自制简单分页：
```javascript
function renderPagination(total) {
    var totalPages = Math.ceil(total / pageSize);
    var html = '<div style="margin-top:10px;">';
    html += '<span>共' + total + '条，第' + currentPage + '/' + totalPages + '页</span> ';
    if (currentPage > 1) html += '<a href="#" onclick="search(1)">首页</a> ';
    if (currentPage > 1) html += '<a href="#" onclick="search(' + (currentPage-1) + ')">上一页</a> ';
    if (currentPage < totalPages) html += '<a href="#" onclick="search(' + (currentPage+1) + ')">下一页</a> ';
    if (currentPage < totalPages) html += '<a href="#" onclick="search(' + totalPages + ')">末页</a> ';
    html += '</div>';
    $('#pagination').html(html);
}
```

### 弹窗控制
```javascript
function openDialog(title, data) {
    $('#dialogTitle').text(title);
    $('#editForm').clear();  // 清空
    if (data) $('#editForm').populate(data);  // 回填
    $('#editDialog').show();
}

function closeDialog() {
    $('#editDialog').hide();
}
```

### AJAX错误处理
```javascript
function ajaxPost(url, data, callback) {
    $.ajax({
        url: url,
        type: 'POST',
        contentType: 'application/json',
        data: JSON.stringify(data),
        success: function(resp) {
            if (resp.success) {
                callback(resp);
            } else {
                alert('操作失败: ' + (resp.message || '未知错误'));
            }
        },
        error: function(xhr, status, error) {
            alert('网络错误: ' + error);
        }
    });
}
```
