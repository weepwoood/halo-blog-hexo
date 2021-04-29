---
title: Hexo博客搭建
date: 2019-06-17 20:28:50
tags: 
- GitHub
- Hexo
categories:
- Blog
mathjax: true
---

介绍Hexo博客的搭建以及部署到GitHub上

---

![Hexo博客搭建](https://puui.qpic.cn/fans_admin/0/3_1206436828_1572055658592/0)

## 框架安装

1. 官网安装[Node.js](<http://nodejs.cn/download/>)

2. 利用cnmp解决npm下载安装缓慢问题

   ```shell
   npm install -g cnpm --registry=https://registry.npm.taobao.org
   ```

   > 在安装之前需要安装Git

3. 安装Hexo框架`cnpm install -g hexo-cli`

## 框架配置

1. 切换到新建博客目录下
2. `hexo init`自动生成框架
3. `hexo s`启动检验
   > Hexo3将server独立需单独安装
   > 命令npm install hexo-server --save
   > 如果EADDRINUSE错误修改端口例如hexo s -p 8080

<!--more-->
## [框架目录](https://hexo.io/zh-cn/docs/setup)

```shell
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── theme
```

### _config.yml

修改配置信息

### package.json

应用程序的信息

### scaffolds

模版文件夹

### source

资源文件夹是存放用户资源的地方

> 除_posts文件夹之外，开头命名为_(下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public文件夹，而其他文件会被拷贝过去。

### themes

主题文件夹

## 博客使用

1. `hexo n "title"` 创建文章

   > 按照scaffolds文件夹模板创建
   > 此外可以创建草稿`hexo new draft "title"`
2. `hexo clean`
3. `hexo g`生成博客

## 远端部署

1. 安装GitHub插件`cnpm install -g hexo-deployer-git --save`
2. 在Github上创建项目库
   > 项目库命名要是   用户名+.github.io   例如：lanqilu.github.io
3. 设置 _config.yml 文件

   ```yml
   # Deployment
   ## Docs: https://hexo.io/docs/deployment.html
   deploy:
   type: git
   repo: https://github.com/<GitHub_name>/<GitHub_repositories>.git
   branch: master
   ```

4. 部署到远端`hexo d`
   填写github账号密码
5. 常用部署命令
   cmd`hexo clean && hexo g && hexo d`
   powershell`hexo clean '&' hexo g '&' hexo d`

## 更换主题

以[Next](https://github.com/theme-next/hexo-theme-next)为例

+ 克隆到themes目录下

  ```shell
  git clone  https://github.com/theme-next/hexo-theme-next.git
  ```

+ 配置`_config.yml`

   ```yml
   # Extensions
   ## Plugins: https://hexo.io/plugins/
   ## Themes: https://hexo.io/themes/
   theme: next-reloaded  # 主题名
   ```

## 问题

### 图片问题

配置`_config.yml`里面的`post_asset_folder:false`这个选项设置为`true`
默认在创建新文件时生成同名文件夹
> 使用方法`![](xxx.jpg)`

推荐使用图床来在Markdown文本中插入图片
