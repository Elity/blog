---
title: 又get一个更pythonic的方法
tags:
  - Python
  - pythonic
url: 2058.html
id: 2058
categories:
  - 编程随想
date: 2016-01-30 22:33:35
---

原来自己写一个单独的类文件，常常是文件一个名字，类一个名字，在别的文件导入的时候则需要这样些：
```python
from 文件名 import *
```
```python
from 文件名 import 类名
```
今天看这个文章的时候[让python程序更加pythonic的建议（一）](http://115.159.48.140/2015/12/29/%E8%AE%A9python%E7%A8%8B%E5%BA%8F%E6%9B%B4%E5%8A%A0pythonic%E7%9A%84%E5%BB%BA%E8%AE%AE%EF%BC%88%E4%B8%80%EF%BC%89/)看到这么一段代码：
```python
class _const:
    class ConstError(TypeError): pass
    class ConstCaseError(ConstError): pass
 
    def __setattr__(self, name, value):
        if self.__dict__.get(name):
            raise self.ConstError(&quot;Can&#039;t change const. %s&quot; %name)
        if not name.isupper():
            raise  self.ConstCaseError(\
                &#039;const name &quot;%s&quot; is not all uppercase&#039; %name)
        self.__dict__[name] = value
 
import sys
sys.modules[__name__] = _const()
```
最后一句：
```python
sys.modules[__name__] = _const()
```
其中`sys.modules`是IDE启动后导入的所有模块的一个字典,`__name__`则是当前的模块名
关键点就在于`__name__`在shell模式下是`__main__`,在被导入的时候是其所在文件的文件名.
所以当上述文件被存为const.py时，我们在别的文件中：`import const`的时候，const已经是作为类_const的一个实例存在于运行环境中了