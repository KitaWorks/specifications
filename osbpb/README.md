# KWT osbpb 规范

规范版本：20250726-v1

osbpb 是 eternalOS 的包管理器。

它不会管理依赖，也没有版本这个概念。

## 文件扩展名

`*.okg` - 一个正常的软件包。  
`*.osb` - 描述一个软件包应该如何被构建的文件。  
fun fact：`*.okg` 是因为 osbpb 创始人不小心打错 `*.pkg` 而意外创造的扩展名。使用 20250722-v2 标准及以前标准的包管理器使用 `*.pkg` 作为软件包扩展名。  

## 打包

使用 `osbpb -p` 进入打包环境。  
里面会挂载宿主机的部分目录为只读，如 `/usr/bin` 。  
构建输出需要把 `(chroot)/pkgroot` 当做 `/` 。  
打包环境里面没有网络。  
当用户退出环境时，会询问用户是否打包，示例如下：  

```Terminal
[OSBPB:/] # exit
Are you want build this package?(y/n)y
Type pname here: example
SUCCESS! package placed at /root/example.tar.gz
```

由于打包环境没有网络，所以用户还必须利用 `-s <folder>` 选项将源代码包裹放在打包环境的 `/srcs` 里面。  

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

## 软件包结构

软件包的扩展名为 `*.okg`，其本质只是一个 `*.tar.gz` 而已。  
软件包拆开之后如下：  
```
package.info
data.tar.gz
```

其中，`package.info` 放置了软件包的元数据，而 `data.tar.gz` 才是真正的打包时 `(chroot)/pkgroot` 的内容。

`package.info` 的格式如下：

```Package Info
pname=example
version=1.0.0-r1
deps=[dep1,dep2]
```

## 其他参数

如下：

```
-p 启动手动打包环境
-e 打包一个 *.osb 文件
-i <file> 安装一个包。
-u <pname> 卸载一个包
-l 列出所有已经安装的包
-o <pname> <file> 导出一个包。
-s <folder> 将指定文件夹中的所有内容复制到打包环境内的 /srcs 里面。
-h 显示版本，兼容的规范版本，以及帮助信息
```

## *.osb 格式

待定。

## osbpb-get 组件

osbpb-get 和 osbpb 是同一个组件。曾经，它们两个是不同的组件。但是，它们的调用方式仍然是两个命令（待定）。  
osbpb 在 osbpb-get 的基础上，添加了依赖管理，远程软件源（eUP 和 eUB）。  
eUP，即 eternalOS User Packages，是为用户提供软件包构建脚本（即 `*.osb`）的平台。官方未来将会提供官方版本的 eUP。  
eUB，即 eternalOS User Binary Packages，是为用户提供二进制软件包（即 `*.okg`）的平台。eUB 需要用户自行搭建，官方没有提供它的计划。  

其他待定。

## 未来计划

### 版本管理

我们强制使用 0.0.0 这种格式的版本号。不过也可以使用 0.0.0-rN 这种版本号（N 使用数字代替）。其他格式的版本号不被允许。  
一个包可以被升级也可以被降级。 

osbpb 打包环境退出时将会提醒输入版本号，并且输出的文件名将会变成类似于 `example-1.0.0.tar.gz` 的格式。  
osbpb 数据库中的 `[pname]` 将会变成 `[pname=version]`。  
