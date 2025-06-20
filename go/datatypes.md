# Primitives

```go
var _byte byte = 255 // byte
var _short int16 = 32767 // short
var _int int32 = 2147483647 // integer
var _long int64 = 9223372036854775807 // long
var _float float32 = math.MaxFloat32 // float
var _double float64 = math.MaxFloat64 // double
var _bool bool = true // bool
var _rune rune = 'a' // char|rune (alias for int32) => output: 97
```

# Composites

```go
var arr [3]int = [3]int{1,2,3} // array
var slice []int = []int{1,2,3} // slices
var m map[string]int = map[string]int{ // maps
	"apple":  1,
	"banana": 2,
}
type Person struct { // structs
	name string,
	age int32
}
var p Person = Person{"John", 30}
var x int32 = 100 // pointers
var xPtr *int32 = &x
var add func(int, int) int = func(a int, b int) int { // functions
	return a + b
}
var ch chan int = make(chan int) // channels
```

# String type

```go
var str string = "go" // string (immutable sequences of bytes)
var bytes []byte = []byte{72, 101, 108, 108, 111} // byte example
var s string = string(bytes) // bytes to string
```