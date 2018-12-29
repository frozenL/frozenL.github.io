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
    - map: "Map types are reference types, like pointers or slices, and so the value of m above is nil; it doesn't point to an initialized map. A nil map behaves like an empty map when reading, but attempts to write to a nil map will cause a runtime panic; don't do that.": [go maps](https://blog.golang.org/go-maps-in-action)
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
    - is a slice of bytes, i.e. 
    ```golang
    s := "Hello world"
    bs := []byte(s)
    ```
    - [`%U`](https://godoc.org/fmt)
    ```golang
    fmt.Printf("%#U\n", 'x') // prints U+0078 'x'
    fmt.Printf("%U\n", 'x') // prints U+0078, UTF8 code
    ```
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
  - numeric types
    - `byte` == `uint8`
    - `rune` == `int32`
  - underlying type: each type T has an underlying type, if T is one of the predeclared boolean, numeric, or string types, or a type literal, the corresponding underlying type is T itself. Otherwise, T's underlying type is the underlying type of the type to which T refers in its type declaration. i.e. recursion
  - constants
    - typed constant
    ```golang
    const (
      a int = 42
      b float64 = 42.78
    )
    ```
    - untyped constant
    ```golang
    const (
      a = 42
      b = 42.78
    )
    ```
    - [`iota`](https://golang.org/ref/spec#Iota)
      - Within a constant declaration, its value is the index of the respective [ConstSpec](https://golang.org/ref/spec#ConstSpec), starting at zero. Multiple uses of iota in the same ConstSpec all have the same value.
      - implicit repitition can be used
      
- `fmt`
  - the verbs:
    - `%v`: the value in a default format
    - `%+v`: when printing structs, adds field names
    - `$#v`: a Go-syntax representation of the value
    - `%%`: a literal percent sign; consumes no value
    
- control flow
  - loop: no `while`
    - format
    ```golang
    for init; cond; post {}
    for cond { S() }
    for ; cond; { S() }
    ```
    - `while` equivalent: `for {}`
  - conditional
    - `if true {}`
    - only one assignment: `if x := 42; x == 2 {}`
    - `switch`
      - switch on nothing:
      ```golang
      switch {
      case false:
        fmt.Println("1") // won't print
      case (2 == 4):
        fmt.Println("2") // won't print
      case true:
        fmt.Println("3") // prints
      case (3 == 3):
        fmt.Println("4") // won't print
      }
      ```
      - `fallthrough`, evaluate the next case too
      ```golang
      switch {
      case false:
        fmt.Println("1") // won't print
      case (2 == 4):
        fmt.Println("2") // won't print
      case true:
        fmt.Println("3") // prints
        fallthrough
      case (3 == 3):
        fmt.Println("4") // also prints
      case (5 == 5):
        fmt.Println("5") // won't print
      }
      ```
      - `default:` will run when no case is evaulated to true. Can be put at any place.
      - switch on value:
      ```golang
      switch "Bond" {
      ... // same
      }
      ```
      - can be presented in comma-separated lists:
      ```golang
      switch c {
      case ' ', '?', '=':
        fmt.Println("true")
      }
      ```
      

**?** shadow
