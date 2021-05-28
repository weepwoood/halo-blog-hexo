---
title: Java IO
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-b@master/img/Java.png
tags:
  - Java
categories:
  - Java
date: 2021-5-23 16:31:54
---

参考资料：《Java 技术手册》

## File 类

File 类是对文件系统中文件以及文件夹进行操作的类，可以通过面向对象的思想操作文件和文件夹。是以前 Java 处理文件 I/O 的基础。这个抽象既能表示文件，也能表示目录，不过有时使用起来有些麻烦，写出的代码如下所示：

```java
// 创建一个文件对象，表示用户的家目录
File homedir = new File(System.getProperty("user.home"));

// 创建一个对象，表示配置文件
// （家目录中应该存在这个文件）
File f = new File(homedir, "app.conf");

// 检查文件是否存在，是否真是文件，以及是否可读
if (f.exists() && f.isFile() && f.canRead()) {
    // 创建一个文件对象，表示新配置目录
    File configdir = new File(f, ".configdir");
    // 然后创建这个目录
    configdir.mkdir();
    // 最后，把配置文件移到新位置
    f.renameTo(new File(configdir, ".config"));
}
```

上述代码展现了 File 类使用灵活的一面，但也演示了这种抽象带来的一些问题。一般情况下，需要调用很多方法查询 File 对象才能判断这个对象到底表示的是什么，以及具有什么能力。

File 类中有相当多的方法，但根本没有直接提供一些基本功能（尤其是无法读取文件的内容），下述代码简要总结了 File 类中的方法：

```java
// 权限管理
boolean canX = f.canExecute();
boolean canR = f.canRead();
boolean canW = f.canWrite();

boolean ok;
ok = f.setReadOnly();
ok = f.setExecutable(true);
ok = f.setReadable(true);
ok = f.setWritable(false);

// 使用不同的方式表示文件名
File absF = f.getAbsoluteFile();
File canF = f.getCanonicalFile();
String absName = f.getAbsolutePath();
String canName = f.getCanonicalPath();
String name = f.getName();
String pName = getParent();
URI fileURI = f.toURI(); // 创建文件路径的URI形式

// 文件的元数据
boolean exists = f.exists();
boolean isAbs = f.isAbsolute();
boolean isDir = f.isDirectory();
boolean isFile = f.isFile();
boolean isHidden = f.isHidden();
long modTime = f.lastModified(); // 距Epoch时间的毫秒数
boolean updateOK = f.setLastModified(updateTime); // 毫秒
long fileLen = f.length();

// 文件管理操作
boolean renamed = f.renameTo(destFile);
boolean deleted = f.delete();
// 创建文件不会覆盖现有文件
boolean createdOK = f.createNewFile();
// 处理临时文件
File tmp = File.createTempFile("my-tmp", ".tmp");
tmp.deleteOnExit();
// 处理目录
boolean createdDir = dir.mkdir();
String[] fileNames = dir.list();
File[] files = dir.listFiles();
```