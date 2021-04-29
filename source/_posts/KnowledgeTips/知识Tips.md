---
title: 知识Tips
date: 2021-04-19 22:04:03
comments: true
description: 一些小知识
top_img: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面顶部图片
cover: https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-a@main/img/704736.jpg # 页面缩略图
toc_number: true
mathjax: true
aside: true
large: true
bilibili_banner: winter # autumn 、winter
---


## 为何众多计算机语言要从1970年1月1日开始算起？

最初计算机操作系统是32位，而时间也是用32位表示。

```java
System.out.println(Integer.MAX_VALUE);
// OutPut: 2147483647
```

Integer 在JAVA内用32位表示，因此32位能表示的值是2147483647。

另外1年365天的总秒数是 31536000s，2147483647/31536000 = 68.1，也就是说32位能表示的最长时间是68年，从1970年开始的话，加上68.1，实际最终到2038年01月19日03时14分07秒，便会到达时间，过了这个时间点，所有32位操作系统时间便会变为10000000 00000000 00000000 00000000，算下来也就是1901年12月13日20时45分52秒，这样便会出现时间回归的现象，很多软件便会运行异常了。

到这里，问题的答案已经显现出来了，那就是：因为用32位来表示时间的间隔是68年，而最早出现的UNIX操作系统考虑到计算机产生的年代和应用的时限综合取了1970年1月1日作为UNIX TIME的纪元时间(开始时间)。

至于时间回归的现象相信随着64为操作系统的产生逐渐得到解决，因为用64位操作系统可以表示到 292,277,026,596年12月4日15时30分08秒，相信我们的N代子孙，哪怕地球毁灭那天都不用愁不够用了，因为这个时间已经是千亿年以后了。

> 来源：[为何众多计算机语言要从1970年1月1日开始算起？](https://developer.51cto.com/art/201508/488060.htm)

