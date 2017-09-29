# Go 切片（slice）

Go中，数组的长度是不可变的，但是Go提供了一种灵活的内置类型：切片（也称为动态数组）。

切片的长度不固定，可以追加元素，也可以扩容。



### slice 的本质

slice是引用类型，是一个指向数组的指针。

在内存中，一个slice是一个结构体：

```go
struct Slice
{
    byte* array;    // actual data
    uintgo  len;    // number of elements
    uintgo  cap;    /// allocated number of elements
}
```

所以，一个slice是一个包含三个域的结构体：指向slice中第一个元素的指针，slice的长度，slice的容量。

slice的长度是slice中元素的个数，slice的容量是slice中最多能有的元素的个数。

因此长度是下标操作的上界，如x[i]中 i 必须小于长度。容量是分割操作的上界，如x[i:j]中 j 不能大于容量。

如下图：

![](E:\articles\sucai\slice.png)



### 定义切片

```go
var s1 []int    // 声明一个未指定大小的数组来定义切片
var s2 []int = make([]int, len)  // 使用make() 来创建切片
s3 := make([]int, len)   // 同上
s4 := make([]int, len, cap)  // 也可以指定容量。
```

切片未初始化之前默认为 nil，长度为 0 。

内置函数 len() 和 cap() 提过了获取切片长度和容量的方法。



### 切片初始化和截取

```go
s1 := []int{1, 2, 3} // 直接初始化，其 cap = len = 3

arr := [...]int{1, 3, 5, 7, 9, 11}  // arr 是一个数组
s2 := arr[:]   // 切片s2 是数组arr的引用
s3 := arr[2:5]  // s3 = [5, 7, 9]
s3 := arr[2:]   // s3 = [5 7 9 11]  缺省endIndex时，就直到arr的最后一个元素
s3 := arr[:3]   // s2 = [1, 2, 5]   缺省startIndex时，就从arr的第一个元素开始

s4 := s3[startIndex:endIndex]  // 用切片s3 初始化 s4，但它们只是不同的结构体，指向了同一片内存区，共享底层数据
```



### 切片追加元素

内置函数 append() 可以向切片追加新元素。

```go
s1 = append(s1, 4)     // s1 = [1 2 3 4]
s1 = append(s1, 5, 6)  // s1 = [1 2 3 4 5 6]

```

对 slice 进行 append 操作时，可能会造成 slice 自动扩容。其扩容时的规则是：

- 如果新的大小是当前大小的2倍以上，则大小增长为新大小
- 否则循环一下操作：如果当前大小小于1024，按每次2倍增长，否则每次按当前大小的 1/4 增长。直达增长的大小超过或等于新大小。

我们通常不知道 append 调用是否导致了内存的重新分配，因此我们不能确认新的slice和原始的slice是否引用的是相同的底层数组空间。同样，我们不能确认在原先的slice上的操作会不会影响到新的 slice，因此，通常是将 append 返回的结果直接复制给输入的 slice 变量， 即  s = append(s, r) 。



### 手动扩容

```go
s5 := make([]int, len(s3), (cap(s3))*2)  // 创建切片s5 是 s3 的两倍容量
copy(s5, s3) // 拷贝 s3 的内容到 s5
```



### slice作为函数参数

向函数传递 slice 将允许在函数内部修改底层数组的元素。



### 其他

- 不能使用 == 来判断两个slice 是给含有全部相同元素
- slice 唯一合法的比较是 和 nil 比较。
- 判断 slice 是否为空，使用  `len(s) == 0` , 而不是 `s == nil` 。