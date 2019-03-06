---
title: 是chrome欺骗了我的眼睛还是我二了？
tags:
  - Javascript
  - JQuery
  - 数组
url: 1757.html
id: 1757
categories:
  - 编程随想
date: 2013-05-03 14:10:12
---

刚接触jQuery那会，一直以为jQuery('....')返回的是数组，如图： ![](http://ww2.sinaimg.cn/large/a74ecc4cjw1e4b1lgvqwaj20k5029mxn.jpg) 谁能在当时告诉我这个刚触及门槛的菜鸟这货不是数组么？chrome控制台输出的明明就是"数组" 后来渐渐发现了jQuery返回的那东西并没有Array的许多方法..自然 如下代码的结果是false

jQuery('body') instanceof Array

那么，jQuery对象究竟是一种怎样的存在呢？ 众里寻他千百度，那货就在灯火阑珊处:

var node = document.getElementsByTagName('span');

这里的node"专业点"来讲是NodeList,即节点列表或集合，说白了就是伪数组。 jQuery返回的也是伪数组,我也找过关于如何创建伪数组的资料,但在chrome浏览器下console的结果却并不是jQuery那样的"数组的样子"; 我纠结了，尝试阅读jQuery的源码 [](http://code.jquery.com/jquery-1.9.1.js)[http://code.jquery.com/jquery-1.9.1.js](http://code.jquery.com/jquery-1.9.1.js) [](http://code.jquery.com/jquery-1.9.1.js)好多地方我暂时看不明白，不要紧，我们精简到只看我们需要的（代码中的注释仅仅只是我自己的见解,如有错误欢迎指正）

(function( window, undefined ) {
    jQuery = function( selector, context ) {
        // 这里解释了为什么我们无需new jQuery()来创建jQuery对象了
        //当然，也说明了我们创建的jQuery对象其实是jQuery.fn.init对象
        return new jQuery.fn.init( selector, context, rootjQuery );
    };
    jQuery.fn = jQuery.prototype = {
    // 上面简化了 jQuery.prototype的写法
        constructor: jQuery,
        init: function( selector, context, rootjQuery ) {
            if ( !selector ) {
                return this;
            }
            //处理各种可能的选择器以及function(){}
            //以一种情况为例(处理直接传入DOM的情况)
            if ( selector.nodeType ) {
                this.context = this\[0\] = selector;
                this.length = 1;
                return this;
            }
        },
        length: 0,
        //.......
    };
    jQuery.fn.init.prototype = jQuery.fn;
    //将jQuery的原型挂到init上，这样我们new的init对象就能直接使用
    //绑定在jQuery原型上的方法了
})( window );

貌似创建的方法和网上流传的诸多方法差不多,进一步简化的情况如下：

var k={0:'a',1:'b',length:2}

那么k就是一个伪数组....多简单明了，我们写个简单的代码来看看chrome下的效果： ![](http://ww4.sinaimg.cn/large/a74eed94jw1e4b2jwt2x4j20fo08gjsn.jpg) 为什么对我创建的对象和jQuery显示结果要区别对待？ 再来瞧瞧Opera下的情况： ![](http://ww3.sinaimg.cn/large/a74e55b4jw1e4b2ln1825j20e007vgmf.jpg) 多明显Object\[.........\]，chrome是欺骗的我的眼睛还是嘲笑了我学习的浅显？ 

> 结语：

如果非要用这么官方的东西来结束这篇吐槽文，我只能说告诫自己：作为一个非专业的前端爱好者，多装几个浏览器多测试是很有必要的（以前调试js代码一直用chrome）。另外，如果打算更进一步的学习，多读专业方面的书籍才是王道，自己的底子太浅了...

### N年后转行做前端更新

其实要让一个对象在chrome控制台看起来像数组，只需要这样：

    var fakeArr = {0:'foo',1:'bar',length:2,splice:function(){}}
    console.log(fakeArr)
    

但是这样，会在数组末尾有一项splice，不是那么完美，要完美就自己造对象：

    function A(){
        this.length = 2
        this[0] = 'foo'
        this[1] = 'bar'
    }
    A.prototype.splice = function(){}
    var fakeArr = new A
    console.log(fakeArr)