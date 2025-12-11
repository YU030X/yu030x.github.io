---
title: web任务
description: web任务
pubDate: 2025-09-29
author: 'YU030X'
tags: ['杂']
cover: assets/律师5.jpg
draft: false
---

## 任务一

```
POST /api/graphs/fulltext-search?haha=123 HTTP/1.1
Host: localhost
Cookie: xxx=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML,
like Gecko) Chrome/140.0.0.0 Safari/537.36 Edg/140.0.0.0
Sec-Ch-Ua: "Chromium";v="140", "Not=A?Brand";v="24", "Microsoft Edge";v="140"
Sec-Ch-Ua-Mobile: ?0
Accept: text/html
Referer: https://osgraph.com/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 26
indexName=github&keyword=1
```

### 请求行：

`POST /api/graphs/fulltext-search?haha=123 HTTP/1.1`

### 请求体：

`indexName=github&keyword=1`

从github索引库搜索关键词 1

## 任务二

### php

```php
<?php
// login.php
// 纯 PHP 登录示例，使用单独文件的数据库连接
// 引入数据库连接
require 'db.php';
// 模拟接收表单数据（CLI 或 Web POST）
if (php_sapi_name() === 'cli') {
 echo "请输入用户名: ";
 $username = trim(fgets(STDIN));
 echo "请输入密码: ";
 $password = trim(fgets(STDIN));
} else {
 $username = trim($_POST['username'] ?? '');
 $password = trim($_POST['password'] ?? '');
}
// 检查输入
if ($username === "" || $password === "") {
 echo "用户名和密码不能为空\n";
 exit;
}
// 查询数据库（明文密码示例）
$sql = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = $conn->query($sql);
if ($result && $result->num_rows > 0) {
 echo "登录成功！欢迎你，$username\n";
} else {
 echo "用户名或密码错误\n";
}
// 关闭数据库
$conn->close();
```

```php
CREATE DATABASE test;
USE test;
CREATE TABLE users (
 id INT AUTO_INCREMENT PRIMARY KEY,
 username VARCHAR(50),
 password VARCHAR(50)
);
INSERT INTO users (username, password) VALUES ('alice', '12345');
INSERT INTO users (username, password) VALUES ('bob', 'abcde');
```

登陆系统

### html

```html
<!-- login.html -->
<!doctype html>
<html>
<head><meta charset="utf-8"><title>Login</title></head>
<body>
 <h3>登录</h3>
 <form action="/login.php" method="post">
 <label>用户名: <input type="text" name="username" /></label><br>
 <label>密码: <input type="password" name="password" /></label><br>
 <button type="submit">登录</button>
 </form>
</body>
</html>


<!-- upload.html -->
<!doctype html>
<html>
<body>
 <h3>上传文件</h3>
 <form action="/upload.php" method="post" enctype="multipart/form-data">
 <input type="file" name="file" />
 <button type="submit">上传</button>
 </form>
</body>
</html>
```

登录界面，以及上传文件

## 任务三

### less 1

![PixPin_2025-09-30_00-28-00](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/PixPin_2025-09-30_00-28-00.png)

### less 5

![PixPin_2025-09-30_00-38-13](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/PixPin_2025-09-30_00-42-16.png)

### less11

![PixPin_2025-09-30_00-44-35](https://cdn.jsdelivr.net/gh/YU030X/blogpics@main/img/PixPin_2025-09-30_00-44-35.png)
