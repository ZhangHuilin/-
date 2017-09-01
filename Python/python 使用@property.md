# python 使用@property

python 内置 `@property` 装饰器可以把一个方法变成属性调用，还能检查参数。

```python
class Person(object):
    def __init__(self, name):
        self._name = name
    
    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, s):
        if len(s) > 10:
            raise ValueError('fake name')
        self._name = s

>>> p = Person()
>>> p.name = 'obama'   # 把方法转化为属性调用，等于 p.set_name('obama')
>>> p.name
'obama'
>>> p.name = 'Donald Trump'  # 检查参数，使得这个属性可控
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    p.name = 'Donald Trump'
  File "C:\Users\DELL\AppData\Local\Programs\Python\Python35-32\workspace\demo.py", line 12, in name
    raise ValueError('fake name')
ValueError: fake name

```

`@property` 把一个属性变成方法，同时又创建了另一个装饰器 `@name.setter` ，负责把 setter 方法变成属性赋值，检查参数，这样属性就可控了。

如果我们只定义 getter 方法，不定义 setter 方法，那么这个属性就是一个只读属性。