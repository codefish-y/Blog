---
title:  解决run `npm audit fix` to fix them
author: 小鱼
date: 2022-09-26 18:49:35
tags: npm
cover: /image/npm_fix/npm_fix.jpg
categories: [技术分享,bug解决]
---

今天给站点添加看板娘的时候，npm安装插件出现了“run `npm audit fix` to fix them”的报错，一般情况下根据提示运行 `npm audit fix` 就能解决这个问题了，但是众所周知，一下子就能解决的问题就不叫问题了。这里放出两个解决方案，第一种不行就用第二种。

## <font color="\#4682B4">出现原因</font>

这个问题是因为npm安装插件时依赖版本不匹配所造成的，可能是所用的依赖太老，所以npm的自动检查发现了高危漏洞 `high vulnerabilities` 

```bash
added 253 packages from 162 contributors and audited 1117 packages in 42.157s
found 5 vulnerabilities (1 low, 4 high)
  run `npm audit fix` to fix them, or `npm audit` for details html
```



## <font color="\#4682B4">解决方案</font>

#### <font color="\#Fa889">方案一： </font>

这是最简单的，但是成功的概率小，因为有些依赖不能被自动修复。

运行所提示的修复命令：

```bash
npm audit fix
```

如果返回0 vulnerabilities就修复成功，否则继续：

```bash
npm audit fix --force
```

```bash
npm audit
```

如果不行的话就得采用第二种方案

#### <font color="\#Fa889">方案二： </font>

既然现有的npm镜像不能自动修复依赖，那我们就换一个镜像源：

```bash
npm install -g nrm
```

如果这一步无法安装，提示 `permission denied` 	那就是权限不够，使用sudo加权安装：

```bash
sudo npm install -g nrm
```

安装完成之后可以使用下面的命令查看各个仓库的地址：

```bash
nrm ls
```

可以看到以下内容：

```bash
wei@MacBook-Pro Hexo % nrm ls

  npm ---------- https://registry.npmjs.org/
  yarn --------- https://registry.yarnpkg.com/
  tencent ------ https://mirrors.cloud.tencent.com/npm/
  cnpm --------- https://r.cnpmjs.org/
  taobao ------- https://registry.npmmirror.com/
  npmMirror ---- https://skimdb.npmjs.com/registry/

```

​	第一个npm是npm官方的仓库，既然它更新不了，我们就换一个仓库，这里我选择的是taobao：

```bash
wei@MacBook-Pro Hexo % nrm use taobao
                        

   Registry has been set to: https://registry.npmmirror.com/


```

可以看到，仓库已被设定为 `https://registry.npmmirror.com/` ，接下来就可以再次尝试安装插件了，这次应该不会报错，且插件正常安装使用～
