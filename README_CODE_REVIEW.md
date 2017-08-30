# 常见脚本异常收集

## Uncaught TypeError

**例子**

```js
// $scope.fltOptions 为异步请求返回结果，当结果未返回时执行下面代码出错
$scope.$watch('editData.fltCtgId', function(newValue, oldValue) {
    if (newValue == oldValue) { return; }
    else{
        $scope.flt = $scope.fltOptions.filter(function (p) {
            if(newValue == p.ctgId){
                return p;
            }
        })[0];
        $scope.editData.name = $scope.flt.name;
    }
});
```

## Uncaught ReferenceError

**例子**

```js
// 严格模式下，禁止不声明变量来提升全局变量
'use strict';
global = 1;
```

最佳实践

```js
// 在实现自动化发布之前，源码就要使用严格模式
'use strict';
JT_CONTEXT.component('ngpopicker', {});
```

# 打包异常

## 意外修改组件名称

**例子**

```js
JT_CONTEXT.component('popicker', {});
JT_CONTEXT.component('confirmwin', {});
JT_CONTEXT.component('popwin', {});
```

压缩后

```js
JT_CONTEXT.component('popicker_31c0d5b6.min', {});
JT_CONTEXT.component('confirmwin_90c0a5a9.min', {});
JT_CONTEXT.component('popwin_31c0d5b6.min', {});
```

最佳实践

```js
// 指令或组件名称前添加前缀"ng"，如ngpopicker
JT_CONTEXT.component('ngpopicker', {});
JT_CONTEXT.component('ngconfirmwin', {});
JT_CONTEXT.component('ngpopwin', {});
```

## 在严格模式下使用了保留字

**例子**

```js
var static = {
    mhtml: {
        top: "Mime-Version: 1.0\nContent-Base: " + location.href + "\nContent-Type: Multipart/related; boundary=\"NEXT.ITEM-BOUNDARY\";type=\"text/html\"\n\n--NEXT.ITEM-BOUNDARY\nContent-Type: text/html; charset=\"utf-8\"\nContent-Location: " + location.href + "\n\n<!DOCTYPE html>\n<html>\n_html_</html>",
        head: "<head>\n<meta http-equiv=\"Content-Type\" content=\"text/html; charset=utf-8\">\n<style>\n_styles_\n</style>\n</head>\n",
        body: "<body>_body_</body>"
    }
}
```

> 注：`implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`, `yield`都是保留字，注意规避



