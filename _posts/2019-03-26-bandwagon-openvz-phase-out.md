---
layout: post
title: 搬瓦工 OpenVZ 无法续费
author: "gebaocai"
header-style: text
lang: zh
tags:
  - 搬瓦工
  - OpenVZ
---
# 搬瓦工 OpenVZ 无法续费常见问题解答
1、搬瓦工是否会免费升级现有的 OpenVZ 到 KVM？

不会。主要原因有两个：

a) 从技术角度来看，不可能直接将基于 OpenVZ 的 VPS 转换为KVM，因为两种虚拟化技术的运行方式都不同，它们不兼容。

b) KVM 技术的运营成本远高于 OpenVZ，因为它需要更强大的硬件。 因此，我们无法提供对应的 KVM 方案来匹配最低价的 OpenVZ 方案。

2、是否会提供 OpenVZ 迁移到 KVM 的一键工具？

不会。原因如上面的 a 所述，数据迁移的唯一方法就是手动把数据从 OpenVZ 的 VPS 中导出，再导入到 KVM 方案的 VPS 里面。

此外，搬瓦工已经把所有的 OpenVZ VPS 方案免费延长了两周时间，方便大家进行数据迁移。

3、是否会有更低价的 KVM 方案推出来匹配低价的 OpenVZ 方案？

不会，原因如上面的 b 所述。KVM 方案的成本更高，不会推出比现有 KVM 方案更低价的 KVM 方案。

4、目前的 OpenVZ 方案还能用多久？

原有的方案到期日期的基础上，再加两个星期的使用时间。比如你 10 月份刚续费了，那你可以用到明年的 10 月份 + 2 个星期。所以不用担心，并不是立马就取消了。但是在到期之后就不能续费了。

5、迁移到 KVM 方案，还需要我自己买一台 KVM 的 VPS？

是的。搬瓦工不会免费升级，也不会免费提供，只能自己重新买一台。

目前 KVM 方案最便宜的是年付 $18 美元的，参考：搬瓦工 VPS KVM 常规版（KVM PROMO）便宜方案 / 教程汇总，当然，也可以购买 CN2、CN2 GIA 或者香港方案，这些方案都是基于 KVM 架构的。

6、KVM 比 OpenVZ 的优势在哪？

优势可以简单总结如下：

a) KVM 比 OpenVZ 的资源更独立，更加不容易超售。

b) KVM 有自带 BBR 的系统可选，对于新手用户很友好。

c) KVM 的自定义性更高，对于想学习 Linux 的朋友来说可操作空间更大。

d) KVM 一般来说都是比 OpenVZ 贵的（因为可超售的余地更小），但是搬瓦工的 KVM 和 OpenVZ 价格一样，所以明显更具有性价比。

7、基于 KVM 的方案包括哪些？

目前所有在售方案均基于 KVM 架构，包括常规 KVM 方案、CN2 方案、CN2 GIA 方案、香港方案。
# 搬瓦工目前可购买的方案

1、20G KVM VPS 

*CPU: 2x Intel Xeon

*内存: 1024MB

*硬盘: 20 GB SSD

*流量: 1TB/月

*价格: $49.99/年

[购买地址>>](https://bandwagonhost.com/aff.php?aff=14283 "购买地址")

2、40G KVM VPS 

*CPU: 3x Intel Xeon

*内存: 2 GB

*硬盘: 40 GB SSD

*流量: 2TB/月

*价格: $52.99/年

[购买地址>>](https://bandwagonhost.com/aff.php?aff=14283 "购买地址")

3、 

[更多配置>>](https://bandwagonhost.com/aff.php?aff=14283 "更多配置")

# 搬瓦工优惠码
以上折扣后的价格是我们可以使用<span style="color:red;">BWH26FXH3HIQ</span>优惠码再节省6.25%。

