# 组件规范 v1.0

# 弹窗

### 表单

**样式名**

设置表单列数 

| 列数 | class名称 | 是否必须 |
|----|:-------------:|----:| 
| 1列 | `jtform` | 是 | 
| 2列 | `jtform jtform_2` |  是 | 
| 3列 | `jtform jtform_3` |  是 | 
| 4列 | `jtform jtform_4` |  是 | 

标注必填字段

| class名称 | 是否必须 |
| ---- |----:|
| `required` | 否|

**属性**

| 属性 | 说明 | 是否必须 | 
|----|:-------------:|----:| 
| `novalidate` | 去除浏览器默认验证提示 | 是| 
| `name` | 表单名称 | 是| 

**html**
``` html
<form name="org_edit_view" novalidate class="jtform">
    <table>
        <tbody>
            <tr>
                <td class="required">字段1</td>
                <td>
                    <input type="text" ng-model="editData.name" name="name" ng-required="true">
                </td>              
            </tr>                                            
        </tbody>
    </table>
</form>
```


### 弹出层表单

**样式名**

设置表单列数

| class名称 | 功能 | 是否必须 | 
|---- |:-------------:| ----:| 
| `M-popwinHead` | 弹出层头部，一般存放标题和关闭按钮 | 是| 
| `M-popwinBody` | 弹出层内容，一般存放表单 |  是| 
| `M-popwinFooter` | 弹出层底部，一般存放按钮 |  是 | 
| `M-popwinClose` | 关闭按钮 |  否| 

**html**
``` html
<div class="M-popwinHead">
    <p>{{title}}</p>
    <em class="M-popwinClose" ng-click="cancel()"></em>
</div>
<div class="M-popwinBody">
    <form name="form" novalidate class="jtform">
        <table>
            <tbody>
                <tr>
                    <td class="required">字段1</td>
                    <td>
                        <input type="text" ng-model="editData.name" name="name" ng-required="true">
                    </td>              
                </tr>                                            
            </tbody>
        </table>
    </form>
</div>
<div class="M-popwinFooter">
    <button ng-click="submit()" class="M-btn">确定</button>
</div>
```

**controller**
``` js
/**
* 父层
* @params Sys    Service 系统对象
*/
JT_CONTEXT.controller('org_ctrl', function (Sys,...){
    ....
    var modalInstance = $uibModal.open({
        templateUrl: 'platform/page/application/wftype_select_view.html',
        controller: 'wftype_select_ctrl',
        backdrop: "static",
        resolve: {
            params: function () {
                return { 
                    title: "选择分类"
                    , msg: $scope.editData 
                };
            }
        }
    });
    var openEditDialog = function (title, data) {
        ....
        // 重新调整弹窗位置
        modalInstance.opened.then(Util.resetPopWinPos);
    }; 
}
/**
* 弹出层
* @params Sys    Service 系统对象
* @params params Object  系统对象
*/
JT_CONTEXT.controller('org_edit_ctrl', function (Sys,params,...){
    ....
    // 弹出层标题
    $scope.title = params.title;
    // 统一由Sys.formValidate验证表单，$scope.ok为原有提交操作
    $scope.submit = function(){
        Sys.formValidate($scope.form, $scope.ok);
    }
})
```

### 系统提示

totast提示框，弹出3秒后自动消失

**controller**

``` js
// 引入Sys服务
JT_CONTEXT.controller('org_edit_ctrl', function (Sys,...){
    ....
    Sys.notify("下班");
})
```

确认提示框

**属性**
| class名称 | 功能 | 是否必须 | 
|---- |:-------------:| ----:| 
| `popTitle` | 弹框标题 | 是| 
| `showPop` | 是否显示 |  是| 
| `popTpl` | 弹框内容模板 |  是 | 
| `popBtns` | 按钮配置 |  否| 

**html**
``` html
<ngpopwin title="{{popTitle}}" show="showPop" btns="popBtns" models="popData" tpl={{popTpl}}></ngpopwin>
```

**controller**

``` js
// 引入Sys服务
JT_CONTEXT.controller('org_edit_ctrl', function (Sys,...){
    ....
    
    // 确认对话框
    function showPopWin(){
        $scope.popTitle = '删除';
        $scope.showPop = true;
        $scope.popTpl = '<div class="confirmBox">确定要删除吗？</div>';
        $scope.popBtns = [{
            "val": "确定"
            , "type": "ok"
            , "onClick": function() {
                // 确定后操作
                ...
            }
        }];
    }
})
```

### 表单元素

日期类型(支持时间)

**属性**
| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `type` | 类型 | 否 | {String} 默认 "date" 日期 "time" 日期+时间 |

**html**
``` html
<!--日期-->
<ngdatetime model="editData.beginTime" ng-model="editData.beginTime" type="date" name="beginTime" ng-required="true">
<!--日期+时间-->
<ngdatetime model="editData.beginTime" ng-model="editData.beginTime" type="time" name="endTime" ng-required="true">
```

**js**
``` js
// 回填数据
scope.editData = {
    beginTime: "04/06/2017"
    ,endTime: "05/26/2017 20:22"
};
```

下拉框

**属性**
| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `convert-to-number` | 选中值转为{number}类型 | 否 | NAN |

**html**
``` html
<select name="menu" ng-model="editData.menu" ng-required="true" convert-to-number>
    <option ng-repeat="option in options" value="{{option.id}}">{{option.name}}</option>
</select>
```

**js**
``` js
// 回填数据
scope.editData = {
    menu: 1
};
// 模拟获取下拉框数据源
timeout(function(){
    scope.options = [{
        id: 1
        ,name: "菜单一"
    }
    ,{
        id: 2
        ,name: "菜单二"
    }];
}, 600)
```

文本输入

**html**
``` html
<input type="text" ng-model="editData.name" name="name" ng-required="true">
```

**js**
``` js
// 回填数据
scope.editData = {
    name: 1
};
```

checkbox 多选框

**属性**
| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `data` | 数据源 | 是 | {Object} |
| `field` | 数据源字段，表示用该字段作为checkbox值 | 是 | {String} |
| `push-to-array` | 将值注入到数组 | 是 | NAN |
| `options` | 选中值转为{number}类型 | 是 | {String} |
| `ng-model` | model值 | 是 | {String} 注意必须绑定item.$result |
| `model` | 回填数据 | 是 | {String} |
| `checkbox-required` | 是否必填 | 否 | NAN |

**html**
``` html
<input type="hidden" ng-model="editData.multis" checkbox-required>
<label ng-repeat="item in multiOptions" class="ckbox">
    <input type="checkbox"
            data="{{item}}"
            field="value"
            push-to-array
            options="multiOptions"
            ng-model="item.$result"
            model="editData.multis">
    {{item.text}}
</label>
``` 

**js**
``` js
// 初始化
scope.multiOptions = [
    {value: 1, text: "学生1"}
    ,{value: 2, text: "学生2"}
    ,{value: 3, text: "学生3"}
];
// 回填数据
scope.editData = {
    multis: [1, 3]
};
```

radio 单选框

**属性**
| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `value` | 数据源字段，表示用该字段作为radio值 | 是 | {String} |
| `convert-to-number` | 将值自动转成数组，用于正确回填 | 否 | NAN |
| `ng-model` | model值 | 是 | {String} |

**html**
``` html
<input type="hidden" ng-model="editData.single" ng-required="true">
<label ng-repeat="item in singleOptions" class="rdbox">
    <input type="radio"
           convert-to-number
           value="{{item.value}}"
           ng-model="editData.single">
    {{item.text}}
</label>
```

**js**
``` js
// 初始化
scope.singleOptions = [
    {value: 1, text: "学生1"}
    ,{value: 2, text: "学生2"}
    ,{value: 3, text: "学生3"}
];
// 回填数据
scope.editData = {
    single: 2
};
```

选择输入框

**html**
``` html
<div class="input-group">
    <input id="appTitle" type="text" name="appTitle" ng-model="editData.appTitle" readonly />
    <span class="input-group-btn">
        <button class="btn btn-default" ng-click="onAppSelectClick()">选</button>
    </span>		
</div>
``` 

树

**属性**
| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `config` | angular-ui-tree的Options | 是 | 默认 {Object} {} |
| `data` | 数据源 | 是 | 默认 {Array} `[]` |
| `on-toggle` | 折叠节点事件 | 否 | 参数 $data: {Object} `node` 树节点对象 |
| `on-create` | 创建节点事件 | 否 | 参数 $data: {Object} `this` 树实例, $all[1]: {Object} `this` 树实例 |
| `on-create-first` | 创建首个节点事件 | 否 | 参数 无 |
| `on-edit` | 编辑节点事件 | 否 | 参数 $data: {Object} `this` 树实例, $all[1]: {Object} `this` 树实例 |
| `on-delete` | 删除节点事件 | 否 | 参数 $data: {Object} `this` 树实例, $all[1]: {Object} `this` 树实例 |
| `on-select` | 选中节点事件 | 否 | 参数 $data: {Object} `node` 树节点对象 |
| `on-droped` | 移动节点事件 | 否 | 参数 $id: {String} 当前节点id $dest_id: {String} 目标节点id $cb {Function} 保存节点数据的回调 |
| `show-nochild` | 是否显示独立层级节点标识（没有任何子节点）条件 | 否 | 默认 {String} `!(node.children && node.children.length > 0)`|
| `enable-create` | 是否允许新增节点条件 | 否 | 默认 {String} `true`|
| `enable-edit` | 是否允许编辑节点条件 | 否 | 默认 {String} `true`|
| `enable-delete` | 是否允许删除节点条件 | 否 | 默认 {String} `true`|
| `enable-drag` | 是否允许拖动节点 | 否 | 默认 {String} `true`|
| `enable-toggle` | 是否允许折叠节点条件 | 否 | 默认 {String} `node.children && node.children.length > 0`|
| `show-selected` | 是否显示选择结果 | 否 | 默认 {Boolean} `false`|
| `height` | 容器高度 | 否 | 默认 {Number \| String} eg: 400 或 "400"|
| `title` | 树型区标题 | 否 | 默认 {String} "树形区"|
| `fix-plus-bar` | 是否固定显示新增按钮 | 否 | 默认 {String} `false`|

**html**
``` html
<!-- 普通树 -->
<ngtree config="treeOptions"
        data="data"
        on-toggle="toggle($data)"
        on-create="newSubItem($data)"
        on-edit="editSubItem($data)"
        on-delete="deleteSubItem($data)"
        on-select="treeNodeSelected($data)"
        show-nochild="!(node.children && node.children.length > 0)"
        enable-toggle="node.children && node.children.length > 0"
        enable-create="true"
        enable-edit="true"
        enable-delete="true"
        title="故障类型"
        enable-drag="true"
        on-droped="ondroped($id, $dest_id, $cb)">
</ngtree>

<!-- 单选树 -->
<ngtree config="treeOptions"
        data="data"
        on-toggle="toggle($data)"
        on-select="treeNodeSelected($data)"
        enable-create="false"
        enable-edit="false"
        enable-delete="false"
        show-selected="true"
        height="400">
</ngtree>
``` 

> 注：
> - `fix-plus-bar` 为 `true` 时，新增按钮会一直显示（原来因为数据为空默认显示的新增按钮不会再出现），对应的处理函数为 `on-create-first`
> - `enable-drag` 为 `true` 时，树节点最右侧操作按钮用于移动节点，要移动节点到最后尾，请先将该节点移动到倒数第二个节点，然后再与最后一个节点对调位置。原因是数据节点保存需要找到被替换节点的id，直接放到最后时，替换节点不存在所以不允许拖动，会提示操作失败
> - `on-droped` 的处理函数示例
> ``` js
>    $scope.ondroped = function(id, target_id, cb){
>        console.log(id, target_id);
>        // 模拟请求数据
>        setTimeout(function(){
>            // 是否保存成功
>            var asyncResult = false;
>            // 第二个参数可以自定义操作失败信息
>            cb(asyncResult, "操作失败！");
>        }, 2000);
>    }
> ```

列表单选，多选

**属性**
| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `result-tpl` | 已选择内容区模板 | 是 | {Strign} |
| `grid-conf` | angular-ui-grid配置 | 是 | {Object} |
| `selected-data` | 已选中数据 | 是 | 单选 {Object} 多选 {Array} |
| `grid-title` | angular-ui-grid标题  | 否 | {String} 默认 "数据区" |
| `result-title` | 已选择内容区标题 | 否 | {String} 默认 "已选择" |

**html**
```html
    <ngdatapicker 
        grid-conf="gridOptions"
        selected-data="selectedData"
        result-tpl="<span ng-repeat='item in selectedData'>\{\{item.entity.name}}(\{\{item.entity.jobnum}}); </span>"
        grid-title="用户列表">
    </ngdatapicker>
```

布局模板(左边，右边布局均适用)

**html**
```html
<style>
/*
* 让表格高度自适应
* class命名：P-{页面文件名}，如P-srcField_view
* 表格高度： 100vh（浏览器视窗总高度）- 其他部件高度总和（如网站头部、底部、tab高度）
*/
.P-srcField_view [ui-grid]{
    height: -webkit-calc(100vh - 304px);
}
</style>
<div class="P-srcField_view" ng-controller="srcField_ctrl" ng-init="init()">
    <div class="cbox page_btn_box">
        <div class="hd">操作栏</div>
        <div class="bd">  
            <div class="searchbox">
                <div class="leftbox">
                    <button type="button" class="M-btn " ng-click="onAddClick()">新增</button>
                    <button type="button" class="M-btn " ng-click="onEditClick()">编辑</button>
                    <button type="button" class="M-btn " ng-click="onDeleteClick()">删除</button>
                    <ngpopwin title="{{popTitle}}" show="showPop" btns="popBtns" models="popData" tpl={{popTpl}}></ngpopwin>
                </div>
            </div>
            ...
            <div ui-grid="gridOptions" ui-grid-selection ui-grid-edit ui-grid-pagination ui-grid-resize-columns></div>
        </div>
    </div>
</div>
```

# 标题栏标题修改

1. 普通表格标题，直接修改模板，如fitcategory_view.html

   Tips：模板路径可以在控制台获取，前提必须开启调试模式（开启方式参见"调试方法"）
   
    ```html
        <div class="col-sm-8" ng-if="category" ng-controller="fltcategory_ctrl" ng-init="init()">
            <div class="cbox page_btn_box">
                <div class="hd">操作栏</div>
                <div class="bd">        
                    <div class="searchbox">
                        ...
                    </div>
                    <div ui-grid="gridOptions" ui-grid-selection ui-grid-edit ui-grid-pagination ui-grid-resize-columns></div>
                </div>
            </div>
        </div>
    ```   
   
1. 树标题（单选，多选，不可选），修改指令`ngtree`配置

    ```html
        <ngtree config="treeOptions"
                ...
                title="故障类型"
                result-title="已选择">
        </ngtree>
    ```

1. 列表标题（单选，多选），修改指令`ngdatapicker`配置
   
    ```html
        <ngdatapicker grid-conf="gridOptions"
                      grid-title="用户列表"
                      result-title="已选择">
        </ngdatapicker>
    ```