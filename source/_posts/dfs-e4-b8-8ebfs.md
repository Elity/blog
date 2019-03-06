---
title: DFS与BFS
tags:
  - bfs
  - dfs
url: 2426.html
id: 2426
comments: false
categories:
  - 编程随想
date: 2018-10-13 23:52:08
---

DFS与BFS实现，考虑到递归有爆栈的可能，所以，采用循环来做，实现思路都是通过维护一个stack，只是入栈的规则不一样。
以遍历DOM节点为例：

```javascript

function dfs(node) {
    const stack = [node];
    const nodes = [];
    let tmp;
    while (tmp = stack.pop()) {
        nodes.push(tmp)
        let childs = tmp.children, len = childs.length, i = len - 1;
        for (; i &gt; -1; i--) {
            stack.push(childs[i])
        }
    }
    return nodes
}

```

```javascript
function bfs(node) {
    const stack = [node];
    const nodes = [];
    let tmp;
    while (tmp = stack.pop()) {
        nodes.push(tmp)
        let childs = tmp.children, len = childs.length, i = 0;
        for (; i &lt; len; i++) {
            stack.unshift(childs[i])
        }
    }
    return nodes
}
```