# MultiBar

## 关于

go语言编写的彩色命令行进度条

## 环境

Mac Linux 或者 win10

## 使用说明

获取代码

```
go get -u github.com/cloudfstrife/bar
```

### 单进度条示例

```
package main

import (
	"os"
	"time"

	"github.com/cloudfstrife/bar"
)

func main() {
	bar := bar.NewDefault()
	bar.Title = "bar1"
	for i := 0; i <= 100; i++ {
		bar.Percent = i
		bar.Show(os.Stdout, 10, true)
		time.Sleep(100 * time.Millisecond)
	}
}
```

### 多进度条示例

```
package main

import (
	"os"
	"sync"
	"time"

	"github.com/cloudfstrife/bar"
)

func main() {
	bars := bar.MultiBar{}
	bar1 := bar.NewDefault()
	bar1.Title = "bar1"

	bar2 := bar.NewDefault()
	bar2.Title = "bar2"

	bar3 := bar.NewDefault()
	bar3.Title = "bar3"

	bars.Append(bar1)
	bars.Append(bar2)
	bars.Append(bar3)
	wg := sync.WaitGroup{}

	pro := func(b *bar.Bar, t time.Duration) {
		wg.Done()
		for i := 0; i <= 100; i++ {
			b.Percent = i
			time.Sleep(t)
		}
	}
	wg.Add(1)
	go pro(bar1, 100*time.Millisecond)

	wg.Add(1)
	go pro(bar2, 200*time.Millisecond)

	wg.Add(1)
	go pro(bar3, 500*time.Millisecond)

	wg.Add(1)
	go func() {
		defer wg.Done()
		defer bars.Show(os.Stdout)
		for {
			bars.Show(os.Stdout)
			time.Sleep(100 * time.Millisecond)
			if bar1.Percent == 100 && bar2.Percent == 100 && bar3.Percent == 100 {
				break
			}
		}
	}()
	wg.Wait()
}
```

## 代码说明（Code description）

```
.
├── bar.go					进度条定义
└── multi_bar.go			多进度条定义
```

### bar.go

```
func NewDefault() *Bar 
```

创建一个默认的进度条，可以修改此方法中的初始化值构建个性化的默认进度条

```
func (process *Bar) Show(w io.Writer, max int, clean bool) 
```

输出进度条

参数说明：

* w			&nbsp;&nbsp;输出目地址

* max		&nbsp;&nbsp;最长的title，用于多进度条输出时对齐输出内容

* clean		&nbsp;&nbsp;是否清除上一次输出，Bar结构体内部有一个showed，表示是否进行过输出，如果是第一次输出，即使clean为true也不会清理

### multi_bar.go

```
func NewMultiBar() *MultiBar 
```

创建一个进度多进度条struct 

```
func (multiBar *MultiBar) Append(process *Bar)
```

添加一个进度条到多进度条输出中

参数说明 

* process 		&nbsp;&nbsp;进度条指针

```
func (multiBar *MultiBar) Show(w io.Writer) {
```

输出进度条

参数说明 

* w			&nbsp;&nbsp;输出目地址

## SonarQube 代码质量审查

### 前置条件

* 已安装SonarQube
* 已配置本地sonar scanner

### 执行指令

```
go test -coverprofile=testing/coverprofile
sonar-scanner
```

## Reference(参考资料)

[Build your own Command Line with ANSI escape codes](http://www.lihaoyi.com/post/BuildyourownCommandLinewithANSIescapecodes.html)
