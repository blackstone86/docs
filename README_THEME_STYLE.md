主题样式开发 v0.0.1
-----------------

## 一、主题样式开发

1. 项目需要`nodeJS` 4.2.2 或以上的版本

1. 安装开发工具
    ```shell
    npm install
    ```

1. 运行打包命令

    ```shell
    // 生产模式
    npm run prod

    // 开发模式
    npm run dev
    ```

> 注：
> - 生产模式
>   - **仅**生成主题样式`skin.css`
>
> - 开发模式
>   - 生成主题样式`skin.css`
>   - 监听`styles/skin`下所有less文件，发现变更会自动生成主题样式
>   - 浏览例子 http://localhost:5000/demos.html
>   如连续执行`npm run dev`会报错，是因为监听5000端口的服务没停掉，所以先关闭5000端口的进程
>   ```shell
>   // 列出5000端口的进程
>   lsof -i tcp:5000
>   // 如发现进程，kill掉它，PID类似`15271`这样数字
>   kill PID
>   ```

## 二、主题样式目录结构
```
├── miniui-common
│   ├── fonts
│   ├── icon.css
│   └── less
│       ├── common.less
│       ├── hx_icon.less
│       ├── icon.less
│       └── normalize.less
└── miniui-green
    ├── iframe.css
    ├── bpm.css
    ├── images
    ├── less
    │   ├── iframe.less
    │   ├── mod
    │   │   ├── file-list.less
    │   │   ├── filemanager.less
    │   │   ├── form.less
    │   │   ├── list.less
    │   │   ├── manage.less
    │   │   └── portal.less
    │   ├── skin.less
    |   ├── bpm.less
    │   └── var.less    
    └── skin.css
```

- `miniui-common目录` 主题公用样式目录
- `fonts目录` 主题公用的字体，包含了自定义、框架两套字体
- `less目录` 主题公用样式目录
- `common.less` 主题公用样式，包含了图标字体、基准样式
- `hx_icon.less` 框架提供的图标字体
- `icon.less` 自定义的图标字体，基于`iconmoon`
- `normalize.less` 基准样式
- `icon.css` 图标样式【生成文件】
- `miniui-green目录` 绿色主题（为项目定制）
- `images目录` 图片目录，拷贝自`miniui`的`jqueryui-cupertino`主题
- `less目录` 主题样式
- `mod目录` 模块样式
- `file-list.less` 上传区域样式
- `filemanager.less` 菜单样式
- `form.less` 表单样式
- `list.less` 列表样式
- `manage.less` 菜单样式
- `portal.less` 门户样式
- `bpm.less` 流程样式（原来的commons.css)
- `skin.less` 皮肤样式，包含菜单等模块样式
- `iframe.css` 编辑器样式【生成文件】
- `skin.css` 主题样式【生成文件】
- `bpm.css` 流程样式【生成文件】

## 三、不需要重复引入的样式

``` html
    <link href="${ctxPath}/styles/css/icons.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/icons.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/file-list.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/filemanager.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/form.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/list.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/manage.css" rel="stylesheet" type="text/css" />
    <link href="${ctxPath}/styles/portal.css" rel="stylesheet" type="text/css" />
```

## 四、整改内容

- 移除文件或目录
    - `webapp/styles/skin/black目录` 
    - `webapp/styles/skin/orange目录` 
    - `webapp/styles/skin/green目录` 
    - `webapp/styles/skin/default目录`
    - `webapp/styles/css/icons.css`

        > 已集成在`skin.css`

    - `webapp/styles/filetype目录`

        > 迁移至`miniui-green/images/filetype`

    - `webapp/icons目录`

        > 迁移至`miniui-common/fonts`

    - `webapp/styles/icons.css`
        
        > 迁移至`miniui-common/hx_icon.less`

    - `webapp/styles/icons.html`
        
        > 迁移至`webapp/demos/icons.html`

    - `webapp/styles/file-list.css`

        > 迁移至`miniui-green/less/mod/file-list.less`

    - `webapp/styles/filemanager.css`
        
        > 迁移至 `miniui-green/less/mod/filemanager.less`

    - `webapp/styles/form.css`

        > 迁移至 `miniui-green/less/mod/form.less`

    - `webapp/styles/iframe.css`

        > 迁移至 `miniui-green/less/iframe.less`，不集成到`skin.css`，是`ueditor/themes/iframe.css`需单独使用`iframe.css`

    - `webapp/styles/list.css`

        > 迁移至 `miniui-green/less/mod/list.less`

    - `webapp/styles/portal.css`

        > 迁移至 `miniui-green/less/mod/portal.less`
    
    - `webapp/styles/manage.css`

        > 迁移至 `miniui-green/less/mod/manage.less`

    - `webapp/styles/commons.css`
        
        > 迁移至`miniui-green/less/bpm.less`
    
    - `webapp/styles/icons`

        > 迁移至`miniui-green/images/icons`

- 基础样式调整
    - 重置样式替换为`normalize.css`，已集成到`skin.css`，不需要**重复**以下样式
    ```css
    /*basecss重置样式*/
    body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,form,p{margin:0;padding:0}
    table{border-collapse:collapse;border-spacing:0}
    fieldset,img,object{border:0 none;}
    img{-ms-interpolation-mode: bicubic;}
    ol,ul{list-style:none}
    h1,h2,h3,h4,h5,h6{font-size:100%;}
    input,textarea,select,button{font-family:inherit;font-size:inherit;font-weight:inherit;color: #333;
    *font-size:100%;vertical-align: middle;}
    em{ font-style: normal;}
    body{  width: 100%; height:100%; overflow: hidden; font-size: 12px;}
    ```

    `basecss`的功能样式集成到`skin.css`，不需要**重复**以下样式

    ```css
    /*basecss功能样式*/
    .fl{float:left;}
    .fr{float:right;}
    .tc{text-align:center;}
    .vm{vertical-align: middle;}
    .rel{position:relative;}
    .abs{position:absolute;}
    .none{display:none;}
    .center{margin: auto;}
    ```

- `sys/core/controller/FileController.java`修改图标样式路径

    ```java
    // 框架自带图标 + 自定义图标
    String icon=FileUtil.readFile(WebAppUtil.getAppAbsolutePath()+"/styles/skin/miniui-common/icon.css");
    ```

- `ueditor/themes/iframe.css` 修改`iframe.css`路径

    ```css
    @IMPORT url("../../../../styles/skin/miniui-green/iframe.css");
    ```

## 五、md5文档如何预览

1. 代码编辑器上安装插件（推荐）
   - vscode
     
     安装`Preview`插件，打开md5文档，按下快捷键 `Ctrl` + `Shift` + `v` 即可预览

   - webstorm

     参考[这里](http://blog.fens.me/webstorm-markdown/)

1. 浏览器上安装插件
   - chrome

     安装StackEdit插件应用，打开后将md5文档黏贴过去即可预览







