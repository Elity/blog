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
{	"scripts":{
		"test": "jest"
	},
	"jest": {
    "testURL": "http://localhost",
    "moduleFileExtensions": [
      "js",
      "vue"
    ],
    "moduleNameMapper": {
      "^@/(.*)$": "<rootDir>/src/$1"
    },
    "transform": {
      "^.+\\.js$": "<rootDir>/node_modules/babel-jest",
      ".*\\.(vue)$": "<rootDir>/node_modules/vue-jest"
    },
    "snapshotSerializers": [
      "<rootDir>/node_modules/jest-serializer-vue"
    ]
  }
}

```
3.配置Babel (.babelrc)

```json
{
	"env": {
    "test": {
      "presets": [["env", { "targets": { "node": "current" } }]]
    }
  }
}
```
4.项目根目录新建test文件夹
添加测试文件 `test.spec.js`
```javascript
import { shallowMount } from '@vue/test-utils';
import ContentLength from '@/components/ContentLength';

describe('Component.ContentLength', () => {
  const wrapper = shallowMount(ContentLength, {
    propsData: {
      content: 'Hello',
      maxLength: 10,
    },
  });

  it('传值正确', () => {
    expect(wrapper.vm.content).toBe('Hello');
    expect(wrapper.vm.maxLength).toBe(10);
  });

  it('未超过长度渲染正确', () => {
    expect(wrapper.find('.exceed').exists()).toBe(false);
  });

  it('超过长度渲染正确', () => {
    wrapper.setProps({
      content: 'Hello World!',
    });
    expect(wrapper.find('.exceed').exists()).toBe(true);
  });

  it('长度计算正确', () => {
    wrapper.setProps({
      content: 'Hello World!',
    });
    expect(wrapper.vm.currentLength).toBe(12);
    wrapper.setProps({
      content: '你好世界！',
      mode: 'content',
    });
    expect(wrapper.vm.currentLength).toBe(10);
  });
});
```

5.运行测试
```bash
npm run test
```
