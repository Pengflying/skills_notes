# npm

https://blog.csdn.net/Stdio_QU/article/details/120361940

安装了对应的node之后

配置淘宝镜像

```
npm install -g cnpm --registry=http://registry.npm.taobao.org
```

设置淘宝镜像，输出镜像路径

```
npm config set registry https://registry.npm.taobao.org
npm config get registry
```

安装 @vue/cli 构建工具，全局安装 webpack

```
npm install -g @vue/cli
npm install --save-dev webpack
```

进入项目目录，初始化构建项目

```
vue init webpack 项目名称
```

安装项目所需依赖

```
cnpm install
```

启动项目

```
npm run serve
```

