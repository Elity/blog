---
title: Vue下优雅实现事件频率控制
tags:
  - debounce
  - vue
url: 2164.html
id: 2164
comments: false
categories:
  - 编程随想
date: 2017-09-04 09:26:18
---

> 该方式在vue2.4以后不再可用，之后的版本，在绑定事件的时候，内部给函数有包装了一层，我们无法在指令内部移除事件监听。源码见 [->->->这里](https://github.com/vuejs/vue/blob/c3553196b8b15a71f982bd5e04c61be52e87c828/src/platforms/web/runtime/modules/events.js#L48)


在web开发中，对DOM事件做频率限制随处可见，特别是涉及网络请求。这样的需求下，我们希望事件在第一次触发后立即响应,然后不接受响应，接下来在特定的时间内没再次收到该事件，则解除该限制。

借鉴`debounce`与`throttle`的思路，我们第一次可能想到的是自己定义一个频率控制的高阶函数(比如`oneTime`)，接受我们传入的事件响应函数作为参数，得到一个新的函数传入`@click`里面，就像：

```html
&lt;button @click=&quot;oneTime(myFn)&gt;测试&lt;/button&gt;
```

经过实验，发现这样不行。查看vue.js[源码](https://github.com/vuejs/vue/blob/91deb4fd910afd51137820f120d4c26d0a99e629/src/core/vdom/helpers/update-listeners.js#L26)：

```javascript
export function createFnInvoker (fns: Function | Array&lt;Function&gt;): Function {
  function invoker () {
    const fns = invoker.fns
    if (Array.isArray(fns)) {
      const cloned = fns.slice()
      for (let i = 0; i &lt; cloned.length; i++) {
        cloned[i].apply(null, arguments)
      }
    } else {
      // return handler return value for single handlers
      return fns.apply(null, arguments)
    }
  }
  invoker.fns = fns
  return invoker
}
```

可发现，我们在模版里面传入的部分，也就是函数中的`fns`，包裹在`invoker`中每次都会被执行，也就是绑定的过程每次点击都会进行。所以，这样行不通。
那么，我们只能在data里面单独定义一个变量来保存生成的函数，然后把这个变量塞入`@click`里面。这样就导致每次需要使用该特性都得单独定义一个变量来保存新的函数，更优雅地实现方式是咋样的呢？我首先想到的是指令，想象下，在需要控制频率的地方使用自定义的指令，传入需要被控制的事件及时间，一切都和谐多了。附上指令实现方式：

```javascript
function oneTime(method, duration) {
	let timer = null,running = false;
	return function (...args) {
		clearTimeout(timer);
		timer = setTimeout(()=&gt;{
			running = false
		}, duration);
		if(running)return;
		running = true;
		method.apply(this, args);
	}
}
	
Vue.directive(&#039;freq&#039;, { 
	inserted(el,bd,vnode) { 
			console.log(&#039;here&#039;,vnode.data.on)
		let all = {};
		if(typeof bd.value === &#039;string&#039;){
			all[bd.value] = 500
		}else if(Array.isArray(bd.value)){
			bd.value.forEach(val=&gt;all[val] = 500)
		}else{
			all = bd.value
		}
		for(let ev in all){
			if(!vnode.data.on[ev])continue;
			el.removeEventListener(ev,vnode.data.on[ev]);
			vnode.data.on[ev] = oneTime(vnode.data.on[ev],all[ev])
			el.addEventListener(ev,vnode.data.on[ev])
		}
	}
})
```

然后我们就可以这样使用了：

```html
&lt;button @click=&quot;fn&quot; v-freq=&quot;{click:500}&quot;&gt;
    测试
&lt;/button&gt;
```

或者这样：

```html
&lt;button @click=&quot;fn&quot; v-freq=&quot;&#039;click&#039;&quot;&gt;
    测试
&lt;/button&gt;
```

甚至这样：

```html
&lt;button @click=&quot;fn&quot; v-freq=&quot;[&#039;click&#039;,&#039;mouseenter&#039;]&quot;&gt;
    测试
&lt;/button&gt;
```
