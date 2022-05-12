---
title: npm 和 yarn 安装及配置
---

## npm

### npm 换源成淘宝镜像

由于 node 下载第三方依赖包是从国外服务器下载，虽然没有被墙，但是下载的速度是非常的缓慢且有可能会出现异常。所以为了提高效率，我们还是把 npm 的镜像源替换成淘宝的镜像源。有几种方式供我们选择

方法一：使用阿里定制的 cnpm 命令行工具代替默认的 npm，输入以下代码：

```bash
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```

检测是否安装成功

```bash
$ cnpm -v
```


安装成功之后，以后安装依赖包的方式和 npm 的是一样的，只是 npm 的命令换成是 cnpm 就可以了

假如你已经习惯了使用 npm 的安装方式的，不想去下载阿里的 cnpm 命令工具的话，很简单，我们直接将 node的仓库地址换成淘宝仓库地址即可

方法二：替换仓库地址（单次使用）

```bash
$  npm install --registry=https://registry.npm.taobao.org
```

方法三（推荐）：替换仓库地址（永久使用）

在开发 react-native 的时候，不要使用 cnpm！cnpm 安装的模块路径比较奇怪，packager 不能正常识别。所以，为了方便开发，我们最好是直接永久使用淘宝的镜像源

直接命令行的设置：

```bash
$ npm config set registry https://registry.npm.taobao.org
```

检测是否修改成功：

```
npm config get registry
// 或
npm info express
```

注：如果想还原 npm 仓库地址的话，只需要在把地址配置成 npm 镜像就可以了

```
npm config set registry https://registry.npmjs.org/
```

[参考链接](https://www.cnblogs.com/cythia/p/10985080.html)

## yarn





[参考链接](https://www.yarnpkg.cn/getting-started/install)