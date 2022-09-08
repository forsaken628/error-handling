用宏来简化go的错误处理是不同于常见错误处理提案的新思路。

这个方案叫左值宏，是一个易于阅读，易于理解，易于实现，且非常通用的方案。

### 宏定义与展开
```go
func Bar1() (int, error)

func Far() error {
	// 左值宏

	// 宏定义，err是宏名称，!标识这是一个宏。err同时也是关联变量的名称，（出于简单性）。
	// 仅支持关联单一变量，且必须显式指定变量类型
	err! error {
	    if err != nil {
	        return err
	    }
	}

	b1, err! := Bar1()
	_, err! = Bar1()

	/* 宏展开后
	var _err0 error // 每次调用宏会关联到一个独立的变量上，作用域限于宏内部
	b1, _err0 := Bar1()
	{
		var err error = _err0
		if err != nil {
			return err
		}
	}
	var _err1 error
	_, _err1 = Bar1()
	{
		var err error = _err0
		if err != nil {
			return err
		}
	}
	*/

	_=b1

	// 行为非常类似闭包函数，仅仅只是提供了令外层函数return的能力

	return nil
}
```

### 不限于error interface的形式
```go
	// 不限于error interface的形式，也可以用于其他错误处理的形式，如bool
	ok! bool {
	    if !ok {
	        return errors.New("not found")
	    }
	}

	var m map[int]int
	a, ok! := m[5]
	s += a
	a, ok! = m[3]
	s += a
```

### 不限于错误处理的场景
```go
	var ls []int
	item! int {
		if i % 2 == 0 {
			ls = append(ls, item)
		}
	}
	item! = 1
	item! = 2
	item! = 3
	item! = 4
	item! = 5

	// ls == []int{2,4}
```

### 同一行中包含多个宏
```go
	i! int {
		fmt.Println(i)
	}
	i!,i!,i! = 1,2,3
	/*
	var _i0,_i1,_i2 int
	_i0,_i1,_i2 = 1,2,3
	{
		var i = _i2 // 同一行中包含多个宏，从右向左展开。或者这是一个不好的写法，不应该支持？
		fmt.Println(i)
	}
	{
		var i = _i1
		fmt.Println(i)
	}
	{
		var i = _i0
		fmt.Println(i)
	}
	*/
```

### 总结
不支持闭包，相互嵌套，goto，顶层continue，顶层break，Label 等，仅仅只是一个简化重复语句的语法糖
