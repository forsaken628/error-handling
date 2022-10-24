# Left Value Macro
easy-to-read, easy-to-understand, easy-to-implement, and very general proposal.

### Macro definition and expansion
```go
func Bar1() (int, error)

func Far() error {
	// definition, use 'err' as macro name, also variable name.
	// Only a single variable is supported, and the variable type must be specified explicitly
	err! error {
	    if err != nil {
	        return err
	    }
	}

	b1, err! := Bar1()
	_, err! = Bar1()

	/* expansion
	var _err0 error // Each call to the macro is associated with a separate variable, and the scope is limited to the inside of the macro
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

	// The behavior is very similar to the closure function, but only provides the ability to make the outer function return

	return nil
}
```

### Not limited to the form of error interface
```go
	// Not limited to the form of error interface, it can also be used in other forms of error handling, such as bool
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

### Not limited to error handling scenarios
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

### Multiple macros on the same line
```go
	i! int {
		fmt.Println(i)
	}
	i!,i!,i! = 1,2,3
	/*
	var _i0,_i1,_i2 int
	_i0,_i1,_i2 = 1,2,3
	{
		var i = _i2 // Multiple macros on the same line expand from right to left. Or is this a bad spelling and shouldn't be supported?
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

### Return wrapped error
```go
	// May not need adjustment, old form is clear. 
	b1, err := Bar1()
	if err!= nil { return fmt.Errorf("fail %s: %w", "b1", err) }
```
```go
	// Or declare a function-scoped variable.
	errMsg := ""
	err! error {
	    if err != nil {
	        return fmt.Errorf("fail %s: %w", errMsg, err)
	    }
	}

	// Why must cram everything into one line?
	errMsg = "b1"
	b1, err! := Bar1()
```
### Other
Does not support closures, nesting, goto, top-level continue, top-level break, Label, etc., just a syntactic sugar to simplify repeated statements
