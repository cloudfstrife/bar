# MultiBar

## About

show colorful progress bar in the command line,Write in  go programming language.

## Environment

Mac Linux or win10

## Usage

get source code

```
go get -u github.com/cloudfstrife/bar
```

### single progress bar demo

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

### multi progress bar demo

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

## Code description

```
.
├── bar.go					single progress bar definition
└── multi_bar.go			multi progress bar definition
```

### bar.go

```
func NewDefault() *Bar 
```

create a default progress bar , you can modify the value in this function to build personalized progress bar.

```
func (process *Bar) Show(w io.Writer, max int, clean bool) 
```

show progress bar

parameter description

* w			&nbsp;&nbsp;output target 

* max		&nbsp;&nbsp;use it for align multi progress bar output content

* clean		&nbsp;&nbsp;clean or don't clean the last time output , variable `showed` in `Bar` struct means is already do first out，if current out is the first invoked，this clean parameter is invalid

### multi_bar.go

```
func NewMultiBar() *MultiBar 
```

create a multe progress bar

```
func (multiBar *MultiBar) Append(process *Bar)
```

append progress bar into multe progress bar

parameter description

* process		&nbsp;&nbsp;point to progress bar 

```
func (multiBar *MultiBar) Show(w io.Writer) {
```

show progress bar

parameter description

* w			&nbsp;&nbsp;output target 


## SonarQube check

### requirements

* SonarQube have been installed 
* sonar scanner have been configured on local machine

### run command

```
go test -coverprofile=testing/coverprofile
sonar-scanner
```

## Reference

[Build your own Command Line with ANSI escape codes](http://www.lihaoyi.com/post/BuildyourownCommandLinewithANSIescapecodes.html)
