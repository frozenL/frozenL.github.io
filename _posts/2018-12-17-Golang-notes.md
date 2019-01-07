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
      
## data structure
- array: building block for slices
  - e.g. `var x [5]int`
  - the length is part of the array's type, i.e. `var x [5]int` and `var x[6]int` are different types
  ```golang
  x := [1]int{1}
  fmt.Printf("%T\n", x) // [1]int
  ```
- slice: composite data type
  - e.g. `x := []int{4,5,6,7}`
  - length: `len(x)`
  - loop over the values in slices:
  ```golang
  x := []int{1,2,3,4,5}
  for i, v := range(x) {}
  ```
  - slicing a slice
    - print: `fmt.Println(x[1:])`
    - upto but not including: `fmt.Println(x[1:3])`
  - append to a slice
    - signature: `func append(slice []T, elements ...T) []T`
    - e.g.
    ```golang
    x = append(x, 4, 5, 6, 7)
    // append a slice to a slice
    y := []int{4,5,6}
    x = append(x, y...) // only works for one y...
    ```
  - delete from a slice
    - sol.1:
    ```golang
    x = append(x[:2], x[4:]...)
    ```
  - make:
  ```golang
  x := make([]int, 10, 100) // capacity = 100
  x[10] = 1 // index out of range
  x = append(x, 111) // works
  ```
  - multi-dimensional slice
  ```golang
  a := []string{"a", "b"}
  b := []string{"a", "b"}
  x := [][]string{a, b}
  ```
- map
  - e.g.
  ```golang
  m := map[string]bool {
    "a": true,
  }
  fmt.Println(m["b"]) // false
  ```
  - exists
  ```golang
  v, ok := m["b"] // v = false, ok = false
  ```
  - add elements
  ```golang
  m["todd"] = 33
  ```
  - range
  ```golang
  for k, v := range m {}
  ```
  - delete: 
    - e.g. `delete(m, "a")`
    - can also delete something that doesn't exist

- [struct](https://golang.org/ref/spec#Struct_types)
  - type:
  ```golang
  type person struct {
    age int
    x, y string // can be in the same line
  }
  ```
  - a value of type person:
  ```golang
  p2 := person {
    age: 17,
  }
  ```
  - embedded/anonymous field
  ```golang
  type secretAgent struct {
    person
    ltk bool
  }
  ```
    - the unqualified type name acts as the field name
    ```golang
    sa1 := secretAgent{
      person: person{
        age: 17,
      },
      ltk: true,
    }

    fmt.Println(sa1.age, sa1.ltk) // the inner fields of person got promoted up to the higher level
    ```
    - if we have a name collision, the outer field is used by default
  - anonymous structs
  ```golang
  p := struct {
    age int
  }{
    age: 17,
  }
  ```
## functions
everything in Go is pass by value
- signature
```golang
func (r receiver) identifier (parameter(s)) (return(s)) { code }
```
- variadic parameters / function

the one has to be the last parameter
```golang
func foo(x ...int) {
  fmt.Printf("%T\n", x) // []int
}
```
  - unfurling a slice `...`
  ```golang
  xi := []int{1,2,3,4,5}
  x := foo(xi) // types don't match
  x := foo(xi...) // OK
  y := foo() // OK
  ```
- `defer`: invokes a function whose execution is deferred to the moment the surrounding function returns
- methods: in a struct
```golang
  type secretAgent struct {}
  func (s secretAgent) speak() {}
```
- [interface & polymorphism](https://golang.org/ref/spec#Interface_types)
  
  a value can be of more than one type. if an object has all the methods defined in an interface, it's of that type.
  - ```golang
  type human interface {
    speak()
  }
  ```
  - check type
  ```golang
  switch h.(type) {
    case person:
      ... // h.(person).first
    case secretAgent:
      ... // h.(secreteAgnet).first
  }
  ```
  - e.g.
  ```golang
  func Println(a ...interface{}) (n int, err error)
  ```
- anonymous func
```golang
func() {
  fmt.Println("Anonymous func ran")
}()
```
- func expression
```golang
f := func() {}
f()
```
- returning a func
```golang
func foo() func() int {
  return func() int {
    return 1
  }
}
```
- callback: passing a `func` as an argument
- closure: limiting scope, e.g. the scope of variables
- recursion: nothing special

## pointers
```golang
a := 42
fmt.Printf("%T\n", a) // int
fmt.Printf("%T\n", &a) // *int
fmt.Println(*&a) // 42
*&a = 43
fmt.Println(a) // 43
```
- pass pointers
```golang
func foo(y *int) {
  *y = 43
}
```
- [method sets](https://github.com/golang/go/wiki/MethodSets)
  > The method set of any other named type T consists of all methods with receiver type T. The method set of the corresponding pointer type *T is the set of all methods with receiver *T or T (that is, it also contains the method set of T).
  
  come back later [7th Jan. 2019]

## packages
### encoding
- [marshal](https://godoc.org/encoding/json#Marshal)
  ```golang
  package encoding
  func Marshal(v interface{})([]byte, error)
  ```
- [unmarshal](https://godoc.org/encoding/json#Unmarshal)
  ```golang
  func Unmarshal(data []byte, v interface{}) error
  var jsonBlob = []byte(`[
    {"Name": "Platypus", "Order": "Monotremata"},
    {"Name": "Quoll",    "Order": "Dasyuromorphia"}
  ]`) // the marshaled thing
  type Animal struct {
    Name  string
    Order string
  }
  var animals []Animal
  err := json.Unmarshal(jsonBlob, &animals)
  ```
- Encode / Decode: convert JSON into a stream and vice versa

### io
- Writer:
```golang
type Writer interface {
    Write(p []byte) (n int, err error)
}
```
  - e.g.
  ```golang
  func (f *File) Write(b []byte) (n int, err error)
  ```
- Reader:
```golang
type Reader interface {
    Read(p []byte) (n int, err error)
}
```
### fmt
- `Fprintln`
```golang
fmt.Println("Hello")
fmt.Fprintln(os.Stdout, "Hello") // alternative 1
io.WriteString(os.Stdout, "Hello") // alternative 2
```

### sort
- `Ints`
```golang
s := []int{6, 3, 7, 9}
sort.Ints(s) // no assignments
fmt.Println(s)
```
- `Strings`: similar
- `Sort`: custom
copied from godoc.org
```golang
type Person struct {
    Name string
    Age  int
}

func (p Person) String() string {
    return fmt.Sprintf("%s: %d", p.Name, p.Age)
}

// ByAge implements sort.Interface for []Person based on
// the Age field.
type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }

func main() {
    people := []Person{
        {"Bob", 31},
        {"John", 42},
        {"Michael", 17},
        {"Jenny", 26},
    }

    fmt.Println(people)
    // There are two ways to sort a slice. First, one can define
    // a set of methods for the slice type, as with ByAge, and
    // call sort.Sort. In this first example we use that technique.
    sort.Sort(ByAge(people)) // conversion
    fmt.Println(people)

    // The other way is to use sort.Slice with a custom Less
    // function, which can be provided as a closure. In this
    // case no methods are needed. (And if they exist, they
    // are ignored.) Here we re-sort in reverse order: compare
    // the closure with ByAge.Less.
    sort.Slice(people, func(i, j int) bool {
        return people[i].Age > people[j].Age
    })
    fmt.Println(people)
}
```
### bcrypt
- `x/`: things haven't been migrated to the standard library yet
- `GenerateFromPassword`
```golang
s := `pwd`
bs, err := bcrypt.GenerateFromPassword([]byte(s), bcrypt.MinCost) // 
```
- `CompareHashAndPassword`
```golang
err := bcrypt.CompareHashAndPassword(bs, []byte(s))
```

## questions?
- shadow
- method sets
