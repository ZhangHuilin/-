# python 定制类

### `__slots__`

`python` 在定义`class` 时，使用变量`__slots__` 来限制该`class` 实例能添加的属性。

```python
# 以下所有代码运行在python 3.5上
# 未使用 __slots__ 变量时，可以给一个实例绑定任何属性和方法
>>> class Person(object):
	    pass

>>> p = Person()
>>> p.name = 'Bob'
>>> p.name
'Bob'
>>> p.age = 25
>>> p.age
25
>>> p.sex = 'male'
>>> p.sex
'male'

# 使用 __slots__ 变量，可以限制添加属性和方法
class Person(object):
    __slots__ = ('name', 'age') # 用元组定义允许绑定的属性

>>> p = Person()
>>> p.name = 'Bob'
>>> p.name
'Bob'
>>> p.age = 25
>>> p.age
25
>>> p.sex = 'male'  # 因为 sex 没有被放 __slots__ 中，所以不能绑定 sex 属性，会引起 AttributeError
Traceback (most recent call last):
  File "<pyshell#11>", line 1, in <module>
    p.sex = 'male'
AttributeError: 'Person' object has no attribute 'sex'
```

`__slots__` 定义的属性仅对当前的类实例起作用，对继承的子类不起作用

如果在子类中也定义 `__slots__` , 这样，子类实例允许定义的属性就是自身的`__slots__` 加上父类的的` __slots__` 。





### `__str__`

使用 `print` 打印对象时调用，可以返回易读的对象。

```python
>>> class Person(object):
	    def __init__(self, name):
            self.name = name

>>> p = Person('Bob')
>>> print(p)
<__main__.Person object at 0x02E46710> # 返回对象的内存地址


>>> class Person(object):
	    def __init__(self, name):
            self.name = name
        def __str__(self):
          return 'Person object whose name is {}'.format(self.name)
        
>>> p = Person('Bob')
>>> print(p)
Person object whose name is Bob
>>> p
<__main__.Person object at 0x02E463F0>   # 如果不用print，还是返回对象的内存地址

# 如果再定义个__repr__()
>>> class Person(object):
	    def __init__(self, name):
            self.name = name
        def __str__(self):
          return 'Person object whose name is {}'.format(self.name)
        def __repr__(self): # 也可以 __repr = __str__
          return 'Person object whose name is {}'.format(self.name) 
        
>>> p = Person('Bob')
>>> p
Person object whose name is Bob
```



###  `__iter__`

`__iter__()` 方法返回一个可迭代对象，使得这个类可以用于 `for ... in ` 循环，不断调用改迭代对象的 `__next__()` 方法拿到下一个循环值，直到循环结束。

```python
>>> class Square(object):
        def __init__(self):
            self.n = 1
        def __iter__(self):
            return self    # 实例就是迭代对象
        def __next__(self):
            if self.n * self.n > 100:
                raise StopIteration
            self.n = self.n + 1
            return self.n
>>> for x in Square():
	    print(x)
	
2
3
4
5
6
7
8
9
10
11        
```



### `__getitem__`

上一个例子中，要想让 `Square` 实例可以像列表那样用 index 获取元素还是不行

```python
>>> Square()[3]
Traceback (most recent call last):
  File "<pyshell#81>", line 1, in <module>
    Square()[3]
TypeError: 'Square' object does not support indexing
>>> 
```

此时，需要定义 `__getitem__()` 方法

```python
>>> class Square(object):
        def __getitem__(self, n):
            for x in range(n):
                self.n += 1
            return self.n
                      

>>> Square()[3]
4
>>> Square()[5]
6
```

应该检测 `__getitem__()` 输入的参数是不是负数，是不是切片， 有没有对 step 做处理。 还可以把对象看成字典，返回 key 的 value。

对应的有 `__setitem__()`, `__delitem__()` 等方法。



对应的还有 `__getattr__()` 方法，在没有找到属性的情况下，调用该方法，查找属性。



###  `__call__()`

直接调用实例

```python
>>> class Person(object):
	def __init__(self, name):
	    self.name = name
	def __call__(self):
	    print('his name is {}'.format(self.name))

        
>>> p = Person('Bob')
>>> p()   # 像把实例当成函数一样调用
his name is Bob
>>> callable(p)  # 使用 callable 函数判断一个对象是否能被调用
True	    
```





###  `__dict__`

```python
>>> p.__dict__
{'name': 'Bob'}
>>> Person.__dict__
mappingproxy({'__dict__': <attribute '__dict__' of 'Person' objects>, '__init__': <function Person.__init__ at 0x02E39FA8>, '__weakref__': <attribute '__weakref__' of 'Person' objects>, '__call__': <function Person.__call__ at 0x031EC030>, '__module__': '__main__', '__doc__': None})
```

实例，类型等对象的所有属性，都放置在其 `__dict__` 字典中。





### 最后

`__del__(self)` 用于在 GC 之前，python回调用这个方法来完成一些终止工作。

这样的定制方法还有很多，需要时可以查阅 [python官方文档](https://docs.python.org/3/reference/datamodel.html#special-method-names) 。

还有用于比较的方法，如 `__lt__() 表示小于` , `__le__() 表示小于等于`， `__gt__() 表示大于` ，`__ge__() 表示大于等于`，`__eq__()表示等于`，`__ne__() 表示不等于`

`__hash__()` 用于哈希，返回一个32位长的整数。