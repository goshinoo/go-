# defer用法

#### 1.defer语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。



#### 2.调试复杂程序时，defer机制常被用于记录何时进入和退出函数。

**不要忘记defer语句后的圆括号**，否则本该在进入时执行的操作会在退出时执行，而本该在退出时执行的，永远不会被执行。

```go
func bigSlowOperation() {
    defer trace("bigSlowOperation")() // don't forget the ()
	extra parentheses
	// ...lots of work…
	time.Sleep(10 * time.Second) // simulate slow
	operation by sleeping
}

func trace(msg string) func() {
	start := time.Now()
	log.Printf("enter %s", msg)
	return func() {
		log.Printf("exit %s (%s)", msg,time.Since(start))
	}
}
```

每一次bigSlowOperation被调用，程序都会记录函数的进入，退出，持续时间。（我们用time.Sleep模拟一个耗时的操作）

```go
$ go build gopl.io/ch5/trace
$ ./trace
2015/11/18 09:53:26 enter bigSlowOperation
2015/11/18 09:53:36 exit bigSlowOperation (10.000589217s)
```

#### 3.defer语句中的函数会在return语句更新返回值变量后再执行，又因为在函数中定义的匿名函数可以访问该函数包括返回值变量在内的所有变量，所以，对匿名函数采用defer机制，可以使其观察函数的返回值。

```go
func double(x int) (result int) {
	defer func() { 
        fmt.Printf("double(%d) = %d\n", x, result) 
    }()
	return x + x
}
_ = double(4)
// Output:
// "double(4) = 8"

func triple(x int) (result int) {
	defer func() { 
        result += x 
    }()
	return double(x)
}
fmt.Println(triple(4)) // "12"
```

#### 4.在循环体中的defer语句需要特别注意，因为只有在函数执行完毕后，这些被延迟的函数才会执行。下面的代码会导致系统的文件描述符耗尽，因为在所有文件都被处理之前，没有文件会被关闭。

```go
for _, filename := range filenames {
	f, err := os.Open(filename)
	if err != nil {
		return err
	}
	defer f.Close() // NOTE: risky; could run out of file
	descriptors
	// ...process f…
}
```

一种解决方法是将循环体中的defer语句移至另外一个函数。在每次循环时，调用这个函数。

```go
for _, filename := range filenames {
	if err := doFile(filename); err != nil {
		return err
	}
}
func doFile(filename string) error {
	f, err := os.Open(filename)
	if err != nil {
		return err
	}
	defer f.Close()
	// ...process f…
}
```

下例中，通过os.Create打开文件进行写入，在关闭文件时，我们没有对f.close采用defer机制，因为这会产生一些微妙的错误。许多文件系统，尤其是NFS，写入文件时发生的错误会被延迟到文件关闭时反馈。如果没有检查文件关闭时的反馈信息，可能会导致数据丢失，而我们还误以为写入操作成功。如果io.Copy和f.close都失败了，我们倾向于将io.Copy的错误信息反馈给调用者，因为它先于
f.close发生，更有可能接近问题的本质。

```go
func fetch(url string) (filename string, n int64, err error) {
	resp, err := http.Get(url)
	if err != nil {
		return "", 0, err
	}
	defer resp.Body.Close()
	local := path.Base(resp.Request.URL.Path)
	if local == "/" {
		local = "index.html"
	}
	f, err := os.Create(local)
	if err != nil {
		return "", 0, err
	}
	n, err = io.Copy(f, resp.Body)
	// Close file, but prefer error from Copy, if any.
	if closeErr := f.Close(); err == nil {
		err = closeErr
	}
	return local, n, err
}
```

