# Go结构体内存布局

**1.结构体是占用一块连续的内存，一个结构体变量的大小是由结构体中的字段决定。**

```go
type Foo struct {
	A int8 // 1byte
	B int8 // 1
	C int8 // 1
}

var f Foo
fmt.Println(unsafe.Sizeof(f))  // 3
```

**2.但是结构体的大小又不完全由结构体的字段决定，例如：**

```go
type Bar struct {
	x int32 // 4
    y *Foo  // 8(64位下指针类型都为8byte)
	z bool  // 1
}

var b1 Bar
fmt.Println(unsafe.Sizeof(b1)) // 24
```

此结构体内存布局如下：

![memory layout of Bar1](.\picture\struct02.png)

Go 在编译的时候会按照一定的规则自动进行内存对齐。之所以这么设计是为了减少 CPU 访问内存的次数，加大 CPU 访问内存的吞吐量。如果不进行内存对齐的话，很可能就会增加CPU访问内存的次数。例如下图中CPU想要获取`b1.y`字段的值可能就需要两次总线周期。

![word size](.\picture\struct03.png)

因为 CPU 访问内存时，并不是逐个字节访问，而是以字（word）为单位访问。比如 64位CPU的字长（word size）为8bytes，那么CPU访问内存的单位也是8字节，每次加载的内存数据也是固定的若干字长，如8words（64bytes）、16words(128bytes）等。

**3.字段对齐**

我们上面已经知道了可以通过内置`unsafe`包的`Sizeof`函数来获取一个变量的大小，此外我们还可以通过内置`unsafe`包的`Alignof`函数来获取一个变量的对齐系数，例如：

```go
// 结构体变量b1的对齐系数
fmt.Println(unsafe.Alignof(b1))   // 8
// b1每一个字段的对齐系数
fmt.Println(unsafe.Alignof(b1.x)) // 4：表示此字段须按4的倍数对齐
fmt.Println(unsafe.Alignof(b1.y)) // 8：表示此字段须按8的倍数对齐
fmt.Println(unsafe.Alignof(b1.z)) // 1：表示此字段须按1的倍数对齐
```

`unsafe.Alignof()`的规则如下：

- 对于任意类型的变量 x ，`unsafe.Alignof(x)` 至少为 1。
- 对于 struct 类型的变量 x，计算 x 每一个字段 f 的 `unsafe.Alignof(x.f)`，`unsafe.Alignof(x)` 等于其中的最大值。
- 对于 array 类型的变量 x，`unsafe.Alignof(x)` 等于构成数组的元素类型的对齐倍数。

在了解了上面的规则之后，我们就可以通过调整结构体 Bar 中字段的顺序来减少其大小：

```go
type Bar2 struct {
	x int32 // 4
	z bool  // 1
	y *Foo  // 8
}
// or
// type Bar2 struct {
    // z bool  // 1
	// x int32 // 4
	// y *Foo  // 8
// }

var b2 Bar2
fmt.Println(unsafe.Sizeof(b2)) // 16
```

内存布局如下：

![memory layout of Bar2](.\picture\struct04.png)

总结一下：在了解了Go的内存对齐规则之后，我们在日常的编码过程中，完全可以通过合理地调整结构体的字段顺序，从而优化结构体的大小。



## 结构体内存布局的特殊场景

首先我们需要了解的一个前提是：如果结构或数组类型不包含大小大于零的字段（或元素），则其大小为0。两个不同的0大小变量在内存中可能有相同的地址。

由于空结构体`struct{}`的大小为 0，所以当一个结构体中包含空结构体类型的字段时，通常不需要进行内存对齐。例如：

```go
type Demo1 struct {
	m struct{} // 0
	n int8     // 1
}

var d1 Demo1
fmt.Println(unsafe.Sizeof(d1))  // 1
```

但是当空结构体类型作为结构体的最后一个字段时，如果有指向该字段的指针，那么就会返回该结构体之外的地址。为了避免内存泄露会额外进行一次内存对齐。

```go
type Demo2 struct {
	n int8     // 1
	m struct{} // 0
}

var d2 Demo2
fmt.Println(unsafe.Sizeof(d2))  // 2
```

![empty struct memory layout](.\picture\struct06.png)
