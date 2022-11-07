---
title: hexo
date: 2020-02-16 16:06:27
tags:
---
这篇blog就是简单记录一下安装hexo会遇到的小问题
# 添加 Gittalk 评论插件
Gitalk 是一个基于 GitHub Issue 和 Preact 开发的评论插件
基本的安装流程参照官方文档即可
需要提到的有一下几点:
## 集成的步骤
layout/_partials/comments.swig
layout/_third-party/comments/index.swig
_config.yml
还需要在layout/_third-party/comments/路径下，添加我们自己的文件gitalk.swig。下面我们一步一步来。

## 预先建立好GitHub用来存放gitalk的repo
因为gitalk是基于GitHub Issue的, 因此最好是额外新建一个repo来存放
申请 GitHub Application需要使用 GitHub Application，如果没有 [点击这里申请](https://github.com/settings/applications/new)，基本上最后一栏`Authorization callback URL`填上会使用的网站网址即可, 其他可以随意
生成后记录下Client ID 和 Client Secret

## md5解决文章title太长的问题
不建议直接使用默认的 id: location.pathname, 即 文章链接
这是 Github 的限制，我们的 id 使用的是文章的相对路径，路径中含有标题。所以一种解决方案是文章标题短一点；另一种解决方案是对路径算 hash，通过 MD5 加密 id 来缩短label长度，这样，不论标题多长都没有问题。具体操作如下： 

## 使用OAuth App还是access_token的问题
https://github.com/theme-next/hexo-theme-next/pull/1365
## 如果config的值定义在主题文件夹内
## gitalk 配置文件的设置
基本上像这样配置就可以了, 属性的值可以定义在这里也可以定义在主题文件夹的_config.yml下
可以在主题中添加一个gitalk.ejs文件
内容如下
```
<script type="text/javascript">
    const gitalk = new Gitalk({
        clientID: 'xxxx',
        clientSecret: 'xxxxxxxxx',
        accessToken: 'xxxxxxxx',
        repo: 'gitalk',
        owner: 'xinyo',
        admin: ['xinyo'],
        id: md5(location.pathname)
        // labels: '{{ theme.gitalk.labels }}'.split(',').filter(l => l),
        // perPage: '<%=theme.gitalk.perPage%>',
        // createIssueManually: false
    })

    gitalk.render('gitalk-container')
</script>
```
# 其他小问题
## i.concat(…).join is not a function
这个问题是 labels 部分的问题
```
{
   labels: '{{ theme.gitalk.labels }}'.split(',').filter(l => l),
}
```
https://github.com/gitalk/gitalk/issues/114

## 未找到相关的 Issues 进行评论
大概率是因为id和labels的设置导致没有匹配到
出现了这个提示不要紧，你只要部署后，使用github登录就好了。去application里点击一下Reset client secret，重新生成一个secret。回去清下内存，刷新下页面，如果还是显示 Bad credentials，尝试点击一下评论框的Login登录一遍。
## 

# TODO:
文章分享（微博、微信、空间）链接

添加[Zooming](https://github.com/kingdido999/zooming)

添加邮箱 RSS