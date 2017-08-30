# 系统图标字体制作

   - 生成icon font方法
        - png转化svg[地址](http://c.otar.im/)，转化后的svg放进去svg-icons文件夹(用于备案)
        - 导入原有icon[地址](https://icomoon.io/app/#/select)
            
            点击Import Icons，选择jt-set.json
        - 右上角菜单项，选择Import to Set，选择svg文件(png转化svg)
        - 点击Generate Font F
            - 获得png的code，到platform/css/icon.css上追加新增的icon图片样式
            - 最好在icons.html上也加上，方便后续查阅所有icon
            - 点击Download，解压
                - 将icomoon.svg, icomoon.ttf, icomoon.eot, icomoon.woff覆盖platform/fonts
                - 将selection.json 改名为 jt-set.json