# python 类方法

## 静态方法

静态方法：

- 通过类型和实例都能调用
- 使用装饰器 `@staticmethod` 来定义静态方法
- 无需传入 self 参数，静态方法无法访问实例变量（除非把self传给静态方法）



类方法：

- 定义时的第一个参数是 cls，
- 类和实例都能够调用
- 使用装饰器 `@classmethod` 定义

```python
# -*- coding:utf-8 -*-
class Person(object):
    COUNTRY = 'CHINA'
    def __init__(self, name):
        self.name = name

    @staticmethod
    def print1(self):
        print(self.name)

    @staticmethod
    def print2():
        print(Person.COUNTRY)

    @classmethod
    def print3(cls):
        print(cls.COUNTRY)



if __name__ == "__main__":
    p = Person('Bob')
    p.print1(p)
    p.print2()
    Person.print2()
    p.print3()
    Person.print3()
 
## output
Bob  # 实例调用静态方法，静态方法传入self参数，可以访问实例变量
CHINA  # 实例调用静态方法，访问类变量
CHINA  # 类调用静态方法，访问类变量
CHINA  # 实例调用类方法，访问类变量
CHINA  # 类调用类方法，访问类变量
```

