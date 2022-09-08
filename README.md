用宏来简化go的错误处理是不同于常见错误处理提案的新思路。

这个方案叫左值宏，是一个易于阅读，易于理解，易于实现，且非常通用的方案。

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
	b2, err! := Bar1()
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
	b2, _err1 := Bar1()
	{
		var err error = _err0
		if err != nil {
			return err
		}
	}
	*/

	_=b1
	_=b2

	// 行为非常类似闭包函数，仅仅只是提供了令外层函数return的能力

	return nil
}
```

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

```go
	// 也不限于错误处理
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

不支持闭包，相互嵌套，goto，顶层continue，顶层break，Label 等
仅仅只是一个语法糖
