---
title: Vue中loading状态管理
tags:
  - vue
url: 2230.html
id: 2230
comments: false
categories:
  - 编程随想
date: 2018-05-12 21:48:34
---

为了提升用户体验，我们一般会在做耗时操作时显示loading效果，缓解用户等待的焦虑.通常情况下，我们的代码可能是这样子：
```html
&lt;div v-show=&quot;isLoading&quot;&gt;
   loading...
&lt;/div&gt;
```
```javascript
new Vue({
  data:{
    isLoading:false
  },
  methods:{
    async fetchFn(){
        this.isLoading = true
        try{
          await getSomething()
        }catch(e){
          //
        }finally{
          this.isLoading = false
        }
    }
  }
})
```
这样，我们在调用`fetchFn`时，即可显示loading效果了。
这种实践不是不可以，只是每次都需要要用户手动的、命令式的来维护loading的状态，很容易出现漏写的情况，虽然通过事后的测试可能发现问题，但总归是麻烦的。
我们能否想到一种自我状态管理的情况，给每个异步的method都生成一个loading状态，然后在需要用到loading的地方，在视图里面订阅指定method的loading变化即可。
我们通过Vue插件来实现该功能：
```javascript
var VueLoadingPlugin = {};
VueLoadingPlugin.install = (Vue,loadingNameSpace=&#039;tzLoading&#039;) =&gt; {
  Vue.mixin({
    data() {
      return {
        [loadingNameSpace]: {},
      };
    },
    beforeCreate() {
      const methods = this.$options.methods || {};
      Object.keys(methods).forEach(key =&gt; {
        let fn = methods[key];
        this.$options.methods[key] = function(...args) {
          this.$set(this[loadingNameSpace], key, true);
          let ret = fn.apply(this, args);
          if (ret &amp;&amp; typeof ret.catch === &quot;function&quot;) {
            return ret.finally(() =&gt; (this[loadingNameSpace][key] = false));
          } else {
            this[loadingNameSpace][key] = false;
            return ret;
          }
        };
      });
    },
  });
};
```
这样，当我们有如下代码时：
```javascript
new Vue({
  methods:{
    async fetchFn(){
      await getSomething()
    }
  }
})
```
只需要在模版里面适当的地方订阅`fetchFn`的loading状态，然后，在每次fetchFn开始时都会自动出现加载效果，然后在fetchFn正常结束或出错后关闭加载效果：
```html
&lt;div v-show=&quot;tzLoading.fetchFn&quot;&gt;
loading...
&lt;/div&gt;
```