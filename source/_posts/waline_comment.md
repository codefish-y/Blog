---
title: Hexo|Butterfly 添加Waline评论系统
author: 小鱼
cover: /image/waline_comment/image-20221101134935755.png
date: 2022-11-01 13:42:56
tags: [comment,waline,主题美化,评论系统]
categories: [主题美化]
---

[Waline](https://waline.js.org/)是Valine的升级版，加入了后端管理系统。可在后台对评论进行编辑，删除，回复，置顶等操作。

![image-20221101135330958](../image/waline_comment/image-20221101135330958.png)

## 部署要求

- LeanCloud
- Vercel
- GitHub
- 一个域名

## LeanCloud

### 环境准备

注册LeanCloud国际版(一定要是国际版，否则需要另外准备备案的域名和服务器)

LeanCloud地址：https://console.leancloud.app/

![69d888dbe3e9a8c417498e40f8fc6563](../image/waline_comment/69d888dbe3e9a8c417498e40f8fc6563.png)

注册完成之后，前往邮箱验证。![a423e39bb71de190cc873e7f5ec5b59a](../image/waline_comment/a423e39bb71de190cc873e7f5ec5b59a-7282499.png)

![a423e39bb71de190cc873e7f5ec5b59a](../image/waline_comment/9cc8f71d7d7581afe83b1c0683a7629f.png)

验证成功后创建一个应用。![2721475878d8185416a57e601435822c](../image/waline_comment/2721475878d8185416a57e601435822c.png)

![bd043942d7cd9a4a5c4664b025b342ad](../image/waline_comment/bd043942d7cd9a4a5c4664b025b342ad.png)

进入`设置-应用凭证`，记下自己的`AppID-AppKey-MasterKey`。

![95b5ae65eef91832923f2ca41781e065](../image/waline_comment/95b5ae65eef91832923f2ca41781e065.png)

LeanCloud的设置已经完成了，接下来要换一个地方。

## Vercel

### 开始部署

在Vercel进行部署：[点我部署](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fwalinejs%2Fwaline%2Ftree%2Fmain%2Fexample)

![71d22876ad1e9540acfcd815968148fa](../image/waline_comment/71d22876ad1e9540acfcd815968148fa.png)

使用GitHub登陆，将Vercel安装到GitHub仓库。

![d8181e70e2f7924a20511af9921b9780](../image/waline_comment/d8181e70e2f7924a20511af9921b9780.png)

创建一个仓库：

![6db50e08193de8654bed4abf9858b78e](../image/waline_comment/6db50e08193de8654bed4abf9858b78e.png)

等待一会儿，出现满屏的烟花提示你部署成功了：

![bb02341e110bfd60a30f0e6ac85fbe1c](../image/waline_comment/bb02341e110bfd60a30f0e6ac85fbe1c.png)

Continue to Dashbored，去仪表盘`Settings - Environment Variables`,进入环境变量配置页。

并配置三个环境变量 `LEAN_ID`, `LEAN_KEY` 和 `LEAN_MASTER_KEY` 。它们的值分别对应上一步在 LeanCloud 中获得的 `AppID`, `AppKey`, `MasterKey`。

![28820abf39ed6026aa2b287da6d23934](../image/waline_comment/28820abf39ed6026aa2b287da6d23934.png)

填写完成后如图所示：

![2c3ce7c7bf4f53795b7e3f9dc74fcb2e](../image/waline_comment/2c3ce7c7bf4f53795b7e3f9dc74fcb2e.png)

环境变量配置完成之后点击顶部的 `Deployments`，然后`Redeploy`重新部署使环境变量生效。

![e280db1d0f92daac7ab09d6e42d9afd9](../image/waline_comment/e280db1d0f92daac7ab09d6e42d9afd9.png)

部署完成后点击`Visit`即可用vercel提供的域名访问评论系统：

![6470069e5ecea3b626021057bea5be47](../image/waline_comment/6470069e5ecea3b626021057bea5be47.png)

### 更换域名

但是由于Vercel的dns在国内已经被污染，我们需要将评论系统换成自己的域名；

- 点击顶部的 `Settings` - `Domains` 进入域名配置页
- 输入需要绑定的域名并点击 `Add`

![139cae3cf16b85d286caf08dbb124956](../image/waline_comment/139cae3cf16b85d286caf08dbb124956.png)

![411b6af824300d5bb1242f15323ac32b](../image/waline_comment/411b6af824300d5bb1242f15323ac32b.png)

在域名服务器商处添加新的 `CNAME` 解析记录

| Type  | Name                       | Value                |
| ----- | -------------------------- | -------------------- |
| CNAME | @/waline(根据实际情况填写) | cname.vercel-dns.com |

![73ea53d15e9da927cc32d9600f212789](../image/waline_comment/73ea53d15e9da927cc32d9600f212789.png)

等待生效，你可以通过自己的域名来访问了🎉

- 评论系统：waline.yourdomain.com
- 评论管理：waline.yourdomain.com/ui

### CloudFlare设置

如果提示网站重定向次数过多，无法访问，将CloudFlare的ssl设置为严格模式即可。

![image-20221102094744076](../image/waline_comment/image-20221102094744076.png)

![2bba4890299b973ae09c2080045eba0e](../image/waline_comment/b61064e19c973e1ef87dc44619ba4e3a.png)

### 注册为管理员

默认第一个注册的用户为管理员用户：waline.yourdomain.com/ui/register

![image-20221101143120596](../image/waline_comment/image-20221101143120596.png)

![image-20221101144000649](../image/waline_comment/image-20221101144000649.png)

## 站点配置

在站点根目录安装Waline；`npm install @waline/hexo-next --save`

编辑主题配置文件：`_config.butterfly.yml`

```yaml
comments:
  use: Waline
  text: true # Display the comment name next to the button
  # lazyload: The comment system will be load when comment element enters the browser's viewport.
  # If you set it to true, the comment count will be invalid
  lazyload: false
  count: false # Display comment count in post's top_img
  card_post_count: false # Display comment count in Home Page
```

往下找到Waline配置：

```yaml
waline:
  serverURL: waline.yourdomain.com    # 填入刚刚得到的后端 URL（必填,根据实际情况填写你自己的域名）
  option:
    locale: {
      nick: '昵称',
      mail: '邮箱',
      link: '你的网站',
      placeholder: '你这么可爱一定会文明评论的吧～(填写邮箱可以收到回复通知哦)',
      requiredMeta: [],
      mailError: '请填写正确的邮件地址',
      sofa: '快来抢占沙发~',
      level0: '锻体',
      level1: '练气',
      level2: '筑基',
      level3: '金丹',
      level4: '元婴',
      level5: '化神',
      }
    emoji: [
        'https://cdn.jsdelivr.net/gh/walinejs/emojis@1.0.0/tw-emoji',
        'https://cdn.jsdelivr.net/gh/norevi/waline-blobcatemojis@1.0/blobs',
        'https://cdn.jsdelivr.net/gh/walinejs/emojis@1.0.0/bilibili',
        ]
```



`hexo cl && hexo g && hexo d`部署

![c022eb9a4f50feb8e281dd9a416d0470](../image/waline_comment/c022eb9a4f50feb8e281dd9a416d0470.png)
