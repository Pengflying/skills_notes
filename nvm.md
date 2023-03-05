# nvm---node多版本管理工具

https://blog.csdn.net/lewky_liu/article/details/87959839

https://github.com/coreybutler/nvm-windows/releases

https://juejin.cn/post/7095318124433506341

## 清理node残留

```
在Windows下完全卸载已安装的node.js
从卸载程序卸载程序和功能，也可以直接右键node.js的安装包并选择卸载。
重新启动（或者重新启动任务管理器杀死所有与节点相关的进程）。
从下列的目录中找到相关的内容并删除掉：
C:\Program Files (x86)\nodejs
C:\Program Files\nodejs
C:\Users\{User}\AppData\Roaming\npm（或%appdata%\npm）
C:\Users\{User}\AppData\Roaming\npm-cache（或%appdata%\npm-cache）
检查%PATH%环境变量以确保没有引用Nodejs或npm存在。
重新启动电脑。
```

## 配置国内镜像

### 设置npm_mirror:

```
nvm npm_mirror https://npmmirror.com/mirrors/npm/
```

### 设置node_mirror:

```
nvm node_mirror https://npmmirror.com/mirrors/node/
```

## 查看已安装的nodejs版本

```
nvm list
```

## 查看可以安装的版本

```
nvm list available
```

## 安装指定版本

```
nvm install 8.11.2 64-bit
```

## 使用指定版本

```
nvm use 8.11.2
```

## 查看使用情况

```
C:\Users\Lewis>nvm list

  * 8.11.2 (Currently using 64-bit executable)

C:\Users\Lewis>node -v
v8.11.2
```

## 卸载指定版本

```
nvm uninstall 8.11.2
```

