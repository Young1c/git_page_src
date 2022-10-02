# Fedora 密码找回

## Fedora登录密码忘记怎么办?

随便搜索了一下，没有找到能够找回密码的方法。但是可以重置用户登录密码。按照以下步骤进行：

1. 进入grub界面，修改系统的启动参数。增加如下参数命令。

```
... rw init=/bin/bash
```

2. 登录到系统后，切换到root用户
3. 首先通过passwd修改root用户密码。紧接着执行passwd <user>修改<user>用户的密码 （没有试过是否一定要修改root用户密码，可能并不需要？）
4. 在根目录下创建.autolabel和autorelabel文件

```
$ touch /.autorelabel
$ touch /autorelabel
```

5. 重新启动系统
6. 使用修改后的密码能够正常登录

[参考](https://jingyan.baidu.com/article/54b6b9c0bd88ca2d583b47c7.html)

## 什么原理呢?

### autorelabel文件的作用

参考下这个[文档](https://www.cnblogs.com/songhaixing/p/13650755.html)以及[stackexchange](https://unix.stackexchange.com/questions/509798/what-does-touch-autorelabel-do-when-we-reset-the-root-password-in-red-hat-en#:~:text=The touch %2F.autorelabel command creates a hidden file,process can take a good amount of time.)，貌似和selinux有关系。。。那以后再看。

## 系统安全问题

既然可以通过这种方式修改登录密码，系统会变得很不安全。所以需要对grub进行加密，以拦截掉非法的修改。

```
$ grub2-setpassword # 直接设置grub密码
或者
与ssh类似，通过密钥的方式进行加解密(貌似openeuler的grub默认会使用密钥对grub进行加密)
```



