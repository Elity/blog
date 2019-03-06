---
title: PHP中逻辑运算符AND/OR与||/&&的一个坑
tags:
  - php，运算符
url: 1980.html
id: 1980
categories:
  - 编程随想
date: 2014-07-14 10:03:56
---

我原来以为PHP中的and和&&是一样的, 只是写法上为了可读性和美观, 事实上我错了. 这里面深藏了一个坑!

    $bA = true;
    $bB = false;
    $b1 = $bA and $bB;
    $b2 = $bA && $bB;
    var_dump($b1); // $b1 = true
    var_dump($b2); // $b2 = false
    
    $bA = false;
    $bB = true;
    $b3 = $bA or $bB;
    $b4 = $bA || $bB;
    var_dump($b3); // $b3 = false
    var_dump($b4); // $b4 = true
    

奇怪吧,and/&&和or/||出来的结果竟然不一样的. 问题出在哪里呢?我们再看一段代码:

    $bA = true;
    $bB = false;
    var_dump($bA and $bB); // false
    var_dump($bA && $bB); // false
    
    $bA = false;
    $bB = true;
    var_dump($bA or $bB); // true
    var_dump($bA || $bB); // true
    

更奇怪, 这时怎么是对的. 所以问题可能出现在`=`上, 一番google和文档,终于找到了答案! [运算符优先级](http://cn2.php.net/manual/en/language.operators.precedence.php)通过这个表, 我们可以看到and/&&和or/||这两组运算符的优先级竟然是不一样的.and和or的优先级是低于`=`的, 所以上面的代码就好理解了, 就是先做赋值然后再做了一个and或or的逻辑运算, 这个运算的结果并没有存下来. 所以最后出来让我们匪夷所思的结果.

> The following table lists the operators in order of precedence, with the highest-precedence ones at the top. Operators on the same line have equal precedence, in which case associativity decides grouping.

总结:慎重使用and,or和xor的逻辑运算符, 避免和赋值号以及&&和||一起用, 以免发生不必要的逻辑错误. 其实我更喜欢javascript那样的逻辑运算符:

    var  a="a",b="b";
    console.log( a&&b );//  "b"
    
    var  a="a",b="b";
    console.log( a||b );//  "a"