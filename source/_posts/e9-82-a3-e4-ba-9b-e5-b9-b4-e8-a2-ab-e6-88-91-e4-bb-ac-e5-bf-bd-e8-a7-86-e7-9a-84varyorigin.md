---
title: '那些年被我们忽视的Vary:Origin'
tags:
  - cors
  - http
  - http协议
  - Origin
  - Vary
url: 2375.html
id: 2375
comments: false
categories:
  - 编程随想
date: 2018-09-28 15:28:41
---

之前刷知乎有看到[一篇文章](https://zhuanlan.zhihu.com/p/38972475?utm_source=qq&utm_medium=social&utm_oi=26757239406592 "一篇文章")，讲Vary头未配置引起资源缓存错乱的问题，当时只是大概瞄了下，对Vary这个头有了点印象，便没有继续深究。

今天同事碰到一个奇怪的bug，由于chrome65开始禁止通过a标签设置download属性下载跨域资源，官方说明在这里：[Block cross-origin <a download>](https://developers.google.com/web/updates/2018/02/chrome-65-deprecations#chrome_no_longer_trusting_certain_symantec_certificates)，同事打算先用xhr读取图片，然后转成base64塞到a标签实现下载。
	
然鹅，理想很丰满，现实挺骨干，实现的时候，在服务端配置Access-Control-Allow-Origin:*后，浏览器端发送xhr并没有得到该响应头，然后浏览器报跨域错误了。
	
实验发现，我们把该图片单独拧出来在这个浏览器上通过fetch请求依然报错，当换个电脑或者浏览器通过fetch请求时并不会报错，看来是浏览器缓存了什么。

于是乎，尝试了在原来的浏览器上通过在图片地址后面加随机参数，然后fetch，绕过了缓存果然不会有跨域错误。

那么，第一次的不能跨域的缓存是哪来的呢？

通过了解业务流程发现，上传完图片后会有一次预览的过程，而这个预览的过程是通过img标签加载的，服务器并不会返回Access-Control-Allow-Origin:*，而浏览器就缓存了该url及其响应header，下次，就算想通过跨域的方式访问，而浏览器则不会再去问服务器，直接从缓存里面取header告诉其不能跨域，这实际违背了服务器的意愿。

这是一种情况，服务端缓存了没有跨域信息的头。前面的文章里面还提到另一种情况：

某资源允许a.com与b.com跨域访问，用户在a.com域名下访问该资源后，浏览器会缓存Access-Control-Allow-Origin:a.com，下次，用户再在b.com域名下访问，浏览器直接从缓存拿到的也是允许a.com跨域，从而报跨域错误。

要解决这个缓存错乱的问题就需要配置我们标题中提到的[Vary](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Vary)了。

未配置Vary之前，浏览器的缓存以url为唯一区分，配置Vary后，浏览器会以二者结合作为区分，刚好是我们所需的。

Vary其它取值情况看文档，不具体说明，聊下可以解决问题的配置，Vary:Origin。

[Origin](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Origin)是个有意思的header，（PS：主要是我一直不了解它）。仅在CORS请求与POST请求下才会携带该请求头，也就是说，我们直接通过img标签加载的请求并不会有Origin，而后通过xhr请求，同样的url，由于属于CORS请求，所以会带有Origin，浏览器会将其当成两种不同的资源。前面提到的另一种情况，从不同域名请求同一个资源也是同样的道理。

问题迎刃而解。

不过针对我们项目碰到的问题，要是使用阿里云OSS，高兴为时过早了，因为阿里云后台仅支持配置9种可选的响应header，并不包含Vary。 (┬＿┬)

不过，既然发现了问题根源，解决问题就从根源着手。

既然是第一次通过img加载图片造成的缓存“污染”，那么，我们让img加载图片的方式走CORS不就好了么？简直太机智了。。[img crossorigin](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img#attr-crossorigin)

```html
&lt;img src=&quot;xxxx&quot; crossorigin /&gt;
```
