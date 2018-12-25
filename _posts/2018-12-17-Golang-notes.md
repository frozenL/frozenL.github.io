---
layout: post
title: Golang notes
date: 2018-12-17
---

Golang

- control flow
  - sequence
  - loop; iterative
  - conditional
  
- variadic function: one which accepts a variable number of arguments
  - `func sum(nums ...int)`
  - `func Println(a ...interface{})`: e.g. `fmt.Println(1, 2, 3)`

- short declaration operator: `:=`

- the `var` keyword: `x := 1` vs `var x = 1`
  - `var x = 1` can be used outside function bodies while `x := 1` can't
  
- the zero value
  - when storage is allocated for a variable, either through a declaration or a call of `new`, or when a new value is created, either through a composite literal or a call of `make`, and no explicit initialisation is provided, the variable or value is given a default value
  - the initialisation is done recursively
  - booleans: `false`
  - integers/floats: `0`
  - pointers, functions, interfaces, slices, channels, maps: `nil`
  - `var i int` == `var i int = 0`
  
- types
  - check types: 
  ```golang
  y := 42
  fmt.Printf("%T\n", y)
  ```
  - string: [golang spec: string literals](https://golang.org/ref/spec#String_literals)
    - double quotes: `"hey"`
    - backticks:
      - `` var a string = `he says: "hey"` ``
      - can do multiple lines
  - create your own type: e.g. 
  ```golang
  var a int
  type hotdog int
  var b hotdog
  
  func main() {
    a = 1
    b = 2
    a = b // error: `cannot use b (type hotdog) as type int in assignment`
    a = int(b) // conversion works
  }
  ```
  - underlying type: each type T has an underlying type, if T is one of the predeclared boolean, numeric, or string types, or a type literal, the corresponding underlying type is T itself. Otherwise, T's underlying type is the underlying type of the type to which T refers in its type declaration. i.e. recursion
- `fmt`
  - the verbs:
    - `%v`: the value in a default format
    - `%+v`: when printing structs, adds field names
    - `$#v`: a Go-syntax representation of the value
    - `%%`: a literal percent sign; consumes no value
    
**?** shadow
