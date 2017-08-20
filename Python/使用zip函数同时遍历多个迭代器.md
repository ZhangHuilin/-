版本为`Python3`。
zip函数是Python内置的函数。

## 应用举例
```python
>>> list1 = ['a', 'b', 'c', 'd']
>>> list2 = ['apple', 'boy', 'cat', 'dog']
>>> for x, y in zip(list1, list2):
	print(x, 'is', y)
# 输出
a is apple
b is boy
c is cat
d is dog
```
这样就很简洁地实现了同时遍历两个列表，very pythonic！！！
## 原理
`Python3` 中的zip函数可以把两个或者两个以上的迭代器封装成**生成器**，这种zip生成器会从每个迭代器中获取该迭代器的下一个值，然后把这些值组装成元组（tuple）。这样，zip函数就实现了**平行地**遍历多个迭代器。
## 注意
- 如果输入的迭代器长度不同，那么，只要有一个迭代器遍历完，zip就不再产生元组了，zip会提前终止，这可能导致意外的结果，不可不察。如果不能确定zip所封装的列表是否等长，可以改用 `itertools` 内置模块中的zip_longest 函数，这个函数不在乎它们的长度是否相等。
- 在 `Python2` 中，zip不是生成器，它平行地遍历这些迭代器，组装元组，并把这些元组所构成的列表一次性完整地返回，这可能会占用大量内存并导致程序崩溃，如果在 `Python2` 中要遍历数据量大的迭代器，推荐使用 `itertools` 内置模块中的 `izip` 函数。