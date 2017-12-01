# Go语言源码阅读之 sort 包

Go语言是开源的，而且Go的标准库功能十分强大，通过阅读源码，可以更好地学习Go代码的写作，更好地掌握标准库的使用。

今天阅读Go标准库的sort包，这个包是实现排序功能的，在安装目录的src文件夹下。

## 使用示例

```go
func main() {
	a := []string{"bag", "cat", "apple", "dog"}
	fmt.Println("before sort:", a)
	sort.Strings(a)  //对字符串切片排序
    // 也可以这样调用 sort.StringSlice(a).Sort() 
	fmt.Println("after sort:", a)

	b := []int{1, 3, 5, 2, 4, 6}
	fmt.Println("before sort:", b)
	sort.Ints(b)  //对整数切片排序
    // 也可以这样调用 sort.IntSlice(a).Sort() 
	fmt.Println("after sort:", b)

	c := []float64{2.0, 1.5, 3.3, 0.8}
	fmt.Println("before sort:", c)
	sort.Float64s(c)  //对浮点数切片排序
    // 也可以这样调用 sort.Float64Slice(a).Sort() 
	fmt.Println("after sort:", c)
}

// 输出
before sort: [bag cat apple dog]
after sort: [apple bag cat dog]
before sort: [1 3 5 2 4 6]
after sort: [1 2 3 4 5 6]
before sort: [2 1.5 3.3 0.8]
after sort: [0.8 1.5 2 3.3]
```



## 源码

sort 包定义了Interface接口，包含一下三个方法，**只要实现了这个接口，就可以调用sort包中的排序方法**。

```go
type Interface interface {
	// Len is the number of elements in the collection.
	Len() int
	// Less reports whether the element with
	// index i should sort before the element with index j.
	Less(i, j int) bool
	// Swap swaps the elements with indexes i and j.
	Swap(i, j int)
}
```

示例中的IntSlice，StringSlice，Float64Slice 等类型都实现了Interface接口，以 IntSlice为例，如下：

```go
// IntSlice attaches the methods of Interface to []int, sorting in increasing order.
type IntSlice []int

func (p IntSlice) Len() int           { return len(p) }
func (p IntSlice) Less(i, j int) bool { return p[i] < p[j] }
func (p IntSlice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

// Sort is a convenience method.
func (p IntSlice) Sort() { Sort(p) }

// Ints sorts a slice of ints in increasing order.
func Ints(a []int) { Sort(IntSlice(a)) }


```

因为 IntSlice 类型有方法 `Sort()` , 所以 `sort.IntSlice(b).Sort()` 调用是可以的，`sort.IntSlice(b)` 是将 slice b 转换为类型 IntSlice。同时Go定义了函数 Ints() 实现了对IntSlice 的排序，因此也可以 `sort.Ints(b)` 的方式调用。Ints 函数内部调用了 sort包的Sort方法，定义如下：

```go
// Sort sorts data.
// It makes one call to data.Len to determine n, and O(n*log(n)) calls to
// data.Less and data.Swap. The sort is not guaranteed to be stable.
func Sort(data Interface) {
	n := data.Len()
	quickSort(data, 0, n, maxDepth(n))
}
//Sort 使用快速排序实现，快排的代码在sort.go 文件中，还有插入排序、堆排序等的代码
```

因此，一个类型只要实现了Interface接口，就可以调用 sort.Sort()方法对其进行排序。

接下来，让我们实现对字符串切片按照长度进行排序。

```go
type mystring []string

func (p mystring) Len() int           { return len(p) }
func (p mystring) Less(i, j int) bool { return len(p[i]) < len(p[j]) }
func (p mystring) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

func main() {
	a := []string{"bbb", "aa", "c", "dddd"}
	fmt.Println("before sort:", a)
	sort.Sort(mystring(a)) // 有一个类型转换的操作
	fmt.Println("after sort:", a)
}

// 输出
before sort: [bbb aa c dddd]
after sort: [c aa bbb dddd]
```



接下来，让我们实现对一个结构切片的排序，即一个切片中的元素是一个结构。

```go
type mystruct struct {
	name string
	age int
}

type myslice []mystruct

func (p myslice) Len() int           { return len(p) }
// 按name的长度排序
func (p myslice) Less(i, j int) bool { return len(p[i].name) < len(p[j].name) }
// 按name排序
//func (p myslice) Less(i, j int) bool { return p[i].name < p[j].name } 
//按age 排序
// func (p myslice) Less(i, j int) bool { return p[i].age < p[j].age }
func (p myslice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

func main() {
	a := mystruct{name:"Bob", age:20}
	b := mystruct{name:"Alice", age:18}
	c := mystruct{name:"Baby", age:21}

	sli := myslice{a, b, c}

	fmt.Println("before sort:", sli)
	sort.Sort(sli)
	fmt.Println("after sort:", sli)
}
// 输出
before sort: [{Bob 20} {Alice 18} {Baby 21}]
after sort: [{Bob 20} {Baby 21} {Alice 18}]


```

sort.go文件中还定义了 `IsSorted()` `Reverse()` 等方法，只要实现了 Interface 接口，就可以调用这些方法。

OK，sort包的源码阅读到此就结束了。总结一下:

- 会对自己定义的切片类型进行排序，就是要实现Interface接口；
- 对接口的使用有进一步的理解，同时学习源码的代码风格，写出更规范的代码。