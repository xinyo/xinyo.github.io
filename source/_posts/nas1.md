---
title: '宅家闲折腾 搭建自己的媒体库 系列一: 用FREENAS搭建NAS系统'
date: 2020-02-15 13:31:10
tags: ['折腾', 'NAS', 'FreeNas', '生活']
---

去年搬新家了以后买了台新电视, 当时只想着以最低的成本买最大的尺寸, 于是选了这货 海信 75P7
什么都好就是毫无扩展能力, 除了预装的youtube netflix 和 Plex以外基本上不能再干啥了. 当初的想法是搭配nVIDIA Shield一起食用
然而当时的时间点比较尴尬, nVIDIA Shield 2019刚刚发布但是土澳发售时间却迟迟未定, 一拖再拖之下结果发现了这货 斐讯N1 
只需要人民币一百块都不到就可以实现超越大多数电视盒子的解码能力 ...真香
趁着年前就入手了

这篇文章就是折腾之路的开始


# NAS系统的搭建
## 硬件方面
我的选择用手头上退休的x86架构的硬件来实现下岗再就业, 手头有CPU 内存 硬盘 机箱 电源, 上某宝买了块二手主板即可, 全部花费大概100多
这里建议大家选择自己手边合适的硬件即可
img

## 网路拓扑
我使用的是手头上一个多年前的Linksys路由EA7500v2
现实环境下路由器即便支持5G频段1300M, 路由和网卡都支持MU-MIMO, 电脑放在路由旁边其实最多也只能跑到60MB/s左右
这里建议除非像我一样主力机离路由器较远而且不方便布线, 不然还是尽可能选择有线连接
img


# 选择FREENAS作为底层系统
基本的结构如下
硬件层 - 底层OS - 虚拟机 - Docker - 应用层
可以参考催眠的小伙伴司波图的[玩转Freenas系统系列视频](https://www.bilibili.com/video/av63317880/)

## 准备工作
U盘一个
最好有一个电视或者显示器连接在NAS主机上
基本的局域网知识

## 安装的过程
首先去FREENAS官网[下载最新的镜像](https://www.freenas.org/download-freenas-release/)
用`Win32DiskImager`, `imageUSB`又或者你熟悉的镜像制作工具将下载好的镜像烧录到U盘里
插入U盘到NAS主机上, 重新启动; 这里注意可能需要到BIOS里面调整启动盘
进入安装界面 选 `1.Boot FreeNAS Installer`
`1 Install/Ugrade`
接下来一步如果提示内存不足, 选YES继续即可
下一步选择系统的将会被安装到的磁盘, 注意整个磁盘都会被占用, 也可以安装到另一个容量8GB以上的U盘上将来以U盘的方式来启动
下一步提示全盘将会被格式化, 按确定
设置root账号的密码, 如果按cancel则留空
设置安装程序启动方式 选择默认即可
安装结束后选择`3 Reboot System`重启
这时候可以把安装的U盘拔掉了. 重启后记得再次进入BIOS设置新的启动盘为刚才安装好系统的磁盘
首次进入会花一点时间
等待完全加载完成后 选择`1 Configure Network Interface`, 设置好固定的IP地址(记得网段要跟家里局域网一致)方便日后使用, 现在你可以通过刚刚设置的地址又或者默认的`freenas.local`来访问FREENAS了


# 按F进入坦, 我是说FREENAS
## 基本设置
用户名为`root`, 使用之前创建系统时设置的密码登陆

如果需要中文可以在`System` -> `General`里面设置
我们第一步还是要设置好网络环境 打开`Network` -> `Global Configuration`
这里把`IPv4 Default Gateway`以`及Nameserver`都设置成路由器的局域网IP

## 存储设置
打开`Storage`, 点击右上添加`ADD`一个新的`Pool`
随心所欲地设置名字(假设名字是`pool1`), 在左边列表里选择可用的磁盘, 创建即可
现在你有了你第一个存储池! 
选择`pool1`最右边的下拉菜单, 选择`Add Dataset`创建一个新的`dataset`(假设名字是`dataset1`), dataset可以简单理解成一个文件夹
在FREENAS里, 这个dataset的地址就是`/mnt/pool1/dataset1`

## 共享设置
取决于你主力机的系统, 以Windows为例我们设置一个[SMB协议](https://www.wikiwand.com/zh/%E4%BC%BA%E6%9C%8D%E5%99%A8%E8%A8%8A%E6%81%AF%E5%8D%80%E5%A1%8A)的共享
打开`sharing`->`Windows (SMB) Shares` 依旧点击右上添加
点击文件夹图标选择想要共享的路径, 例如`/mnt/pool1/dataset1`, 保存

## Windows下访问FREENAS的共享
按下`win`+`r`打开运行界面
输入设置好的FREENAS地址 (如果忘记了, 可以在浏览器打开[freenas的控制面版](http://freenas.local/ui/dashboard), 在`Primary NIC`下面就可以看到此ip地址)
打开后就可以看到`dataset1`的共享了, 右击它选择`网络硬盘映射`就可以把它添加到你的window里, 和平常普通的磁盘使用没有区别

# 尾巴
现在你有了自己的NAS了! 