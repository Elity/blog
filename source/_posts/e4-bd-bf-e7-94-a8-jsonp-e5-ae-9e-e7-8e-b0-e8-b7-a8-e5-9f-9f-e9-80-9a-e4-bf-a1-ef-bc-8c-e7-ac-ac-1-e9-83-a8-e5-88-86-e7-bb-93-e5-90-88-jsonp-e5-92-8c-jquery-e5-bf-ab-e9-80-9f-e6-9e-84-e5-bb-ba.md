---
title: '使用 JSONP 实现跨域通信，第 1 部分: 结合 JSONP 和 jQuery 快速构建强大的 mashup'
tags:
  - Javascript
  - JSON
  - JSONP
  - 跨域
url: 1605.html
id: 1605
categories:
  - 编程随想
date: 2012-11-15 22:36:26
---

最近想通过手机贴吧页面http://wapp.baidu.com实现在贴吧首页http://tieba.baidu.com点击自动签到

没想到遇到了跨域问题 ....好吧 js小白   没办法  网上找了这篇文章实现跨域 很详细

=====================================================

简介

Asynchronous JavaScript and XML (Ajax) 是驱动新一代 Web 站点（流行术语为 Web 2.0 站点）的关键技术。Ajax 允许在不干扰 Web 应用程序的显示和行为的情况下在后台进行数据检索。使用 XMLHttpRequest 函数获取数据，它是一种 API，允许客户端 JavaScript 通过 HTTP 连接到远程服务器。Ajax 也是许多 mashup 的驱动力，它可将来自多个地方的内容集成为单一 Web 应用程序。

不过，由于受到浏览器的限制，该方法不允许跨域通信。如果尝试从不同的域请求数据，会出现安全错误。如果能控制数据驻留的远程服务器并且每个请求都前往同一域，就可以避免这些安全错误。但是，如果仅停留在自己的服务器上，Web 应用程序还有什么用处呢？如果需要从多个第三方服务器收集数据时，又该怎么办？

理解同源策略限制

同源策略阻止从一个域上加载的脚本获取或操作另一个域上的文档属性。也就是说，受到请求的 URL 的域必须与当前 Web 页面的域相同。这意味着浏览器隔离来自不同源的内容，以防止它们之间的操作。这个浏览器策略很旧，从 Netscape Navigator 2.0 版本开始就存在。

克服该限制的一个相对简单的方法是让 Web 页面向它源自的 Web 服务器请求数据，并且让 Web 服务器像代理一样将请求转发给真正的第三方服务器。尽管该技术获得了普遍使用，但它是不可伸缩的。另一种方式是使用框架要素在当前 Web 页面中创建新区域，并且使用 GET 请求获取任何第三方资源。不过，获取资源后，框架中的内容会受到同源策略的限制。

克服该限制更理想方法是在 Web 页面中插入动态脚本元素，该页面源指向其他域中的服务 URL 并且在自身脚本中获取数据。脚本加载时它开始执行。该方法是可行的，因为同源策略不阻止动态脚本插入，并且将脚本看作是从提供 Web 页面的域上加载的。但如果该脚本尝试从另一个域上加载文档，就不会成功。幸运的是，通过添加 JavaScript Object Notation (JSON) 可以改进该技术。

JSON 和 JSONP

JSON 是用于在浏览器和服务器之间交换信息的轻量级数据格式（与 XML 相比）。JOSON 依赖于 JavaScript 开发人员，因为它是 JavaScript 对象的字符串表示。例如，假设有一个含两个属性的 ticker 对象：symbol 和 price。这是在 JavaScript 中定义 ticker 对象的方式：

var ticker = {symbol: 'IBM', price: 91.42};

并且这是它的 JSON 表示方式：

{symbol: 'IBM', price: 91.42}

从 [参考资料](http://www.ccc5.cc/1605.html#resources) 查找更多有关 JSON 和将其作为数据内部交换格式的信息。清单 1 定义了一个 JavaScript 函数，调用该函数时会显示 IBM 的股价。（我们没有详细介绍如何将该函数添加到 Web 页面）。

  
**清单 1\. 定义 showPrice 函数**

function showPrice(data) {
    alert("Symbol: " + data.symbol + ", Price: " + data.price);
}

可以将 JSON 数据作为参数传递，以调用该函数：

showPrice({symbol: 'IBM', price: 91.42}); // alerts: Symbol: IBM, Price: 91.42

现在准备将这两个步骤包含到 Web 页面，如清单 2 所示。

  
**清单 2\. 在 Web 页面中包含 showPrice 函数和参数**

<script type="text/javascript">
function showPrice(data) {
    alert("Symbol: " + data.symbol + ", Price: " + data.price);
}
</script>
<script type="text/javascript">showPrice({symbol: 'IBM', price: 91.42});</script>

加载页面后，应该看如图 1 所示的警告。

  
**图 1\. IBM ticker**  
![IBM ticker](http://www.ibm.com/developerworks/cn/web/wa-aj-jsonp1/ticker.jpg)  

至此，本文已展示了如何将静态 JSON 数据作为参数调用 JavaScript 函数。不过，通过在函数调用中动态包装 JSON 数据可以用动态数据调用函数，这是一种动态 JavaScript 插入的技术。要查看其效果，将下面一行放入名为 ticker.js 的独立 JavaScript 文件中。

showPrice({symbol: 'IBM', price: 91.42});

  

现在改变 Web 页面中的脚本，使其和清单 3 一样。

  
**清单 3\. 动态 JavaScript 插入代码**

<script type="text/javascript">
// This is our function to be called with JSON data
function showPrice(data) {
    alert("Symbol: " + data.symbol + ", Price: " + data.price);
}
var url = “ticker.js”; // URL of the external script
// this shows dynamic script insertion
var script = document.createElement('script');
script.setAttribute('src', url);
           
// load the script
document.getElementsByTagName('head')\[0\].appendChild(script); 
</script>

在清单 3 所示的例子中，动态插入的 JavaScript 代码位于 ticker.js 文件中，它将真正的 JSON 数据作为参数调用 showPrice()函数。

前面已经提到，同源策略不阻止将动态脚本元素插入文档中。也就是说，可以动态插入来自不同域的 JavaScript，并且这些域都携带 JSON 数据。这其实是真正的 JSONP（JSON with Padding）：打包在函数调用中的 JSON 数据。注意，为了完成该操作，Web 页面必须在插入时具有已经定义好的回调函数，也就是我们例子中的 showPrice()。

不过，所谓的 JSONP 服务（或 Remote JSON Service）是一种带有附加功能的 Web 服务，该功能支持在特定于用户的函数调用中打包返回的 JSON 数据。这种方法依赖于接受回调函数名作为请求参数的远程服务。然后该服务生成对该函数的调用，将 JSON 数据作为参数传递，在到达客户端时将其插入 Web 页面并开始执行。

jQuery 的 JSONP 支持

从 1.2 版本开始，jQuery 拥有对 JSONP 回调的本地支持。如果指定了 JSONP 回调，就可以加载位于另一个域的 JSON 数据，回调的语法为：url?callback=?。

jQuery 自动将 **?** 替换为要调用的生成函数名。清单 4 显示了该代码。

  
**清单 4\. 使用 JSONP 回调**

jQuery.getJSON(url+"&callback=?", function(data) {
    alert("Symbol: " + data.symbol + ", Price: " + data.price);
});

为此，jQuery 将一个全局函数附加到插入脚本时需要调用的窗口对象。另外，jQuery 也能优化非跨域调用。如果向同一个域发出请求，jQuery 就将其转化为普通 Ajax 请求。

使用 JSONP 支持的示例服务

在上一个例子中，使用了静态文件（ticker.js）将 JavaScript 动态插入到 Web 页面中。尽管返回了 JSONP 回复，但它不允许您在 URL 中定义回调函数名。这不是 JSONP 服务。因此，如何才能将其转换为真正的 JSONP 服务呢？可使用的方法很多。这里我们将分别使用 PHP 和 Java 展示两个示例。

首先，假设您的服务在所请求的 URL 中接受了一个名为 callback 的参数。（参数名不重要，但是客户和服务器必须都同意该名称）。另外假设向服务发送的请求是这样的：

> http://www.yourdomain.com/jsonp/ticker?symbol=IBM&callback=showPrice

在这种情况下，symbol 是表示请求 ticker symbol 的请求参数，而 callback 是 Web 应用程序的回调函数的名称。使用清单 5 所示的代码可以通过 jQuery 的 JSONP 支持调用该服务。

  
**清单 5\. 调用回调服务**

jQuery.getJSON("http://www.yourdomain.com/jsonp/ticker?symbol=IBM&callback=?", 
function(data) {
    alert("Symbol: " + data.symbol + ", Price: " + data.price);
});

注意，我们使用 ? 作为回调函数名，而非真实的函数名。因为 jQuery 会用生成的函数名替换 ?。所以您不用定义类似于 showPrice()的函数。

清单 6 显示了用 PHP 实现的 JSONP 服务的一段代码。

  
**清单 6\. 用 PHP 实现的 JSONP 服务的代码片段**

$jsonData = getDataAsJson($_GET\['symbol'\]);
echo $_GET\['callback'\] . '(' . $jsonData . ');';
// prints: jsonp1232617941775({"symbol" : "IBM", "price" : "91.42"});

清单 7 显示了具有同样功能的 Java™ Servlet 方法。

  
**清单 7\. 用 Java servlet 实现的 JSONP 服务**

@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
  throws ServletException, IOException {
    String jsonData = getDataAsJson(req.getParameter("symbol"));
    String output = req.getParameter("callback") + "(" + jsonData + ");";
          
    resp.setContentType("text/javascript");
                    
    PrintWriter out = resp.getWriter();
    out.println(output);
    // prints: jsonp1232617941775({"symbol" : "IBM", "price" : "91.42"});
}

那么，如果要构建 mashup 应该怎么办，是从第三方服务器收集内容，并在单一的 Web 页面中显示它们吗？答案很简单：您必须使用第三方 JSONP 服务。这种服务并不少。

现成的 JSONP 服务

知道如何使用 JSONP 之后，可以开始使用一些现成的 JSONP Web 服务来构建应用程序和 mashup。下面为接下来的开发项目做准备。（提示：您可以复制特定的 URL 并将其粘贴到浏览器的地址栏，以检查生成的 JSONP 响应）。

Digg API：来自 Digg 的头条新闻：

> http://services.digg.com/stories/top?appkey=http%3A%2F%2Fmashup.com&type=javascript
> &callback=?

Geonames API：邮编的位置信息：

> http://www.geonames.org/postalCodeLookupJSON?postalcode=10504&country=US&callback=?

Flickr API：来自 Flickr 的最新猫图片：

> http://api.flickr.com/services/feeds/photos_public.gne?tags=cat&tagmode=any
> &format=json&jsoncallback=?

Yahoo Local Search API：在邮编为 10504 的地区搜索比萨：

> http://local.yahooapis.com/LocalSearchService/V3/localSearch?appid=YahooDemo&query=pizza
> &zip=10504&results=2&output=json&callback=?

[回页首](http://www.ibm.com/developerworks/cn/web/wa-aj-jsonp1/#ibm-pcon)

重要提示

JSONP 是构建 mashup 的强大技术，但不幸的是，它并不是所有跨域通信需求的万灵药。它有一些缺陷，在提交开发资源之前必须认真考虑它们。第一，也是最重要的一点，没有关于 JSONP 调用的错误处理。如果动态脚本插入有效，就执行调用；如果无效，就静默失败。失败是没有任何提示的。例如，不能从服务器捕捉到 404 错误，也不能取消或重新开始请求。不过，等待一段时间还没有响应的话，就不用理它了。（未来的 jQuery 版本可能有终止 JSONP 请求的特性）。

JSONP 的另一个主要缺陷是被不信任的服务使用时会很危险。因为 JSONP 服务返回打包在函数调用中的 JSON 响应，而函数调用是由浏览器执行的，这使宿主 Web 应用程序更容易受到各类攻击。如果打算使用 JSONP 服务，了解它能造成的威胁非常重要。（参见 [参考资料](http://www.ibm.com/developerworks/cn/web/wa-aj-jsonp1/#resources) 了解更多信息）。

[回页首](http://www.ibm.com/developerworks/cn/web/wa-aj-jsonp1/#ibm-pcon)

结束语

在该系列的第一篇文章中，我们讲解了如何结合使用 JSONP 和 jQuery 快速构建强大的 mashup。主要主题包括：

*   浏览器同源策略的限制以及解决办法
    
*   作为一种有效的跨域通信技术，JSONP 能够绕过当前浏览器的同源策略限制
    
*   JSONP 使 Web 应用程序开发人员能够快速构建 mashup
    
*   示例 JSONP 服务及其使用：Ticker 服务
    

本系列的下一篇文章将介绍 Yahoo! 查询语言（YQL），这种单端点 JSONP 服务允许您跨 Web 查询、过滤和合并数据。最后还使用 YQL 和 jQuery 构建 mashup 应用程序。

  

参考资料

*   了解 [JSON](http://json.org/)，它是针对 Java 和其他语言的 JavaScript Object Notation API。
    
*   阅读 “[掌握 Ajax，第 10 部分: 使用 JSON 进行数据传输](https://www.ibm.com/developerworks/cn/web/wa-ajaxintro10/)”，并了解在 Ajax 应用程序中使用 JSON 作为数据交换格式。
    
*   通过包含三部分的系列文章 “[使用 jQuery](https://www.ibm.com/developerworks/cn/web/wa-jquery1/)”了解 jQuery。
    
*   [Browser Security Handbook](http://code.google.com/p/browsersec/wiki/Part2#Same-origin_policy) 提供关于同源策略的详细描述。
    
*   “[征服 Ajax 应用程序的安全威胁](https://www.ibm.com/developerworks/cn/xml/x-ajaxsecurity.html)”为确保 mashup 应用程序的安全提供技巧和最佳实践。
    
*   “[打造安全 Ajax mashup 的未来](https://www.ibm.com/developerworks/cn/xml/x-securemashups/)”讲解了如何为混合 Web 应用程序改进浏览器。
    
*   浏览 [技术书店](http://www.ibm.com/developerworks/apps/SendTo?bookstore=safari)，阅读有关这些主题和其他技术主题的图书。
    

[via](http://www.ibm.com/developerworks/cn/web/wa-aj-jsonp1 "文章出处")