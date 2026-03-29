# Unprotected admin functionality-Burp 复现

## 实验信息

- 平台：PortSwigger Web Security Academy
- 漏洞：Unprotected admin functionality 
- Lab: Unprotected admin functionality&Unprotected admin functionality with unpredictable URL
- 难度：Apprentice

## 漏洞原理

该漏洞属于**Broken Access Control(访问控制失效)**，原理是访问控制缺失 + 敏感信息泄露，核心原因是网站管理员后台未做任何身份认证与权限控制，仅依靠隐藏管理面板路径来实现 “安全防护”。同时，敏感路径被泄露在 `robots.txt` 或前端 JavaScript 代码中，攻击者可轻易获取管理员面板地址，直接访问并执行删除用户等管理员权限操作。

## 测试过程

Lab 2:

1. 访问实验环境，查看status code是200，右键发送至Repeater

![get](images\lab2-get.png)

2. 在Request中修改为robots.txt, 查看内部内容, 找到admin panel路径

![robots](images\lab2-robots.png)

3. 在Request中修改为admin-panel的路径， 找到carlos对应的前端代码

![admin](images\lab2-admin.png)

4. 将carlos删除

![delete](images\lab2-delete.png)

5. 成功删除，实验显示Lab solved!

![solved](images\lab2-solved.png)
Lab3:
1. 找到JS中对于Admin认证的相关部分(不过有意思的是我每一次刷新网站JS的setAttribute后面的内容有所改动，但是这个admin路径还是暴露了)

![get](images\lab3-get.png)

2. 将admin路径部分复制到Request中，找到Carlos

![admin](images\lab3-admin.png)

3. 删掉

![delete Carlos](images\lab3-delete.png)

4. Lab solved！

![Lab solved](images\lab3-solved.png)



## 利用Payload

```http
GET /robots.txt HTTP/1.1
```

只有这一个需要记忆，其余的在Response里面都有。robots.txt可以加在很多网站URL后面，不过我尝试了访问Disallow的内容大部分是404。

## 个人总结

​	今天看到一句学安全要重点记住的Triad(Not CIA Triad): 第一， 如何利用这个漏洞？第二，为什么会产生这个漏洞？第三，如何修复这个漏洞？后续的小结我也会遵循这个三段式复盘每一次学习的内容。

​	如何利用？通过robots.txt或是F12查看源码找到泄露的admin panel, 只要能够有admin panel，就破坏了confidentiality，integrity随之破坏。

​	为什么产生？大概就是开发者在robots.txt和JS上没有处理好，让unauthorized user可以访问admin panel。如何修复？robots.txt肯定不能写上这么硬核的内容，JS我还在深入，细节我不清楚，但是像这个靶场提供的setAttribute直接把admin的path拼接起来frontend就能看到肯定不合适，这种内容应该得放backend。

​	虽然这两个lab是apprentice，不过慢慢下来收获还是不小的。作为一名大学二年级的网络安全初学者，很乐意能在平台上和大家交流学习、共同进步。