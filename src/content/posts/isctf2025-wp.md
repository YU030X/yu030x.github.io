---
title: ISCTF2025 wp
description: ISCTF2025比赛题解
pubDate: 2025-12-01
author: 'YU030X'
tags: ['ctf', 'writeup']
draft: true
---

## WEB

### b@by n0t1ce b0ard

![image-20251201232147878](C:/Users/yuu/AppData/Roaming/Typora/typora-user-images/image-20251201232147878.png)

文章提示很明显：CVE-2024-12233

官方：https://github.com/LamentXU123/cve/blob/main/RCE1.md

![image-20251201232241485](C:/Users/yuu/AppData/Roaming/Typora/typora-user-images/image-20251201232241485.png)

根据poc解题

1. 注册账号
2. 上传一句话
3. 访问/images/xx@xx.com/xx.php?cmd=xxx;

![image-20251201232322551](C:/Users/yuu/AppData/Roaming/Typora/typora-user-images/image-20251201232322551.png)

### 难过的bottle

源码：

![image-20251201235713293](C:/Users/yuu/AppData/Roaming/Typora/typora-user-images/image-20251201235713293.png)

查看源码发现，只剩a,f,g,l以及(),{}能用

这里可以通过全角字符绕过

`{{ ｏｐｅｎ('/flag').ｒｅａｄ() }}`

![image-20251201235959745](C:/Users/yuu/AppData/Roaming/Typora/typora-user-images/image-20251201235959745.png)

![image-20251202000309530](C:/Users/yuu/AppData/Roaming/Typora/typora-user-images/image-20251202000309530.png)

压缩成zip，上传即可得到flag

### 题3



## MISC

### Guess！

用二分法，每次要猜的数不一样
