## go 判断文件是否存在

方法是根据 `os.Stat()` 函数返回的错误值进行判断：

```go
import "fmt"
import "os"
func FileExists(path string) (string, error) {
    _, err := os.Stat(Path)
    if os.IsNotExist(err) {
        fmt.Println("file doesn't exist")
    }
    if err == nil {
        fmt.Println("file exists")
    }
}

```

