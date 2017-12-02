# Go源码阅读之errors包

我们经常见到这样的写法：

```go
func f1(arg int) (int, error) {
	if arg == 42 {
		return -1, errors.New("can't work with 42")
	}
	return arg +3, nil
}

if r, e := f1(i); e != nil {
			fmt.Println("f1 failed:", e)
		} else {
			fmt.Println("f1 worked:", r)
		}
```



函数f1返回值中有一个error类型，这个error表示了程序运行中出错的信息，能够帮助我们快速定位程序的bug位置。 

error是内建类型，是一个接口，只有一个Error()方法，定义如下：

```go
// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
	Error() string
}
```

errors.go 文件只有10行代码，如下：

```go
// Package errors implements functions to manipulate errors.
package errors

// New returns an error that formats as the given text.
func New(text string) error {
	return &errorString{text}
}

// errorString is a trivial implementation of error.
type errorString struct {
	s string
}

func (e *errorString) Error() string {
	return e.s
}
```

类型 errorString 是一个结构，并且实现了Error()方法，即errorString实现了error接口，所以函数New()返回值要求是error类型，实际返回errorString是可以的（errorString是不可以导出的）。

下面我们演示一下：

```go
func f1(arg int) (int, error) {
	if arg == 42 {
		return -1, errors.New("can't work with 42")
	}
	return arg +3, nil
}

func main() {
	for _, i := range []int{7, 42} {
		if r, e := f1(i); e != nil {
			fmt.Println("f1 failed:", e)
		} else {
			fmt.Println("f1 worked:", r)
		}
	}
}

// 输出
// f1 worked: 10
// f1 failed: can't work with 42
```

我们在函数f1()中使用errors.New()来返回错误信息。



那我们可以不返回源码定义的errorString，而是返回自定义的结构吗？当然可以，只要我们自定义的结构实现了error接口，即实现了Error()方法，就可以啊！！

下面我们演示返回自定义的错误类型：

```go
type myError struct {
	arg int
	prob string
}

func (e *myError) Error() string {
	return fmt.Sprintf("%d -*- %s", e.arg, e.prob) // 用Sprintf报数字和字符串合并作为字符串返回
}

func f2(arg int) (int, error) {
	if arg == 42 {
		return -1, &myError{arg, "can't work with it"}
	}
	return arg +3, nil
}
func main() {
	for _, i := range []int{7, 42} {
		if r, e := f2(i); e != nil {
			fmt.Println("f2 failed:", e)
		} else {
			fmt.Println("f2 worked:", r)
		}
	}

	_, e := f2(42)
	if ae, ok := e.(*myError); ok {
		fmt.Println(ae.arg)
		fmt.Println(ae.prob)
	}
}

// 输出
// f2 worked: 10
// f2 failed: 42 -*- can't work with it
// 42
// can't work with it
```

我们自定义的myError是一个结构，包含了两个成员，一个arg，表示运行时的f2参数，prob 是错误消息。然后myError实现了error接口。



有一个方便的函数 fmt.Errorf() , 还可以处理字符串的格式化，很好用，源码如下;

```go
package fmt

import "errors"

func Errorf(format string, args ...interface{}) error {
    return errors.New(Sprintf(format, args...))
}
```

