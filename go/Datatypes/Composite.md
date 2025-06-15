```go
// array
var arr [3]int = [3]int{1,2,3}
// slices
var slice []int = []int{1,2,3}
// maps
var m map[string]int = map[string]int{
	"apple":  1,
	"banana": 2,
}
// structs
type Person struct {
	name string,
	age int32
}
var p Person = Person{"John", 30}
// pointers
var x int32 = 100
var xPtr *int32 = &x
// functions
var add func(int, int) int = func(a int, b int) int {
	return a + b
}
// channels
var ch chan int = make(chan int)
```