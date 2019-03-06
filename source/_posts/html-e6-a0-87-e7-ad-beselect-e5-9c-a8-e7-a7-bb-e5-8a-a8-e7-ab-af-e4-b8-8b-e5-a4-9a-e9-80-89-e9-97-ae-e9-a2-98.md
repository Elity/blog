---
title: HTML标签select在移动端下多选问题
tags:
  - HTML
  - select
  - 移动端
url: 2126.html
id: 2126
categories:
  - 编程随想
date: 2017-01-12 16:38:11
---

默认情况下：

```html
<select name="test"multiple="multiple">
    <option value="1">aa</option>
    <option value="2">bb</option>
    <option value="3">cc</option>
    <option value="4">dd</option>
    <option value="5">ee</option>
</select>
```
点击多选条触发多选，然后直接点完成，而不去实际选择某项，这时系统会默认把第一项选上（实际显示未被选择）。
然后在不刷新的情况下再点开多选菜单去选择其它项目，选择界面上第一项仍显示未被选择，提交给后台的数据包含了第一项。
这样的情况，在最开始的option前面插入一项隐藏的，不可用的分组就OK了：
```html
<select name="test"multiple="multiple">
    <optgroup disabled hidden></optgroup>
    <option value="1">aa</option>
    <option value="2">bb</option>
    <option value="3">cc</option>
    <option value="4">dd</option>
    <option value="5">ee</option>
</select>
```
