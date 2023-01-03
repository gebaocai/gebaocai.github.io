---
layout: post
title: 头条_signature逆向分析
author: "gebaocai"
header-style: text
lang: zh
tags: [头条, signature, 逆向]
---

头条访问url参数
------
我们浏览器打开头条页面
```
https://www.toutiao.com/c/user/token/MS4wLjABAAAA0VxYy_b-OIMqWQjBo9g2AIWbQWIf59GaVzYes5bYzY8/?
```
F12进入开发者模式，在NetWork里找到
```
https://www.toutiao.com/api/pc/list/user/feed
```
点开之后可以看到请求的参数
![](/img/in-post/toutiao-signature/feed1.png)

我们向下滑动屏幕触发加载更多操作， 我们再看下请求的参数

![](/img/in-post/toutiao-signature/feed2.png)

可以看到两者变化的只有两个参数:
1. max_behot_time

通过分析得出 `max_behot_time` 是通过上次请求 `/api/pc/list/user/feed` 返回来的

![](/img/in-post/toutiao-signature/feed3.png)

2. _signature

上面两次请求_signature不一样，在Network里也没有发现_signature生成的信息，那么应该是JS生成的了。


_signature参数如何生成
------
既然知道是JS生成的， 那么我们在Sources里搜索`_signature`

![](/img/in-post/toutiao-signature/signature1.png)

我们在`14530`行打上断点，然后再触发加载更多

![](/img/in-post/toutiao-signature/signature2.png)

可以看到`n`就是生成的_signature, 生成方法就是`14530`行的`A(F.getUri(e), e)`方法，我们进入到`A(F.getUri(e), e)`方法里

![](/img/in-post/toutiao-signature/signature3.png)
最终调用的是`a.call(n, o)`, `n`是`window.byted_acrawler`, `a`是`n.sign`, `o`是请求的url, 我们进入到`a.call(n, o)`方法里，发现调用的是一个叫`acrawler.js`的文件，那么这里就是signature生成方法.

模拟signature生成
------
我们知道了signature的生成方法，我们在Console里试一下:
```
var b = window.byted_acrawler;
var a = b.sign;
var o = {url:"https://www.toutiao.com/api/pc/list/user/feed?category=pc_profile_ugc&token=MS4wLjABAAAA0VxYy_b-OIMqWQjBo9g2AIWbQWIf59GaVzYes5bYzY8&max_behot_time=1672642920236&aid=24&app_name=toutiao_web"};
var signature = a.call(b, o);
console.log(signature)
```
输出为:

```_02B4Z6wo00f01UrgjaQAAIDDbDSpRwpvTlVK1IEAADEPqWrhO7vbr4hW27Nfzj0iZoast1HB7ltGjCjYAnpAtndxL7rX4eenX4b7cgudFN8uJv4yHImO2UFfNmP9QTG008DuIyLhQgqw1AGs4f```

我们拼接为url试一下
```
https://www.toutiao.com/api/pc/list/user/feed?category=pc_profile_ugc&token=MS4wLjABAAAA0VxYy_b-OIMqWQjBo9g2AIWbQWIf59GaVzYes5bYzY8&max_behot_time=1672642920236&aid=24&app_name=toutiao_web&_signature=_02B4Z6wo00f01UrgjaQAAIDDbDSpRwpvTlVK1IEAADEPqWrhO7vbr4hW27Nfzj0iZoast1HB7ltGjCjYAnpAtndxL7rX4eenX4b7cgudFN8uJv4yHImO2UFfNmP9QTG008DuIyLhQgqw1AGs4f
```
在postman里执行,截图如下：

![](/img/in-post/toutiao-signature/postman1.png)

数据返回正常，说明signature是正确的.

那么如何通过程序调用生成signature呢？
破解`acrawler.js`里的算法吗？也是个办法吧，但里面涉及到动态加载的东西，难度有点大。 这里我们是通过`nodejs`实现的， 我们在本地跑一个测试一下
![](/img/in-post/toutiao-signature/python1.png)

YES！ 数据也是正常返回的。

部署到服务器
------
我们把上面用到生成signature部署到了服务器, 地址为：

`http://kr.baocai.cf:3000/sign?url=[url]`

url需要做`urlencode`才行, 实例：
```
url="https://www.toutiao.com/api/pc/list/user/feed?category=pc_profile_ugc&token=MS4wLjABAAAA0VxYy_b-OIMqWQjBo9g2AIWbQWIf59GaVzYes5bYzY8&max_behot_time=1672642920236&aid=24&app_name=toutiao_web"
encodeUrl="https%3A%2F%2Fwww.toutiao.com%2Fapi%2Fpc%2Flist%2Fuser%2Ffeed%3Fcategory%3Dpc_profile_ugc%26token%3DMS4wLjABAAAA0VxYy_b-OIMqWQjBo9g2AIWbQWIf59GaVzYes5bYzY8%26max_behot_time%3D1672642920236%26aid%3D24%26app_name%3Dtoutiao_web"
```
**<span style="color: red">传参时是要用encodeUrl, 不要传错了！</span>**

合作交流
------
做技术做了十几年了，积累了些经验，有需要技术服务的可以联系我~ [淘宝](https://item.taobao.com/item.htm?id=692258318480)

