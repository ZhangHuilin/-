Python提供了内置的enumerate函数，可以把各种迭代器包装成生成器，以便稍后产生输出值，这个生成器每次产生一对输出值，前一个是循环下标，后一个是从迭代器获取到的下一个序列元素。它的作用是允许我们遍历迭代器并自动计数。

### 1、遍历列表并自动计数
```
>>> fruit_list = ['apple', 'orange', 'banana', 'pear']
>>> for i, fruit in enumerate(fruit_list):
	print(i, fruit)

#输出	
0 apple
1 orange
2 banana
3 pear
```
可以给enumerate提供第二个参数，用来指定开始计数时所使用的值（默认为0）。
### 2、创建包含索引的元组列表
```
>>> test_list = ['cat', 'dog', 'monkey', 'tiger', 'sheep']
>>> my_list = list(enumerate(test_list, 1))
>>> my_list
# 输出
[(1, 'cat'), (2, 'dog'), (3, 'monkey'), (4, 'tiger'), (5, 'sheep')]

```
如果用在星期和月份上，是不是很方便！