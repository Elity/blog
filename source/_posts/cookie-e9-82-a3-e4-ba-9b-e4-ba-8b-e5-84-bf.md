---
title: Cookie那些事儿
tags:
  - cookie
url: 2358.html
id: 2358
comments: false
categories:
  - 编程随想
date: 2018-10-08 16:22:55
---

面试别人的时候，经常碰到自诩精通HTTP协议的人，我一般会丢个问题“HTTP协议是否有状态？”，来试探是否真的“了解”HTTP协议，很多人听到问题就懵逼了，这TM还是我认识的HTTP协议么。偶尔碰到一个说是无状态的，我继续追问“既然HTTP是无状态的，那么我打开一个网站，然后刷新一下，服务器能否知道这次的访问者和上次的访问者是否是同一个客户端？”，然后继续懵逼，转而把答案改为“那应该是有状态的吧？” 所以啊，我们很多时候学技术性东西，还是要追求甚解，知其然，知其所以然。 HTTP协议当然是无状态的，我们在实际场景中是如何区分客户的呢？传统的做法就是利用Cookie来标记用户，用户第一次访问服务器，服务器便在Response Header中添加Set-Cookie，写入标记该用户的唯一标识符，用户二次访问时，将该标识符带回，服务器即可区分该用户是否是之前的用户了。 说到我们本文的主角Cookie，当然不是那么简简单单的事情（事实上是我自己一直以来将其看得过于简单，后来脸被打肿）。

### 两种方式设置cookie：

*   Set-Cookie(http方式) 服务端通过在Response Header中添加Set-Cookie，告知客户端如何存储cookie，一般格式：

    Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>;Max-Age=<non-zero-digit>;Expires=<date>;Path=<path-value>;SameSite=<Strict|Lax>; Secure; HttpOnly
    

*   document.cookie(代码方式) 客户端（通常指浏览器）通过javascript直接给document.cookie赋值设置cookie

    document.cookie="<cookie-name>=<cookie-value>; Domain=<domain-value>;Max-Age=<non-zero-digit>;Expires=<date>;Path=<path-value>;SameSite=<Strict|Lax>; Secure;"
    

很明显，除了客户端无法设置HttpOnly外，其他与服务端基本一致。 另外，需要注意的是，书写顺序问题：`<cookie-name>=<cookie-value>;`必须写在最前面，后面的其他项顺序则可以随意了。这样也就限制了一句设置代码只能设置一个cookie，服务端需要在一次http响应中添加多个set-cookie header来一次性写入多个cookie，客户端则需要调用多次document.cookie赋值才能设置多个cookie。

### cookie-name=cookie-value

根据[RFC6265](http://www.ietf.org/rfc/rfc6265.txt),二者可取值情况如下： \- cookie-name 区分大小写，字母、数字以及!#$%&'*+-.^_|`~。 - cookie-value 是可选的。支持字母、数字及!#$%&'()*+-./:<=>?@\[\]^_{|}`~。关于编码：许多应用会对 cookie 值按照URL编码（URL encoding）规则进行编码，但是按照 RFC 规范，这不是必须的。不过满足规范中对于 `cookie-value` 所允许使用的字符的要求是有用的。 尽管目前实验了Chrome及Firefox支持更多的字符串，甚至中文，但由于各浏览器、各服务端解析可能存在不一致的情况，还是推荐按标准形式，可以减少古怪问题出现的概率。

*   \_\_Secure-前缀，以 \_\_Secure- 为前缀的 cookie（其中连接符是前缀的一部分），必须与 secure 属性一同设置，同时必须应用于安全页面（即使用 HTTPS 访问的页面）。
*   \_\_Host-前缀，以 \_\_Host- 为前缀的 cookie，必须与 secure 属性一同设置，必须应用于安全页面（即使用 HTTPS 访问的页面），必须不能设置 domain 属性 （也就不会发送给子域），同时 path 属性的值必须为“/”。

以`__Secure-`前缀为例讲解其如何弥补后面即将提到的`Secure`的不足之处： 由于发往服务端的Cookie只有key-value对，并不包含domain、secure之类的其它信息。当我们用`token`作为用户标识时，设置domain为`www.a.com`cookie，并带上了`Secure`设置，尽管`www.a.com`为https，但攻击者（中间人）完全可以构造一个非https的网站`xxx.a.com`诱导用户访问（被劫持的网站，不需要真实存在），然后设置domain为`.a.com`的cookie，当服务端拿到这两个token时并没法分别哪个是安全的token。 但是我们若不以`token`作为cookie name，而是使用`__Secure-token`，攻击者就无计可施了，因为设置`__Secure-`前缀的cookie必须同时设置`Secure`，而`Secure`的设置只能在https链接中，攻击者又无法通过中间人的方式攻击https。

### Domain=domain-value

用于设置cookie生效的范围。若不设置该项，浏览器默认将其标记为HostOnly，也就是只有在完全匹配的hostname（注意是`hostname`，而不是`host`，也就是说cookie的domain**不区分端口**）下面方可读取cookie。比如，我在`a.com`下面设置cookie时没有指定domain，那么我在`sub.a.com`下面就无法读取到该cookie。 若设置domain，domain的取值只能是当前hostname或父级域，比如，在`sub.a.com`下面，可选的domain只能是`sub.a.com`或者`a.com`，其它取值将会被浏览器忽略。 另外，非常值得注意的是，一些顶级域本来就有两段或多段，比如:`.com.cn`、`.usa.gov`、`.edu.cn`等等，我们就无法在形如`a.com.cn`的网站中设置domain为`.com.cn`的cookie。 除了上面提到的多段顶级域名，一些提供网站服务的第三方平台，比如github.io、sina sae等，提供子域名给各个用户，也就是说`w3c.github.io`与`alibaba.github.io`不是同一个网站，那么在`w3c.github.io`下就不能写domain为`.github.io`的cookie。 由于上面提到的两种情况存在，Mozilla很早之前就为此建立了一个列表[Public Suffix List](https://publicsuffix.org/list/public_suffix_list.dat)，专门用于维护顶级域名及类似于`github.io`这样的第三方网站提供商的域名。据[Public Suffix List官网](https://publicsuffix.org/learn/)介绍，目前使用该列表的软件包括Firefox、Chrome、IE等主流浏览器及其他一些对域名查询有需求的软件。 如果希望自己的网站添加进该列表，可以来[这里](https://github.com/publicsuffix/list)提PR（被通过应该需要很多手续）。

### Path=path-value

指定一个 URL 路径，这个路径必须出现在要请求的资源的路径中才可以发送 Cookie 首部。字符 %x2F ("/") 可以解释为文件目录分隔符，此目录的下级目录也满足匹配的条件（例如，如果 path=/docs，那么 "/docs", "/docs/Web/" 或者 "/docs/Web/HTTP" 都满足匹配的条件）。 另外，由于domain与path是分开解析的，所以`a=1;domain=.a.com;path=/x/y`,也能在`sub.a.com/x/y`下被读取。

### Expires=date

date应该是符合`<day-name>, <day> <month> <year> <hour>:<minute>:<second> GMT`格式的字符串，用于标识cookie的过期时间。若不设置该项（下面的max-age也不设置）则cookie是会话级的，浏览器关闭则清除该cookie。

### Max-Age=non-zero-digit

non-zero-digit是只在 cookie 失效之前需要经过的秒数。一位或多位非零（1-9）数字（ps:其实0也可以，只是没意义，浏览器可以实现为直接忽略）。一些老的浏览器（ie6、ie7 和 ie8）不支持这个属性。对于其他浏览器来说，假如二者 （指 Expires 和Max-Age） 均存在，那么 Max-Age 优先级更高。

### SameSite=Strict|Lax|Unset(默认)

允许服务器设定一则 cookie 不随着跨域请求一起发送，这样可以在一定程度上防范跨站请求伪造攻击（CSRF）。 - Strict 任何时候都不跨域发送cookie，所以当你发现从a网站进入b网站，b网站的登陆状态总是失效的，然后刷新一下又正常了，不要悲伤、不要诧异，检查下你用于标记登陆状态的cookie是不是设置了samesite为strict - Lax 点击a标签、form的get请求、通过location改变、通过window.open方式打开时会携带cookie，而ajax、script、link、img、iframe、form的post发起的跨域请求则不会携带cookie - Unset 默认值，任何时候都会跨域携带cookie `samesite`目前属于实验性属性，还未进入标准，目前(2018.10.08)兼容性不是太乐观[can i use](https://caniuse.com/#search=samesite)

### Secure

一个带有安全属性的 cookie 只有在请求使用SSL和HTTPS协议的时候才会被发送到服务器，同时无法在非https的页面通过document.cookie读取，这可以有效防范[SSl strip](https://github.com/Elity/sslstrip-nodejs)后cookie失窃。然而，保密或敏感信息永远不要在 HTTP cookie 中存储或传输，因为整个机制从本质上来说都是不安全的，比如前述协议并不意味着所有的信息都是经过加密的。 新版Chrome与Firefox已经`不支持`在非https的链接中设置Secure了。

### HttpOnly

带此标识的cookie只能以http的方式设置于读取，也就是说，通过document.cookie的方式既不能写入也不能读取带HttpOnly标识的cookie，可有效防止xss的方式窃取cookie。事实上，一般后端web框架的sessionid一般都是设置了HttpOnly的。

### 第三方Cookie

跨域设置cookie的问题也顺带提一下，我已经在此处跌倒几次没长记性了： 首先现代浏览器，在`a.com`引入`b.com`的资源（link、img、script等），是可以正常写入与携带cookie的（ps:老版本的IE需要配置[P3P](https://www.w3.org/P3P/))，现在的广告追踪就是利用这个功能。 在`a.com`下面通过ajax请求`b.com`，由于某些不可描述的原因，`b.com`会通过http的方式向浏览器写入cookie，未做任何处理的情况下，这种方式并不会正常写入cookie，而需要通过浏览器与服务端双方友好协商，你情我愿的情况下方可成功，流程就是：浏览器端发请求的时候告诉服务端，请同意我跨域带cookie给你，我也同意你跨域向我写cookie，如果这时候服务端应答：我愿意。 那么他们就愉快的牵手永远幸福的生活下去了。。 偏了偏了，差点写成言情小说了。 浏览器通过fetch请求时配置`{credentials:'include'}`，通过XMLHttpRequest请求时配置`xhr.withCredentials`为`true`，然后服务端Response Header里面添加 `Access-Control-Allow-Credentials: true`就OK了。 当然这也还要注意，在配置CORS相关的Response Header时，若`Access-Control-Allow-Credentials`的值为`true`，`Access-Control-Allow-Origin`则不能设置为`*`，一来不安全，二来浏览器会报错。

### 参考链接：

*   [http://www.ietf.org/rfc/rfc6265.txt](http://www.ietf.org/rfc/rfc6265.txt)
*   [https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)
*   [https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie)
*   [https://www.cnblogs.com/ziyunfei/p/5637945.html](https://www.cnblogs.com/ziyunfei/p/5637945.html)