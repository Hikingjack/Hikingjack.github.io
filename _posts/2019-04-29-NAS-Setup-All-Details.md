---
layout:     post
title:      如何安心搭建家用群晖NAS
subtitle:   有这一篇基本就够玩转NAS了
date:       2019-04-29
author:     madranger
header-img: img/NAS_personal_cloud.jpg
catalog: true
tags:
    - NAS
    - 群晖
    - Synology
typora-root-url: ..
---



Photo by [rawpixel.com](https://www.pexels.com/@rawpixel?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [Pexels](https://www.pexels.com/photo/person-holding-pen-drawing-on-paper-1483937/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)



## 一篇极具煽动性的文章

最早了解到NAS是几年前了，无奈漂泊的租房生活，迟迟没有下手。几个IT Geek型的朋友的各样花式玩法，更是让人忍不住想折腾一番。

比如，一个朋友自己买了HP的工作站，从零开始搭建NAS，搭载了40T的硬盘，创建了私有4K影院。:joy_cat: 另一个朋友用旧的笔记本，不仅实现了文件、影音管理，最关键是集成海康威视的夜视动态跟踪摄像头，让我想起了读研时参与的女子监狱研发项目。:joy_cat:

好了，道理都懂，但是有时候就是差一篇点燃你心中小火苗的文章。我看到的是这一篇，推荐一下。

![https://post.smzdm.com/p/672137/](/img/nas/first_article.jpg)

在搭建NAS的过程中，我的朋友[CRO](http://blog.icro.me/)给予了很多帮助，他的两篇文章也非常受用。

- [如何让你的群晖通过顶级域名外网访问](http://blog.icro.me/2019/01/04/Nas-01/) :+1:
- [如何让你的顶级域名拥有SSL证书](http://blog.icro.me/2019/01/09/Nas-02/) :+1:



## 目录

:one: [准备剁手](#准备剁手) - 购买建议

:two: [开始搭建](#开始搭建) - 基础配置

:three: [外网访问](#外网访问) - 基本HTTP访问

:four: [使用HTTPS访问](#使用HTTPS访问) - SSL证书护航



## 准备剁手

如果你的家用网络设备比较陈旧，建议考虑整体升级。下面的剁手清单是我目前搭建所用的设备，仅供参考。



#### 千兆路由器 - Netgear R7000

这是一款广受好评，性价比较高的千兆路由器。该有的功能都有了。

**售价**：750 RMB左右

**参考链接**：<https://bit.ly/2ZFHbpP>

**使用感受**：网络稳定，信号强劲，散热不错，网上有丰富的刷梅林教程。



#### 群晖- DS418Play

个人感觉性价比最优的选择。

**售价**：3,900 RMB左右（加装4G内存，另需500 RMB左右）

**参考链接**：https://bit.ly/2GLlaNA

**使用感受**：4盘位确保了家用存储的需要，体积小巧，放在客厅毫无违和感，安静，散热良好（生活在热带地区，比较关注这点）。



#### UPS - APC施耐德BK650-CH

确保你的硬盘避免断电、雷暴造成的损害。

**售价**：550 RMB左右

**参考链接**：https://bit.ly/2XSEG1G

**使用感受**：重，搬运注意保护好腰。



#### 硬盘 - 希捷ST4000VN008

确保你的硬盘避免断电、雷暴造成的损害。

**售价**：780 RMB左右 / 4TB / 片

**参考链接**：https://bit.ly/2IObWnm

**使用感受**：目前测试磁盘速度OK，如果你考虑西数红盘，需要看看其他测评文章。



#### 网线 - ELECOM Cat8

千兆网线，不辜负你的路由器和NAS。

**售价**：100 RMB / 3米

**参考链接**：https://bit.ly/2XV1gH3

**使用感受**：镀金片包头，传说中可以降低信号损耗，并有效屏蔽干扰。



#### 个人域名（可选）

如果你想从外网使用自己的域名访问家里的NAS的话。



#### 开发者SSL证书（可选）

如果你想让自己的域名前面有HTTPS字样，稍微安全点。



#### 其他网盘会员（可选）

非常重要的东西，还是多备份几遍。目前我使用Google Drive和百度云跟家里的NAS对指定的目录同步。免费账户里的几十GB空间，足够日常使用。





## 开始搭建

#### 初始安装 - 观看5分钟视频

- 先把UPS安装好，但先不要开电源。
- 群晖开箱后，说明是非常简单，以至于不熟悉硬件的朋友，安装硬盘都摸不到头脑。建议观看[**这个官方视频**](https://bit.ly/2XQXKgP)，文中有详细的接口说明。
- 所有设备安装之后，确保NAS在家里的网络环境下，再打开UPS电源，最后打开NAS。
- 使用家里的电脑*（不推荐用手机进行配置）*，在同一个网络下，在Chrome（推荐浏览器）中，输入地址[find.synology.com](find.synology.com)，跟随安装引导，注册用户，设置NAS。
- [这篇文章](https://www.synology.com/zh-cn/knowledgebase/DSM/help/DSM/MainMenu/get_started)介绍NAS创建好之后，内网地址和端口的情况。



#### 基本配置

推荐的基本配置如下，其他设置可以稍后慢慢自定义。

1. **安装套件**，比如Download Station, Video Station, Moment, USB Copy等。
2. **管理当前用户**，比如设置新的密码，推荐打开`2步骤验证`
3. **进入控制面板**，创建文件目录，定义用户和权限组。

![](/img/nas/basic_setting.jpg)



#### 确保电脑可以访问文件目录 （很重要:exclamation: ）

- 进入**控制面板** :arrow_forward: **文件服务** :arrow_forward: **SMB/AFP/NFS**，检查下面的配置。

![](/img/nas/network_folder.jpg)



- **Windows 10 的网络文件目录访问方式**

  - 首先，在右键**我的电脑**，在**属性**页面里，确保`Workgroup`和NAS里的**工作组群**一致。

  ![](/img/nas/win_workgroup.jpg)

  - 之后，在文件目录里，输入NAS里显示在`PC(Windows)资源管理器`的值，比如`\\mad`。如果正常的话，会弹出对话框要求输入NAS的登录名和密码，之后应该可以看到下图：

  ![](/img/nas/win_folder.jpg)

  - 如果无法访问（典型的0x80070035错误），检查以下配置：

    - 尝试使用内网IP地址+端口号访问
      如下图，路由器给NAS自动分配的内网IP是`192.168.1.16`*（稍后可以在路由器中固定这个IP）*，默认端口号5000，可以在**控制面板** :arrow_forward: **连接性** :arrow_forward: **网络** :arrow_forward: **DSM设定**，看到DSM端口，这里先用HTTP端口测试。

      例如，我的环境下，`\\mad`UNC路径访问时空文件夹，但是`\\192.168.1.16:5000`没有问题。需要继续找原因。

    ![](/img/nas/nas_local_ip.png)

    - 查看PC上的网络链接设置

      在**系统设置**的**网络设置**中，点击**修改连接属性**。

      ![](/img/nas/pc_network_setting_01.jpg)

      在打开的页面中，选择**私有**。之后在试试`\\mad`网络路径。

      ![](/img/nas/pc_network_setting_02.jpg)

    - 如果还是不行，下面的几篇文章可能可以解决你的问题。

      https://bit.ly/2Wd3b9B

      https://bit.ly/2VqKulJ

      https://bit.ly/2J2dycl

- **Mac OS的网络文件目录访问方式**

  - 我的Macbook访问网络文件目录比较顺利，可以在Finder中找到，点击`Connect As...`输入用户名/密码后，可以看到下图：

  ![](/img/nas/mac_folder.jpg)

  

- 如果上述访问均成功，可以考虑在**控制面板** :arrow_forward: **连接性** :arrow_forward: **安全性** :arrow_forward: **防火墙**中，启用防火墙。在测试下Windows和Mac是否可以正常访问。这一步是为了接下来设置外网访问最准备，尽管路由器有物理防火墙的作用，但多一些防护总是好的。



到目前为止，NAS基本可以投入使用了。



## 外网访问

#### 外网访问须知

注意，在NAS基本配置完成之后，其实已经可以借助群晖的`QuickConnect`地址`http://QuickConnect.to/你的NAS名称`，从外网访问。但是有两个弊端：

- 这是群晖的转发服务，访问速度会受到更多因素影响。
- 默认没有支持HTTPS，安全性较低。



#### 主要步骤

如果需要使用私有域名访问家里的NAS，主要有以下6步：

​	:one: [验证公网IP](#验证公网IP)

​	:two: [购买域名](#购买域名)

​	:three: [创建DDNS](#创建DDNS)

​	:four: [修改私有域名的NameServer](#修改私有域名的NameServer)

​	:five: [设置NAS的外部访问](#设置NAS的外部访问)

​	:six: [设置路由器端口映射](#设置路由器端口映射)



#### 验证公网IP

打开路由器后台管理地址，查看获取到的 IP 地址是否和 https://ip.sb/ 显示的地址是否一样。如果一样的情况下，一般为公网IP。 不一样的话，或许不是公网IP，请联系你的运营商客服。

以NetGear R7000原生固件为例。

![](/img/nas/ip_netgear.jpg)



![](/img/nas/ip_checking.jpg)



#### 购买域名

在各大域名商自行购买。



#### 创建DDNS

这里选择使用[DNSPOD](http://blog.icro.me/2019/01/04/Nas-01/dnspod.cn)做域名的解析，至于什么是DDNS？请参考这篇文章-[DDNS的介绍](https://hsk.oray.com/news/3774.html)，而DNSPod是国内免费智能DNS解析服务商，此外还有很多别的选择。国外用户可以用www.dnspod.com

首先，选择「域名解析」栏，添加你申请的顶级域名:

![](/img/nas/dnspod_add_domain.jpg)

进入添加域名的详细页面，新增以下一条记录:

![](/img/nas/dnspod_config_domain.jpg)

- **第一条**，确保`www`+`你的域名`可以工作。主机记录：可以填127.0.0.1，或者直接填写你的IP。因为DDNS服务稍后会探测到你的IP变化，自动更新。
- **第二条**，确保只在浏览器输入`你的域名`，也可以访问。
- **第三条**，稍后HTTPS配置时，再填写。

之后，在DNSPOD的**用户中心** :arrow_forward: **安全设置** :arrow_forward: **API Token**，输入名称并创建API Token，请务必保存好这个信息！

最后，保存上图中的2个NS记录，在下一步中的域名供应商处，进行修改配置。

> f1g1ns1.dnspod.net
>
> f1g1ns2.dnspod.net



#### 修改私有域名的NameServer

以我的域名为例，需要登录到Godaddy网站，修改DNS配置。

![](/img/nas/godaddy_config_ns.jpg)



修改此处的记录为刚才保存的DNSPOD提供的2个Name Server地址。

> f1g1ns1.dnspod.net
>
> f1g1ns2.dnspod.net

![](/img/nas/godaddy_config_ns_done.jpg)



#### 设置NAS的外部访问

登录NAS，在**控制面板** :arrow_forward: **连接性** :arrow_forward: **外部访问**中，选择**DDNS**，新增一条记录，如下：

![](/img/nas/nas_ddns_config.jpg)



确定后，稍等几分钟后，刷新NAS的页面，可以看到结果。如果有问题的话，请再次检查DNSPOD网站中的配置信息。

_**Note:**_ *现在，刷新 Dnspod 控制台页面也会看到对应的子域名记录值已经不是 127.0.0.1，而且我们真实的公网 IP。当公网 IP 发生变化时，DDNS服务会自动更新子域名的记录值。这样只需通过子域名就可以访问到我们的设备。*

![](/img/nas/nas_ddns_config_good.jpg)



#### 设置路由器端口映射

由于NAS默认端口是5000，且电信运营商一般会屏蔽80, 8080端口，我们需要手动在路由器做`端口转发`。

登录NetGear R7000，在**高级** :arrow_forward: **高级设置** :arrow_forward: **端口映射/端口出发**中，新增一条`端口映射`

![](/img/nas/netgear_port_foward.jpg)

这里的**内部IP地址**是路由器分配，或者你指定的。端口配置为一个范围：

> 5000 - NAS的默认HTTP端口
>
> 5001 - NAS的默认HTTPS端口

保存后，**路由器会自动重启**。

到此，你应该可以用`http://www.你的域名:5000`访问家里的NAS了。NAS默认的HTTP端口5000，目前是必须输入的，除非设法打开路由器的公网80端口。安全起见，建议不要尝试。



## 使用HTTPS访问

#### 主要步骤

​	:one: [通过阿里云申请免费SSL证书](#通过阿里云申请免费SSL证书)

​	:two: [为NAS导入证书](#为NAS导入证书)

​	:three: [测试](#测试)



#### 通过阿里云申请免费SSL证书

如果钱包宽裕，可以考虑购买SSL证书，具体优点请参考各大供应商。不过，有一些供应商为个人开发者提供免费的SSL证书。在我的配置中，采用阿里云免费SSL证书。

首先，登录阿里云（淘宝账号同步）的[SSL证书页面](https://bit.ly/2WaTI2h)，选择**免费证书**，并跟随引导购买。

![](/img/nas/alicloud_ssl_cert.jpg)



根据网页要求，填写必要信息，申请审核。这里说明一下，<u>**没有必要**</u>在NAS里**控制面板** :arrow_forward: **连接性** :arrow_forward: **安全性**  :arrow_forward: **证书**，生成CSR。

一般睡一觉后，审核通过。可以在控制台看到已签发的证书。

![](/img/nas/alicloud_ssl_cert_done.jpg.png)

点击并下载**Apache服务器**类型的证书。

![](/img/nas/alicloud_ssl_cert_download.jpg)

在本地解压后，得到3个文件。稍后会用到。

![](/img/nas/alicloud_ssl_cert_unzip.jpg)



#### 为NAS导入证书

首先，在NAS的**控制面板** :arrow_forward: **连接性** :arrow_forward: **网络**  :arrow_forward: **DSM设置**里将HTTP请求自动转向HTTPS。

![](/img/nas/nas_cert_00.jpg)



登录NAS页面，在**控制面板** :arrow_forward: **连接性** :arrow_forward: **安全性**  :arrow_forward: **证书**里，新增一个证书，填写名称并点击下一步。

![](/img/nas/nas_cert_01.jpg)



将3个证书，选中在对应的位置，如下。并保存。

![](/img/nas/nas_cert_02.jpg)

选中刚创建好的证书，点击**配置**。

![](/img/nas/nas_cert_03.jpg)

将3个条目，均选中刚创建的证书。

![](/img/nas/nas_cert_04.jpg)



单击**确定**后，**NAS的Web服务器会自动重启**。



#### 测试

如果一切正常，现在你已经可以通过HTTPS访问自己的NAS啦！:smiley_cat: 希望你可以看到一样的绿色小图标！

![](/img/nas/nas_ssl_done.jpg)