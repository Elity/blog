---
title: jest + vue-test-utils为vue项目写单元测试
tags:
  - vue
  - 单元测试
url: 2309.html
id: 2309
comments: false
categories:
  - 编程随想
date: 2018-07-30 22:10:09
---

1.安装依赖库
```bash
npm i -D @vue/test-utils babel-jest  jest  jest-serializer-vue  vue-jest
```
2.配置jest (package.json)
```json
{	&quot;scripts&quot;:{
		&quot;test&quot;: &quot;jest&quot;
	},
	&quot;jest&quot;: {
    &quot;testURL&quot;: &quot;http://localhost&quot;,
    &quot;moduleFileExtensions&quot;: [
      &quot;js&quot;,
      &quot;vue&quot;
    ],
    &quot;moduleNameMapper&quot;: {
      &quot;^@/(.*)$&quot;: &quot;&lt;rootDir&gt;/src/$1&quot;
    },
    &quot;transform&quot;: {
      &quot;^.+\\.js$&quot;: &quot;&lt;rootDir&gt;/node_modules/babel-jest&quot;,
      &quot;.*\\.(vue)$&quot;: &quot;&lt;rootDir&gt;/node_modules/vue-jest&quot;
    },
    &quot;snapshotSerializers&quot;: [
      &quot;&lt;rootDir&gt;/node_modules/jest-serializer-vue&quot;
    ]
  }
}

```
3.配置Babel (.babelrc)

```json
{
	&quot;env&quot;: {
    &quot;test&quot;: {
      &quot;presets&quot;: [[&quot;env&quot;, { &quot;targets&quot;: { &quot;node&quot;: &quot;current&quot; } }]]
    }
  }
}
```
4.项目根目录新建test文件夹
添加测试文件 `test.spec.js`
```javascript
import { shallowMount } from &#039;@vue/test-utils&#039;;
import ContentLength from &#039;@/components/ContentLength&#039;;

describe(&#039;Component.ContentLength&#039;, () =&gt; {
  const wrapper = shallowMount(ContentLength, {
    propsData: {
      content: &#039;Hello&#039;,
      maxLength: 10,
    },
  });

  it(&#039;传值正确&#039;, () =&gt; {
    expect(wrapper.vm.content).toBe(&#039;Hello&#039;);
    expect(wrapper.vm.maxLength).toBe(10);
  });

  it(&#039;未超过长度渲染正确&#039;, () =&gt; {
    expect(wrapper.find(&#039;.exceed&#039;).exists()).toBe(false);
  });

  it(&#039;超过长度渲染正确&#039;, () =&gt; {
    wrapper.setProps({
      content: &#039;Hello World!&#039;,
    });
    expect(wrapper.find(&#039;.exceed&#039;).exists()).toBe(true);
  });

  it(&#039;长度计算正确&#039;, () =&gt; {
    wrapper.setProps({
      content: &#039;Hello World!&#039;,
    });
    expect(wrapper.vm.currentLength).toBe(12);
    wrapper.setProps({
      content: &#039;你好世界！&#039;,
      mode: &#039;content&#039;,
    });
    expect(wrapper.vm.currentLength).toBe(10);
  });
});
```

5.运行测试
```bash
npm run test
```
