# KWT osbpb 规范

osbpb 是 eternalOS 的包管理器。

它不会管理依赖，也没有版本这个概念。

## 打包

使用 `osbpb -p` 进入打包环境。  
里面会挂载宿主机的部分目录为只读，如 `/usr/bin` 。  
构建输出需要把 `(chroot)/pkgroot` 当做 `/` 。  
当用户退出环境时，会询问用户是否打包，示例如下：  

```Terminal
[OSBPB:/] # exit
Are you want build this package?(y/n)y
Type pname here: example
SUCCESS! package placed at /root/example.tar.gz
```

## OSBPB 数据库

一般来说，osbpb 的数据库文件存放在 `/var/lib/osbpb/db` 里，是一个纯文本文件。它的内容大概如下：  

```osbpb db
[package1]
/usr/bin/example1
/usr/bin/example2

[package2]
/usr/bin/example3
/usr/bin/example4
```

## 其他参数

如下：

```
-p   启动打包环境
  -e   进入打包环境后，自动执行一个 bash 脚本
  -y   退出打包环境时，跳过询问，自动打包
-i <file> <pname>  安装一个包，并标记该包的名称
-u <pname> 卸载一个包
-l 列出所有已经安装的包
-o <pname> <file>  导出一个包。
```
