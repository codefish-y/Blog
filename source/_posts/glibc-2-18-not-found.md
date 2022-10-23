---
title: 【solved】glibc_2.18_not_found
author: 小鱼
date: 2022-09-27 19:51:19
tags: [glibc,gcc,linux,centos7]
categories: [技术分享,bug解决]
cover: /image/glibc_2.18_not_found/glibc.jpg
---

oops😱!  `nginx: /usr/lib64/libc.so.6: version ``GLIBC_2.18' not found (required by /usr/lib64/libstdc++.so.6)`

这是什么东西？为什么会说有一个叫做 `GLIBC_2.18` 的东西找不到？

## <font color="\#4682B4">前言</font>

在解决这个问题之前，我们先来认识一下 `GLIBC` 到底是什么，为什么会发生这个问题。

[GLIBC](https://www.gnu.org/software/libc/) 是为GNU/Linux和其他类Unix系统所设计的 C 标准库，也就是说，你在Linux上用gcc编译程序的时候 ，实际上是依赖了这个库的，而它又是一个系统核心库，是最底层的API，这就标识着它的地位，几乎可以说，只要你的项目中包含了 C ，就离不开它的支持。比如 `nginx` ,`node` 这些我们熟知的热门项目都是依赖于 `glibc` 的，就像第一段，那就是 nginx 所依赖的 glibc 版本没有找到。



## <font color="red">注意⚠️</font>

手动编译更新GLIBC有风险，要是编译失败导致系统命令诸如ls这些不可用，请使用命令将链接改为系统之前版本：

```bash
LD_PRELOAD=/lib64/libc-2.17.so ln -s /lib64/libc-2.17.so /lib64/libc.so.6
# 这里的2.17可根据实际情况修改
```

```bash
# 如提示libc.so.6已存在，请先删除然后重新创建链接：
rm -rf /usr/lib64/libstdc++.so.6
```





## <font color="\#4682B4">问题解决</font>

既然说某个版本的 `glibc` 没有找到，那我们就先看看系统里面现在用的是哪些版本的。

输入以下命令查询系统当前所用 `glibc` 的版本：

```bash
strings /usr/lib64/libc.so.6 | grep GLIBC_2.1
```

```bash
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_2.15
GLIBC_2.16
GLIBC_2.17
```



发现刚好截止到 `GLIBC_2.17` ,而nginx说它要2.18版本的，那我们就给系统重新编译一个2.18版本的GLBC：

```bash
wget http://mirrors.ustc.edu.cn/gnu/libc/glibc-2.18.tar.gz
# 使用wget命令从官方下载一个2.18版本的glibc
```

将下载的glibc解压：

```bash
tar -zxvf glibc-2.18.tar.gz
# 解压的目录无特别要求，我这里直接解压在当前目录
```

依次输入以下四个命令：

```bash
cd glibc-2.18/
# 进入到解压的glibc目录
```

```bash
mkdir build && cd build
# 创建build目录并进入到这个工程目录，官方要求必须在build目录编译glibc
```

```bash
../configure --prefix=/opt/glibc-2.18
# 会跑一大段信息，只要没有报错就不用管
```

```bash
make -j4 && make install
# 使用j4编译，并且编译完成后安装，这里根据机器性能所用时间不定，十几分钟也是正常的，安心等待
```



配置并查看现在机器所用glibc的版本：

```bash
export GLIBC_PATH=/opt/glibc-2.18/lib
# 配置GLIBC环境变量
```

```bash
strings /usr/lib64/libc.so.6 | grep GLIBC_2.18
# 如果这一步没有任何信息返回，请输入下面的命令：
LD_PRELOAD=/lib64/libc-2.17.so strings /opt/glibc-2.18/lib/libc.so.6 | grep GLIBC_2.18
```

```bash
ldconfig
# 手动链接动态库
```



现在GLIBC已经更新到2.18版本，运行nginx命令已经没有错误提示啦～
