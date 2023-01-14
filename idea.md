# IntelliJ IDEA

## 项目结构

### 与eclipse的一些区别

IntelliJ系中的 Project 相当于[Eclipse](https://so.csdn.net/so/search?q=Eclipse&spm=1001.2101.3001.7020)系中的 Workspace ；

IntelliJ系中的 [Module](https://so.csdn.net/so/search?q=Module&spm=1001.2101.3001.7020) 相当于Eclipse系中的 Project ；

IntelliJ中一个 Project 可以包括多个 Module ；

### 项目创建

创建一个project，idea在创建project的时候会默认创建一个module

### 同级模块的引用

1. 选中任意一个module，按F4进入project structure；

2. 选择左侧的modules菜单；

3. 选择中间的hello_client，选中右侧的dependencies选项卡，点击右侧的绿色+按钮，选择module dependecy，在弹出的选择框中选择hello_service。

4. 这样就为hello_client添加了hello_service的引用。直接在hello_client中引用hello_service的代码即可。




## 快捷键

### 注释

```
1、一次性添加多行注释的快捷键
首先选中要注释区域，然后
ctrl+/        这个是多行代码分行注释，每行一个注释符号
ctrl+shift+/    这个是多行代码注释在一个块里，只在开头和结尾有注释符号

2、取消多行注释快捷键
怎样添加快捷键的，用相同方法取消，
如 ctrl+/  添加注释，则ctrl+/取消注释
ctrl+shift+/添加注释，则ctrl+shift+/取消注释

方法或类注释
在一个方法或类的开头，输入/**，然后按回车,自动根据参数和返回值生成注释模板，我们在这个模板上面编写即可。
```

### 编写操作

```
ctrl+r， 选中替换
ctrl+d， 复制到下一行
按住alt鼠标拖动：垂直线上多选
ctrl+alt+v: 自动生成左边的局部变量， 其实alt+enter在回车也可以
ctrl+shift+上下 alt+shift+上下:控制代码行上下
```

### 关于类的操作

```
查看类中的所有方法: alt+7
```



## 常见问题

### idea导入项目后中文显示乱码之解决方案

1.在idea中找到此路径：File->settings->Editor->File Encodings

2.在左边找到Editor---->File Encodings,然后看右边

3.将上面的 Global Encoding 和 Project Encoding 都设置成 GBK

4.将下面的 Default encoding for properties files 也设置成 GBK 

然后点下面的 “OK” 按钮即可



不过这边我也会选择utf-8





