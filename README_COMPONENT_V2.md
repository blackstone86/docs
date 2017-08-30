# 组件规范 v2.0

# 弹窗

## 普通弹窗

**js属性**

| 属性 | 说明 | 值类型 | 默认值 | 是否必填 |  
|-----|---|---|----|----|
| `width` | 弹窗宽度 | {Number\|String} eg: 600/"600"/"90%"，数字时单位为"px" | "80%" | 否 |
| `height` | 弹窗高度 | {Number} 单位为"px" | 460 | 否 |
| `buttons` | 按钮类型 | {Array-String} eg: ["ok", "cancel"]，ok: 确定按钮 cancel: 取消按钮 | ["ok", "cancel"] | 否 |
| `buttonsTxt` | 按钮文本 | {Object} | {ok: "确定", cancel: "取消"} | 否 |
| `templateUrl` | 弹窗模板 | {String} | N/A | 是 | 
| `controller` | 弹窗控制器 | {String} | N/A | 是 |
| `title` | 弹窗标题 | {String} | "" | 是，必须设置具体标题 |
| `params` | 弹窗参数 | {Mix} | {} | 否 |
| `ok` | 弹窗确定事件回调处理 | {Function} | function(result){} result: 弹窗控制器调用\$scope.\$close(result)传来的参数 | 否 |
| `cancel` | 弹窗取消事件回调处理 | {Function} | function(reason){} reason: 弹窗控制器调用\$scope.\$dismiss(reason)传来的参数 | 否 |
| `opened` | 弹窗打开回调处理 | {Function} | function(){} | 否 |
| `closed` | 弹窗关闭回调处理 | {Function} | function(){} | 否 |
| `rendered` | 弹窗渲染完成回调处理 | {Function} | function(){} | 否 |
| `hidefooter` | 是否隐藏底部按钮 | {Boolean} | false | 否 |  

**html**
``` html
<button ng-click="popwin()">popwin</button>
```

**controller**
``` js
$scope.popwin = function(){
    $Sys.popwin({
        width: "80%",
        height: 650,
        buttons: ["ok", "cancel", "other"],
        buttonsTxt: {
            ok: "确定啦！！"
            ,cancel: "取消啦！！"
            ,other: "自定义按钮！！"
        },
        templateUrl: "popwin_template.html",
        controller: "popwin_ctrl",
        title: "父级设备类型选择",
        params: {
            data: ['item1', 'item2', 'item3']
        },
        ok: function(result){
            if(result){
                switch(result){
                    // 点击确定按钮处理
                    case 'ok':
                        console.log(result);
                        break;
                    // 点击自定义按钮处理
                    case 'other':
                        console.log(result);
                        break;
                }
            }
        },
        cancel: function(reason){
            console.log("popwin cancel reason: " + reason);
        }
    });
}
```

> 注：`$Sys` 须引入服务
> ```js
> JT_CONTEXT.controller('org_edit_ctrl', ["Sys", function ($Sys) {
>   ...    
> }]);
> ```

``` js
// 弹窗控制器
JT_CONTEXT.controller('popwin_ctrl', ["$scope", "$params", function (scope, params) {
    // 接收参数
    if(params) console.log("popwin_ctrl $params: "); console.log(params);
    // 确认事件处理
    scope.$on("$POPWIN_OK", function () {
        scope.$close({type:'ok', data:scope.editData});
    });
    // 右上角关闭事件处理
    scope.$on("$POPWIN_CANCEL", function () {
        scope.$dismiss({type:'cancel', data:scope.editData});
    });
    // 自定义按钮事件
    scope.$on("$POPWIN_OTHER", function () {
        scope.$close({type:'other', data:scope.editData});
    });       
}]);
```

> 注意：
> - `height` 属性必须用数字
> - 业务需要自定义按钮时，设置 `hidefooter` 为 `true`，按钮在 `templateUrl` 指定的模板中添加，
    当 `hidefooter` 为 `false` (默认值)，提供确定、取消两种按钮，在 `buttons` 中配置需要显示的按钮，弹窗确定事件通过 `$scope.$on("$POPWIN_CONFIRM", function(){...})`监听，右上角关闭或取消按钮事件通过 `$scope.$on("$POPWIN_CANCEL", function(){...})`监听，如果取消按钮是自定义的将不能监听，要自行处理，弹窗实例的 `close` 方法为 `$scope.$close`，`dismiss` 方法为 `$scope.$dismiss`
> - 弹窗控制器接收参数，要引入 `$params` ，该参数为父容器控制器中配置的`params`配置项
> - 如何修改弹窗默认配置？ 在 `/script/system.js` 中修改 `popWinDefaultConf` 变量即可
>   ``` js
>    // 弹窗默认配置
>    var popWinDefaultConf = {
>        // 弹窗默认宽度
>        width: "80%" 
>        // 弹窗默认高度
>        ,height: 460
>        // 弹窗按钮默认文本
>        ,buttonsTxt: {ok: "确定", cancel: "取消"}
>        // 弹窗按钮默认种类配置
>        ,buttons: ["ok", "cancel"]
>        // 弹窗是否默认隐藏底部按钮
>        ,hidefooter: false
>        // 弹窗默认标题
>        ,title: ""
>        // 弹窗默认传参
>        ,params: {}
>    } 
>   ```
> - 弹窗控制器如何注册按钮事件？ 用`scope.$on`注册，事件名为`$POPWIN_` + 按钮类型大写（eg: `ok`按钮的注册事件名为`$POPWIN_OK`）
> - 在按钮事件中如何关闭弹窗？ 用`scope.$close`或`scope.$dismiss`方法，传递的参数可以被主控制器的相应回调函数接收，注意`scope.$close`对应的回调是`ok`，
`scope.$dismiss`对应的回调是`cancel`

## 确认弹窗

**js属性**

| 属性 | 说明 | 值类型 | 默认值 | 是否必填 |  
|-----|---|---|----|----|
| `width` | 弹窗宽度 | {Number\|String} eg: 600/"600"/"90%"，数字时单位为"px" | "80%" | 否 |
| `height` | 弹窗高度 | {Number} 单位为"px" | 460 | 否 |
| `buttons` | 按钮类型 | {Array-String} eg: ["ok", "cancel"]，ok: 确定按钮 cancel: 取消按钮 | ["ok", "cancel"] | 否 |
| `buttonsTxt` | 按钮文本 | {Object} | {ok: "确定", cancel: "取消"} | 否 |
| `title` | 弹窗标题 | {String} | "" | 是，必须设置具体标题 |
| `content` | 弹窗内容 | {String} | "" | 是，必须设置具体内容 |
| `ok` | 弹窗确定事件回调处理 | {Function} | function(result){} result: 弹窗控制器调用\$scope.\$close(result)传来的参数 | 否 |
| `cancel` | 弹窗取消事件回调处理 | {Function} | function(reason){} reason: 弹窗控制器调用\$scope.\$dismiss(reason)传来的参数 | 否 |

**html**
``` html
<button ng-click="confirm()">confirm</button>
```

**controller**
``` js
$Sys.confirm({
    width: "600",
    height: 300,
    buttons: ["ok", "cancel"],
    buttonsTxt: {
        ok: "确定"
        ,cancel: "取消"
    },
    title: "hello",
    content: "确认删除？",
    ok: function(){
        console.log("confirm: ok");
    },
    cancel: function(){
        console.log("confirm: cancel");
    }
});
```

> 注意：
> - `height` 属性必须用数字
> - 不需要像`popwin` 配置 `templateUrl`、`controller`、`params`这几个参数，因为确认弹窗用的是`confirmwin`组件
> - 确认事件、取消事件回调都不会传任何参数，因为确认弹窗只需知道用户确认与否即可
> - `content`支持`html`字符串
> - 如何修改确认弹窗默认配置？ 在 `/script/system.js` 中修改 `confirmWinDefaultConf` 变量即可
>   ``` js
>    // 确认弹窗默认配置
>    var confirmWinDefaultConf = {
>        // 确认弹窗默认宽度
>        width: "60%" 
>        // 确认弹窗默认高度
>        ,height: 166
>        // 确认弹窗按钮默认文本
>        ,buttonsTxt: {ok: "确定", cancel: "取消"}
>        // 确认弹窗按钮默认种类配置
>        ,buttons: ["ok", "cancel"]
>        // 确认弹窗默认标题
>        ,title: ""
>        // 确认弹窗默认内容
>        ,content: {}
>    } 
>   ```

## totast弹窗

**controller**
``` js
$Sys.notify("下班!");

// 文本换行显示
$Sys.notify("下班!<br>去Happy~");
```

> 注意：
> - 弹窗出现3秒后自动关闭
> - 如果文本超过12个字，建议不要使用`totast弹窗`，用户3秒不一定能看完

## 图片预览弹层

**js参数**
| 属性 | 说明 | 值类型 | 默认值 | 是否必填 |
|-----|---|---|----|----|
| `show` | 显示状态 | {Boolean} | N/A | 是 |
| `data` | 图片数组 | {Array-String\|Object} eg: ["imgs/img1","imgs/img2"] 或 [{url:"imgs/img1"},{url:"imgs/img2"}] | N/A | 是 |
| `at` | 显示的图片索引，基于0 | {Number} | 0 | 否 |

**controller**
``` js
var imgs = ["demos/imgs/img1.jpg", "demos/imgs/img2.jpg", "demos/imgs/img3.jpg"];
$Sys.previewImg(true, imgs, 0);
```

# 表单

**样式设置**

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

## 日期时间

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `type` | 类型 | 否 | {String} 默认 "date" 日期；"time" 日期+时间；"month" 年份+月份 |
| `fix` | 是否固定显示日历视图 | 否 | {Boolean} 默认 "false" |
| `model` | 回填值，用于传递值给组件 | 是 | {String} 默认 "" |
| `ng-model` | 回填值，用于验证是否必填 | 是 | {String} 默认 "" |
| `disabled` | 是否不可用 | 否 | {String} 默认 "false" |

**html**
``` html
<!--日期-->
<ngdatetime model="editData.beginTime" ng-model="editData.beginTime" type="date" name="beginTime" ng-required="true"></ngdatetime>
<!--日期+时间-->
<ngdatetime model="editData.beginTime" ng-model="editData.beginTime" type="time" name="endTime" ng-required="true"></ngdatetime>
<!--年份月份-->
<ngdatetime model="editData.yearmonth" ng-model="editData.yearmonth" type="month" name="yearmonth" ng-required="true"></ngdatetime>
<!--年份月份(弹窗模式）-->
<script type="text/ng-template" id="selectmonth_view.html">
    <ngdatetime model="month" ng-model="month" type="month" fix="true"></ngdatetime>
</script>
<button class="M-btn" ng-click="selectMonth()">选择月份</button>
```

**js**

主控制器

``` js
// 回填数据
scope.editData = {
    beginTime: "2017-05-07"
    ,endTime: "2017-05-07 20:22:16"
    ,yearmonth: "2017-06"
};
// 打开选择
scope.selectMonth = function(){
    $Sys.popwin({
        width: 350,
        height: 275,
        buttons: ["ok","cancel"],
        templateUrl: "selectmonth_view.html",
        controller: "test_datetime",
        title: "选择月份",
        ok: function(result){
            if(result){
                scope.month = result; // eg: result = '2017-07'
            }
        }
    });
};
```

弹窗控制器

``` js
// 弹窗控制器
scope.$on("$POPWIN_CONFIRM", function () {
    scope.$close(scope.month);
});
```

> 注：
> - 弹窗模式下选月份，默认**选中**当前月份，如立即按`确定`按钮是会返回当前月份，如`2017-07`，在非弹窗模式下，并且没有设置回填值时，日期控件**不**选中任何值
> - `model`和`ng-model`绑定的值类型为`String`，获取年份、月份等不要误当作`Date`类型来处理！
> - 时间采用`24小时制`，只提供`小时`、`分钟`录入，不提供`秒`录入
> - 支持复制日期字符串赋值，并可直接修改该字符串，如输入错误日期格式，组件**不清空**错误的日期，但提交表单时会有提示
> - 时间选择弹窗，支持滚动鼠标滑轮修改小时、分钟，支持直接修改小时、分钟数字，但输入错误格式，点击`关闭`按钮关闭弹窗后，组件会**清空**错误的日期

## 下拉框(select)

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `convert-to-number` | 选中值转为{number}类型 | 否 | NAN |
| `dict-source` | 字典表代码 | 否 | {String} |
| `source` | 返回字典数据 | 否 | {Array} |

**html**
``` html
<select name="menu" ng-model="editData.menu" ng-required="true" convert-to-number>
    <option ng-repeat="option in options" value="{{option.id}}">{{option.name}}</option>
</select>

<!-- 填充字典数据源 -->
<select name="menu" ng-model="editData.menu_dict" ng-required="true" convert-to-number dict-source="osp_team_duty" source="menus_dict_options">
    <option ng-repeat="option in menus_dict_options" value="{{option.value}}">{{option.title}}</option>
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

## 文本输入(text)

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

## 多行文本输入(textarea)

**html**
``` html
<textarea ng-model="editData.remark" name="remark" ng-required="true"></textarea>
```

**js**
``` js
// 回填数据
scope.editData.remark = "充电机模块XS1接头松动"; 
```

## 多选框(checkbox)

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
| `dict-source` | 字典表代码 | 否 | {String} |
| `source` | 返回字典数据 | 否 | {Array} |

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

<!-- 填充字典数据源 -->
<span dict-source="osp_team_duty" source="multi_dict_options">
    <input type="hidden" ng-model="editData.multis_dict" checkbox-required>
    <label ng-repeat="item in multi_dict_options" class="ckbox">
        <input type="checkbox"
            data="{{item}}"
            field="value"
            push-to-array
            options="multi_dict_options"
            ng-model="item.$result"
            model="editData.multis_dict">
        {{item.title}}
    </label>
</span>
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

## 单选框(radio)

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `value` | 数据源字段，表示用该字段作为radio值 | 是 | {String} |
| `convert-to-number` | 将值自动转成数组，用于正确回填 | 否 | NAN |
| `ng-model` | model值 | 是 | {String} |
| `dict-source` | 字典表代码 | 否 | {String} |
| `source` | 返回字典数据 | 否 | {Array} |

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

<!-- 填充字典数据源 -->
<span dict-source="osp_team_duty" source="single_dict_options">
    <input type="hidden" ng-model="editData.single_dict" ng-required="true">
    <label ng-repeat="item in single_dict_options" class="rdbox">
        <input type="radio"
            convert-to-number
            value="{{item.value}}"
            ng-model="editData.single_dict">
        {{item.title}}
    </label>
</span>
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

# 树型组件

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `data` | 数据源 | 是 | 默认 {Array} `[]` |
| `on-toggle` | 折叠节点事件 | 否 | 参数 \$data: {Object} `node` 树节点对象 |
| `on-create` | 创建节点事件 | 否 | 参数 \$data: {Object} `this` 树实例, \$all[1]: {Object} `this` 树实例 |
| `on-create-first` | 创建首个节点事件 | 否 | 参数 无 |
| `on-edit` | 编辑节点事件 | 否 | 参数 \$data: {Object} `this` 树实例, \$all[1]: {Object} `this` 树实例 |
| `on-delete` | 删除节点事件 | 否 | 参数 \$data: {Object} `this` 树实例, \$all[1]: {Object} `this` 树实例 |
| `on-select` | 选中节点事件 | 否 | 参数 \$data: {Object} `node` 树节点对象 |
| `on-droped` | 移动节点事件 | 否 | 参数 \$id: {String} 当前节点id \$dest_id: {String} 目标节点id \$cb {Function} 保存节点数据的回调 |
| `show-nochild` | 是否显示独立层级节点标识（没有任何子节点）条件 | 否 | 默认 {String} `!(node.children && node.children.length > 0)`|
| `enable-create` | 是否允许新增节点条件 | 否 | 默认 {String} `true`|
| `enable-edit` | 是否允许编辑节点条件 | 否 | 默认 {String} `true`|
| `enable-delete` | 是否允许删除节点条件 | 否 | 默认 {String} `true`|
| `enable-drag` | 是否允许拖动节点 | 否 | 默认 {String} `false`|
| `enable-toggle` | 是否允许折叠节点条件 | 否 | 默认 {String} `node.children && node.children.length > 0`|
| `height` | 容器高度 | 否 | 默认 {Number \| String} eg: 400 或 "400" 或 "-webkit-calc(100vh -100px)"|
| `title` | 树型区标题 | 否 | 默认 {String} "虚拟设备"|
| `fix-plus-bar` | 是否固定显示新增按钮 | 否 | 默认 {String} `false`|
| `symbol` | 结果拼接符（用于分割多个已选结果） | 否 | 默认 {String} " ; "|
| `valuefield` | 提交的字段名称 | 否 | 默认 {String} `textfield`指定的字段，当`textfield`没定义，则用`textfield`的默认值 |
| `textfield` | 显示的字段名称 | 否 | 默认 {String} "text"|
| `multis` | 是否支持多选 | 否 | 默认 {String} `false`|
| `model` | 提交的字段值 | 否 | 无默认值 {Mix}|
| `modeltext` | 显示的字段值 | 否 | 默认 {String} ""|
| `format` | 格式化显示值的函数 | 否 | 默认 {Function} 空函数|
| `extendmodel` | 记录或记录集合 | 否 | 无默认值 {Array\|Object}|
| `query` | 过滤条件 | 否 | 默认 {String} ""|
| `readonly` | 是否只读（不允许新增，编辑，删除，拖动位置） | 否 | 默认 {String} `false`|
| `showtitle` | 是否显示标题栏 | 否 | 默认 {String} `true`|

**html**
``` html
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
    on-droped="ondroped($source_node, $dest_node, $cb)">
</ngtree>
``` 

> 注：
> - `fix-plus-bar` 为 `true` 时，新增按钮会一直显示（原来因为数据为空默认显示的新增按钮不会再出现），对应的处理函数为 `on-create-first`
> - `enable-drag` 为 `true` 时，树节点最右侧操作按钮用于移动节点，要移动节点到最后尾，请先将该节点移动到倒数第二个节点，然后再与最后一个节点对调位置。原因是数据节点保存需要找到被替换节点的id，直接放到最后时，替换节点不存在所以不允许拖动，会提示操作失败
> - `on-droped` 的处理函数示例
> ``` js
> $scope.ondroped = function(source_node, dest_node, cb){
>     console.log(source_node);
>     console.log(dest_node);
>     // 模拟请求数据
>     setTimeout(function(){
>       // 是否保存成功
>       var asyncResult = false;
>       // 第二个参数可以自定义操作失败信息
>       cb(asyncResult, "操作失败！");
>     }, 2000);
> }
> ```
> - `readonly` 设置为 `true` 时，即使设置了 `enable-drag` 为 `true` 也不能拖动节点
> - `enable-toggle` 设置为 `true` 是为了懒加载，固定显示文件夹图标，同时 `show-nochild` 必须设置为 `false`，否则没有子节点的树节点会出现两个图标！
> - 当没有定义 `show-nochild`，那么 `enable-toggle` 一定不能设置为 `true`，否则没有子节点的树节点会出现两个图标！
> - `enable-toggle` 与 `show-nochild` 搭配使用方式只有两种
>   - 懒加载
>    ``` html
>        <ngtree enable-toggle="true" show-nochild="false"></ngtree>
>    ```
>   - 非懒加载，两个属性都不定义
>    ``` html
>        <ngtree></ngtree>
>    ```

# 数据选择

## 树型

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `ng-model` | model值 | 是，多选时约定为 `;` 作为分隔符 | {String} |
| `model` | model值 | 是，多选时约定为 `;` 作为分隔符 | {String} |
| `modeltext` | 显示值，用于回填数据 | 否 | {String} |
| `extend-model` | 扩展对象，单选为{Object} 记录对象，多选为{Array-Object} 数组记录对象 | 否 | {String} |
| `name` | 表单元素名称 | 否（但需要辨别哪个表单元素值不合法时，必须加上) | {String} |
| `ng-required` | 是否必填 | 否 | {String} |
| `disabled` | 是否不可用 | 是否不可用 | {String} 默认 "false" |
| `config` | 数据选择器配置 | 是 | {String} |
| `config.component.type` | 弹窗内嵌组件类型 | 否 | {String} 默认 "tree" |
| `config.component.valuefield` | 树型节点提交值对应的字段名 | 否 | {String} 默认 `config.component.textfield`属性值 |
| `config.component.textfield` | 树型节点显示值对应的字段名 | 否 | {String} 默认 "text" |
| `config.component.serverurl` | api服务器域名 | 否 | {String} 默认 serverURL全局变量 |
| `config.component.datasource` | api | 是 | {String} eg:"/application/tree" |
| `config.component.condition` | 请求数据条件 | 否 | {Object} 默认 {} |
| `config.component.method` | http请求方法 | 否 | {String} 默认 "post" |
| `config.component.format` | 格式化显示值的函数 | 否 | {Function} 默认 空函数，接收一个参数，实体 |
| `config.component.conf.showNochild` | 是否显示独立层级标志（没有任何子集）| 否 | {String} 默认 "!(node.children && node.children.length > 0)" |
| `config.component.conf.enableToggle` | 允许折叠树的条件 | 否 | {String} 默认 "node.children && node.children.length > 0" |
| `config.component.conf.onToggle` | 折叠树的事件回调 | 否 | {Function} 默认 空函数，接收一个参数，树节点 |
| `config.component.conf.onSelect` | 树节点选中的事件回调 | 否 | {Function} 默认 空函数，接收一个参数，树节点 |
| `config.popwin.width` | 弹窗宽度 | 否 | {Number\|String} eg: 600/"600"/"90%"，数字时单位为"px" 默认 "80%" |
| `config.popwin.height` | 弹窗高度 | 否 | {Number} 单位为"px" 默认 650 |
| `config.popwin.title` | 弹窗标题 | 否 | {String} 默认 "数据选择" |
| `config.readonly` | 是否只读 | 否 | {Boolean} 默认 true |
| `config.nodata` | 数据源为空时的提示信息 | 否 | {String} 默认 "暂无数据可选" |
| `config.multiselect` | 是否多选 | 否 | {Boolean} 默认 false |
| `config.symbol` | 显示值分割符 | 否 | {String} 默认 " ; " |
| `config.ok` | 确定回调 | 否 | {Function} 无默认值，接收一个参数，扩展对象 |

**html**
``` html
<form name="form" novalidate class="jtform jtform_2">
    <table>
        <tbody>
            <tr>
                <td class="required">单选</td>
                <td>
                    <ngdatpicker ng-model="editData.singleTree" 
                                 model="editData.singleTree" 
                                 extend-model="extendData.singleTree" 
                                 config="singleTreeConf" 
                                 name="singleTree"
                                 ng-required="true">
                    </ngdatpicker>
                </td>
                <td class="required">多选</td>
                <td>
                    <ngdatpicker ng-model="editData.multisTree" 
                                 model="editData.multisTree" 
                                 extend-model="extendData.multisTree" 
                                 config="multisTreeConf" 
                                 name="multisTree" 
                                 ng-required="true">
                    </ngdatpicker>
                </td>
            </tr>                                
        </tbody>
    </table>
    <!--原生元素上应用-->
    <input type="text" 
           ngdatpicker
           ng-model="editData.singleTree_name"
           modeltext="editData.singleTree_name"
           model="editData.singleTree"
           extend-model="extendData.singleTree"
           config="singleTreeConf">
</form>
```

**js**
``` js
// 单选
scope.singleTreeConf = {
    component: {
        type: "tree"
        ,valuefield: "id"
        ,textfield: "text"
        ,serverurl: serverURL
        ,datasource: "/application/tree"
        ,condition: {
            _query__type__IN__t: ''
        }
        ,method: "post"
    }
    , popwin: {
        width: "60%"
        ,height: 400
        ,title: "请选择设备(单选)" 
    }
    , readonly: true
    , nodata: "暂无数据可选"
    , multiselect: false
    , ok: function(extendModel){
        console.log(extendModel);
    }
}

// 多选
scope.multisTreeConf = {
    component: {
        type: "tree"
        ,valuefield: "id"
        ,textfield: "text"
        ,serverurl: serverURL
        ,datasource: "/application/tree"
        ,condition: {
            _query__type__IN__t: ''
        }
        ,method: "post"
    }
    , popwin: {
        width: "60%"
        ,height: 400
        ,title: "请选择设备(多选)"
    }
    , readonly: true
    , nodata: "暂无数据可选"
    , multiselect: true
    , symbol: " ; "
    , ok: function(extendModel){
        console.log(extendModel);
    }    
}
```

> 注：
> - `ng-model` 与 `model` 区别，前者用于表单验证，后者用于将值注入到指令scope
> - `modeltext` 用于直接回填数据，回填速度最快，因为不需要根据 `model` 来查找对应的显示值，当树型组件按层级懒加载的时候，没办法拿到树的所有数据，因此有可能查找不到 `model` 对应的显示值，此时必须靠 `modeltext` 进行回填数据
> - 选中多个值时，显示结果会以 `config.symbol` 作为拼接，但提交的值将固定用`;`作为分割，两侧没有多余空格的，这是为了保证提交值格式的一致性
> - `config.popwin` 可以配置 `弹窗` 中定义的任何属性
> - `config.component.format` 配置函数时，记得返回一个格式化后的值。当没返回值或返回空字符串时，默认设置显示值为 `entity.textfield`
> - 是否过度封装？ 为何 `数据选择` 要独立封装成指令，而不能如 `单选框(radio)` 、 `多选框(checkbox)`等兄弟表单元素一样使用原始表单元素设计？
>   - 需求明确要求组件具有自动获取数据能力，如果用原始表单元素设计实现不了
>   - 树型数据的回填要用到递归函数处理，如果不封装指令，这部分实现将不得不移到业务代码中实现，耗时且容易新增bug
>   - 用了指令封装优点是简化初始化数据和回填数据工作，简化业务逻辑，便于实现代码生成器，缺点是修改组件样式、html结构变得不灵活，处理业务数据会相对不灵活
> - 支持原生元素上应用`ngdatpicker`

## 表格

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `ng-model` | model值 | 是，多选时约定为 `;` 作为分隔符 | {String} |
| `model` | model值 | 是，多选时约定为 `;` 作为分隔符 | {String} |
| `modeltext` | 显示值，用于回填数据 | 否 | {String} |
| `extend-model` | 扩展对象，单选为{Object} 记录对象，多选为{Array-Object} 数组记录对象 | 否 | {String} |
| `name` | 表单元素名称 | 否（但需要辨别哪个表单元素值不合法时，必须加上) | {String} |
| `ng-required` | 是否必填 | 否 | {String} |
| `disabled` | 是否不可用 | 是否不可用 | {String} 默认 "false" |
| `config` | 数据选择器配置 | 是 | {String} |
| `config.component.type` | 弹窗内嵌组件类型 | 否 | {String} 默认 "tree"，需要指定 "grid" |
| `config.component.valuefield` | 树型节点提交值对应的字段名 | 否 | {String} 默认 `config.component.textfield`属性值 |
| `config.component.textfield` | 树型节点显示值对应的字段名 | 否 | {String} 默认 "text" |
| `config.component.serverurl` | api服务器域名 | 否 | {String} 默认 serverURL全局变量 |
| `config.component.datasource` | api | 是 | {String} eg:"/application/tree" |
| `config.component.condition` | 请求数据条件 | 是 | {Function}，接收两个参数，`gridconf` 表格配置，`searchkey` 查询字符串 |
| `config.component.method` | http请求方法 | 否 | {String} 默认 "post" |
| `config.component.format` | 格式化显示值的函数 | 否 | {Function} 默认 空函数，接收一个参数，实体 |
| `config.component.conf` | angular-ui-grid配置 | 是 | {Object} |
| `config.component.conf.columnDefs` | 列配置 | 是 | {Array} |
| `config.popwin.width` | 弹窗宽度 | 否 | {Number\|String} eg: 600/"600"/"90%"，数字时单位为"px" 默认 "80%" |
| `config.popwin.height` | 弹窗高度 | 否 | {Number} 单位为"px" 默认 650 |
| `config.popwin.title` | 弹窗标题 | 否 | {String} 默认 "数据选择" |
| `config.readonly` | 是否只读 | 否 | {Boolean} 默认 true |
| `config.nodata` | 数据源为空时的提示信息 | 否 | {String} 默认 "暂无数据可选" |
| `config.multiselect` | 是否多选 | 否 | {Boolean} 默认 false |
| `config.symbol` | 显示值分割符 | 否 | {String} 默认 " ; " |

**html**
``` html
<form name="form" novalidate class="jtform jtform_2">
    <table>
        <tbody>
            <tr>
                <td class="required">单选</td>
                <td>
                    <ngdatpicker 
                        ng-model="editData.singleGrid" 
                        model="editData.singleGrid" 
                        extend-model="extendData.singleGrid" 
                        config="singleGridConf" 
                        name="singleGrid" 
                        ng-required="true">
                    </ngdatpicker>
                </td>
                <td class="required">多选</td>
                <td>
                    <ngdatpicker 
                        ng-model="editData.multisGrid" 
                        model="editData.multisGrid" 
                        extend-model="extendData.multisGrid" 
                        config="multisGridConf" 
                        name="multisGrid" 
                        ng-required="true">
                    </ngdatpicker>
                </td>
            </tr>                                
        </tbody>
    </table>
</form>
```

**js**
``` js
// 单选
scope.singleGridConf = {
    component: {
        type: "grid"
        ,valuefield: "jobnum"
        ,textfield: "name"
        ,serverurl: serverURL
        ,datasource: "/user/list"
        ,condition: function(gridconf, searchkey){
            return {
                // 第几页
                page: gridconf.paginationCurrentPage
                // 页数目
                ,_query_pagesize: gridconf.paginationPageSize
                // 查询条件
                ,_query__jobnum__CONTAINS__a: searchkey
                ,_query__name__CONTAINS__a: searchkey
                ,_query__cellphone__CONTAINS__a: searchkey
            }
        }
        ,method: "post"
        ,conf: {
            columnDefs: [
                {
                    field: "jobnum"
                    ,name: "工号"
                    ,width: "20%"
                },
                {
                    field: "name"
                    ,name: "名称"
                    ,width: "30%"
                },
                {
                    field: "cellphone"
                    ,name: "手机号码"
                }
            ]
        }
    }
    , popwin: {
        width: "60%"
        ,height: 460
        ,title: "请选择设备(单选)"
    }
    , readonly: true
    , nodata: "暂无数据可选"
    , multiselect: false
}

// 多选
scope.multisGridConf = {
    component: {
        type: "grid"
        ,valuefield: "jobnum"
        ,textfield: "name"
        ,serverurl: serverURL
        ,datasource: "/user/list"
        ,condition: function(gridconf, searchkey){
            return {
                // 第几页
                page: gridconf.paginationCurrentPage
                // 页数目
                ,_query_pagesize: gridconf.paginationPageSize
                // 查询条件
                ,_query__jobnum__CONTAINS__a: searchkey
                ,_query__name__CONTAINS__a: searchkey
                ,_query__cellphone__CONTAINS__a: searchkey
            }
        }
        ,method: "post"
        ,conf: {
            columnDefs: [
                {
                    field: "jobnum"
                    ,name: "工号"
                    ,width: "20%"
                },
                {
                    field: "name"
                    ,name: "名称"
                    ,width: "30%"
                },
                {
                    field: "cellphone"
                    ,name: "手机号码"
                }
            ]
        }
    }
    , popwin: {
        width: "60%"
        ,height: 460
        ,title: "请选择设备(多选)"
    }
    , readonly: true
    , nodata: "暂无数据可选"
    , multiselect: true
    , symbol: " ; "
}
```

> 注：
> - `config.component.conf` 为angular-ui-grid的 `ui-grid` 属性赋值的，配置方法跟表格配置完全一样，默认配置如下
> ``` js
> {
>    columnDefs: [],
>    data: undefined,
>    enableSelection: false,
>    enableRowHeaderSelection: true,
>    enableFullRowSelection: true,
>    multiSelect: true,
>    enableSorting: true,
>    useExternalSorting: true,
>    paginationPageSizes: [20, 30, 50],
>    paginationCurrentPage: 1,
>    paginationPageSize: 20,
>    totalItems: 0,
>    useExternalPagination: true
>}
> ```
> - `config.component.conf.columnDefs` 设置表格的列，注意留一个列是不配置宽度的，如下所示，目的是避免表格出现水平滚动条，由于表格设置了勾选列，当所有字段的宽度总和为100%时，内部宽度比父层容器宽度多出勾选列宽度，故出现水平滚动条
> ``` js
> columnDefs: [
>    {
>        field: "jobnum"
>        ,name: "工号"
>        ,width: "20%"
>    },
>    {
>        field: "name"
>        ,name: "名称"
>        ,width: "30%"
>    },
>    {
>        field: "cellphone"
>        ,name: "手机号码"
>    }
>]
> ```
> - `ng-model`值为`null`或`undefined`或`''`时，组件**取消**尝试获取回填值

## 标签

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `ng-model` | model值 | 是，多选时约定为 `;` 作为分隔符 | {String} |
| `model` | model值 | 是，多选时约定为 `;` 作为分隔符 | {String} |
| `modeltext` | 显示值，用于回填数据 | 否 | {String} |
| `extend-model` | 扩展对象，单选为{Object} 记录对象，多选为{Array-Object} 数组记录对象 | 否 | {String} |
| `name` | 表单元素名称 | 否（但需要辨别哪个表单元素值不合法时，必须加上) | {String} |
| `ng-required` | 是否必填 | 否 | {String} |
| `disabled` | 是否不可用 | 是否不可用 | {String} 默认 "false" |
| `config` | 数据选择器配置 | 是 | {String} |
| `config.component.type` | 弹窗内嵌组件类型 | 否 | {String} 默认 "tree"，需要指定 "tag" |
| `config.component.valuefield` | 树型节点提交值对应的字段名 | 否 | {String} 默认 `config.component.textfield`属性值 |
| `config.component.textfield` | 树型节点显示值对应的字段名 | 否 | {String} 默认 "text" |
| `config.component.serverurl` | api服务器域名 | 否 | {String} 默认 serverURL全局变量 |
| `config.component.datasource` | api | 是 | {String} eg:"/application/tree" |
| `config.component.condition` | 请求数据条件 | 是 | {Function}，接收两个参数，`gridconf` 表格配置，`searchkey` 查询字符串 |
| `config.component.method` | http请求方法 | 否 | {String} 默认 "post" |
| `config.component.format` | 格式化显示值的函数 | 否 | {Function} 默认 空函数，接收一个参数，实体 |
| `config.popwin.width` | 弹窗宽度 | 否 | {Number\|String} eg: 600/"600"/"90%"，数字时单位为"px" 默认 "80%" |
| `config.popwin.height` | 弹窗高度 | 否 | {Number} 单位为"px" 默认 650 |
| `config.popwin.title` | 弹窗标题 | 否 | {String} 默认 "数据选择" |
| `config.readonly` | 是否只读 | 否 | {Boolean} 默认 true |
| `config.nodata` | 数据源为空时的提示信息 | 否 | {String} 默认 "暂无数据可选" |
| `config.multiselect` | 是否多选 | 否 | {Boolean} 默认 false |
| `config.symbol` | 显示值分割符 | 否 | {String} 默认 " ; " |

**html**
``` html
<form name="form" novalidate class="jtform jtform_2">
    <table>
        <tbody>
            <tr>
                <td class="required">单选</td>
                <td>
                    <ngdatpicker 
                        ng-model="editData.singleTag" 
                        modeltext="editData.singleTag_text" 
                        model="editData.singleTag" 
                        extend-model="extendData.singleTag" 
                        config="singleTagConf" 
                        name="singleTag" 
                        ng-required="true">
                    </ngdatpicker>
                </td>
                <td class="required">多选</td>
                <td>
                    <ngdatpicker 
                        ng-model="editData.multisTag" 
                        modeltext="editData.multisTag_text" 
                        model="editData.multisTag" 
                        extend-model="extendData.multisTag" 
                        config="multisTagConf" 
                        name="multisTag" 
                        ng-required="true">
                    </ngdatpicker>
                </td>
            </tr>                                
        </tbody>
    </table>
</form>
```

**js**
``` js
// 标签单选配置
scope.singleTagConf = {
    component: {
        type: "tag"
        ,valuefield: "id"
        ,textfield: "text"
        ,serverurl: serverURL
        ,datasource: "/application/tree"
        ,condition: {
                _query__type__IN__t: ''
        }
        ,method: "post"
        ,format: function(result){
            return result.text + "(" + result.id.substr(0, 8) + ")";
        }
        ,conf: {
            onSelect: function(result){
                if (result != null) {
                    console.log(result);
                }
            }
        }
    }
    , popwin: {
        width: "60%"
        ,height: 400
        ,title: "请选择设备(单选)" 
    }
    , readonly: true
    , nodata: "暂无数据可选"
    , multiselect: false
    , ok: function(extendModel){
        console.log(extendModel);
    }
};

// 标签多选配置
scope.multisTagConf = {
    component: {
        type: "tag"
        ,valuefield: "id"
        ,textfield: "text"
        ,serverurl: serverURL
        ,datasource: "/application/tree"
        ,condition: {
                _query__type__IN__t: ''
        }
        ,method: "post"
        ,format: function(result){
            return result.text + "(" + result.id.substr(0, 8) + ")";
        }
    }
    , popwin: {
        width: "60%"
        ,height: 400
        ,title: "请选择设备(单选)" 
    }
    , readonly: true
    , nodata: "暂无数据可选"
    , multiselect: true
    , ok: function(extendModel){
        console.log(extendModel);
    }
};
```

## 高级查询

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:|
| `model` | model值 | 高级查询条件 | {Object} |
| `config` | 数据选择器配置 | 是 | {String} |
| `config.component.type` | 弹窗内嵌组件类型 | 否 | {String} 默认 "tree"，需要指定 "adsearch" |
| `config.component.data` | 字段集合 | 否 | {Array} 默认 [] |
| `config.popwin.width` | 弹窗宽度 | 否 | {Number\|String} eg: 600/"600"/"90%"，数字时单位为"px" 默认 "80%" |
| `config.popwin.height` | 弹窗高度 | 否 | {Number} 单位为"px" 默认 650 |
| `config.popwin.title` | 弹窗标题 | 否 | {String} 默认 "高级查询" |
| `config.nodata` | 没有字段集的提示信息 | 否 | {String} 默认 "暂无字段供查询" |

**html**
``` html
<ngdatpicker model="editData.adsearchCondition" config="adsearchConf"></ngdatpicker>
```

**js**
``` js
// 字段集合
var fields = [
    {
        field: "name"
        ,text: "姓名"
        ,type: "String"
    }
    ,{
        field: "age"
        ,text: "年龄"
        ,type: "Number"
    }
    ,{
        field: "sex"
        ,text: "性别"
        ,type: "Number" 
        ,dataParam: `[{Key:'0',Value:'男'},{Key:'1',Value:'女'}]`
    }
    ,{
        field: "born"
        ,text: "出生日期"
        ,type: "Date"
    }
    ,{
        field: "interest"
        ,text: "爱好"
        ,type: "Enum"
        ,dataParam: `[{Key:'0',Value:'分子公司'},{Key:'2',Value:'我的客户'},{Key:'3',Value:'供应厂商'},{Key:'4',Value:'合作伙伴'}]`
    }
];

// 高级查询配置
scope.adsearchConf = {
    component: {
        type: "adsearch"
        ,data: fields
    }
    , popwin: {
        width: "80%"
        ,height: 600
    }
    /**
     * 确认回调
     * @param {Object} condition 高级查询条件
     */
    , ok: function(condition){
        console.log(condition);
    }
};
```

## 容器

**属性**

| 属性 | 描述 | 是否必须 | 值|
|---- |:-------------:|:----:| ----:| 
| `data` | 容器tab配置 | 是 | {Array} |
| `data[0].title` | 容器标题 | 是 | {String} 默认 "" |
| `data[0].url` | 容器模板路径 | 是 | {String} 默认 "" |
| `data[0].ctrls` | 容器控制器 | 否 | {Array-String} 无默认值，可选值 `more`（更多）、 `delete`（删除）、 `edit`（编辑）、 `create`（新增）|
| `data[0].canext` | 能否切换到下一个tab | 否 | {Function} 默认返回 true |
| `nohd` | 是否隐藏头部 | 否 | {Boolean} 默认 `false` |
| `noborder` | 是否隐藏边框 | 否 | {Boolean} 默认 `false` |

**事件**

| 事件 | 描述 |
|---- |:-------------:|
| `$container_more` | 更多按钮点击事件 |
| `$container_delete` | 删除按钮点击事件 |
| `$container_edit` | 编辑按钮点击事件 |
| `$container_create` | 新增按钮点击事件 |
| `$container_tab_change` | tab点击事件 |

**html**
``` html
<!--单个tab-->
<ngcontainer data="container_single"></ngcontainer>
<!--多个tab-->
<ngcontainer data="container_multis" nohd="container_multis.nohd" noborder="container_multis.noborder"></ngcontainer>
```

**js**
``` js
var newfaultlist = {
    title: "最新故障"
    ,url: "module/sbwx/page/dashboard/newfaultlist.html"
    ,ctrls: ["more"]
};
var dataanalyze = {
    title: "本月进步排名"
    ,url: "module/sbwx/page/dashboard/dataanalyze.html"
    ,ctrls: ["more"]
};
var faulttrend = {
    title: "故障趋势"
    ,url: "module/sbwx/page/dashboard/faulttrend.html"
};
scope.container_single = [newfaultlist];
scope.container_multis = [faulttrend, dataanalyze];
// scope.container_multis.nohd = scope.container_multis.noborder = true;  
```

事件注册

``` js
// 更多按钮注册
scope.$on('$container_more', function(){});
// 删除按钮注册
scope.$on('$container_delete', function(){});
// 编辑按钮注册
scope.$on('$container_edit', function(){});
// 新增按钮注册
scope.$on('$container_create', function(){});
```

## 上传

**属性**

| 属性 | 描述 | 是否必须 | 值类型 | 默认值 |
|---- |:-------------:|:----:|:----:| ----:| 
| `type` | 上传附件类型 | 否 | {String} 可选值："files"、"pic" | "files" |
| `config` | 上传组件配置 | 否 | {Object} | N/A |
| `config.hasTip` | 是否有绿色尺寸提示 | 否 | {Boolean} | true |
| `config.multiple` | 是否多图上传 | 否 | {String} | false |
| `config.max_limit` | 多图上传最**大**图片数目限制 | 否 | {Number} | 10 |
| `config.min_limit` | 多图上传最**小**图片数目限制 | 否 | {Number} | 0 |
| `config.standardH` | 预览框默认高度 | 否 | {Number} | 60 |
| `config.size` | 上传附件最大尺寸 | 否 | {Number} 单位：字节 | 3984588 (3.8M) |
| `config.accept` | 上传附件类型 | 否 | {String} | `type`为'pic'时，"'image/*'"，`type`为'files'时，"" |
| `config.preview` | 预览图尺寸 | 否 | {Object} | N/A |
| `config.preview.width` | 预览图宽度 | 否 | {Number} 单位：像素 | 160 |
| `config.preview.height` | 预览图高度 | 否 | {Number} 单位：像素 | 160 |
| `imgs` | 图片数据源 | 否 | {Array-Object} | N/A |
| `disabled` | 是否可用 | 否 | {Boolean} | false |
| `ng-model` | 提交值 | 是 | {String} | N/A |
| `extend-model` | 回填数据 | 否 | {Array-Object} | N/A |
| `ng-required` | 是否必填 | 否 | {String} | N/A |

**html**
``` html
<ngupload type="pic"
          config="picuploadConf"
          ng-model="editData.uploadImgs"
          extend-model="uploadImgs">
</ngupload>
```

**js**
``` js
// 回填数据
scope.uploadImgs = [{
    filId:"d378dd4e35be4fbba3cdad4b8336c115"
    ,name:"IMG_4686"
    ...
}];
// 提交值
scope.editData = {
    uploadImgs: null
};
scope.picuploadConf = {
    url: serverURL + '/attach/upload'
    ,multiple: true
    ,onClick:function($ev,index,img,imgs){
        $Sys.previewImg(true, imgs, index);
    }
};
```

> 注：
> - 回填数据**必须**保证有`filId`（文件ID）、`name`（文件名称）两个字段
> - 预览图片的最大高度为浏览器视窗高度减去`100px`，最大宽度为浏览器视窗宽度减去`4.62em`
> - 当上传图片宽度与高度比大的时候，如宽度是高度的好几倍，可能会因受限于其样式的最大显示尺寸，因而出现图片拉伸情况，但此种情况极少出现，因图片都是用户用手机拍的

## 表格

设置行字体颜色

```js
// 设置超时记录的字体颜色
scope.tableConf = {
    data: [{overtime: "0"},{overtime: "1"}]
    enableSelection: false,
    enableRowHeaderSelection: true,
    enableFullRowSelection: true,
    multiSelect: true,
    enableSorting: true,
    useExternalSorting: true,
    paginationPageSizes: [20, 30, 50],
    paginationCurrentPage: 1,
    paginationPageSize: 20,
    totalItems: 0,
    useExternalPagination: true,
    columnDefs: [
        {
            field: "overtime"
            ,name: "是否超时"
            ,cellTemplate: '<div class="ui-grid-cell-contents">{{row.entity.overtime > 0 ? "是" : "否"}}</div>'
        }
    ]
    ,rowTemplate: '<div ng-class="{c_overtime: row.entity.overtime>0}" class="ui-grid-cell" ng-repeat="col in colContainer.renderedColumns" ui-grid-cell></div>'
}
```

自定义列显示

```js
scope.tableConf = {
    columnDefs: [
        {
            field: "overtime"
            ,name: "是否超时"
            ,width: "20%"
            ,cellTemplate: '<div class="ui-grid-cell-contents">{{row.entity.overtime > 0 ? "是" : "否"}}</div>'
        }
    ]
}
```

隐藏分页控件

```js
scope.tableConf = {
    enablePaginationControls: false
}
```

设置表格自适应

```html
<div ui-grid="gridOptions" ui-grid-auto-resize></div>
```

## 输入框

自动获取焦点

```html
<input type="text" autofocus />
```
