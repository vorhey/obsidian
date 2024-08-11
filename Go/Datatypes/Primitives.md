```go
// byte
var _byte byte = 255
// short
var _short int16 = 32767
// integer
var _integer int32 = 2147483647
// long
var _long int64 = 9223372036854775807
// float
var _float float32 = math.MaxFloat32
// double
var _double float64 = math.MaxFloat64
// bool
var _bool bool = true
// char|rune (alias for int32)
var _rune rune = 'a' // output: 97
// string (immutable sequence of bytes)
var str string = "go" // immutable sequences of bytes
var bytes []byte = []byte{72, 101, 108, 108, 111} // byte example
var s string = string(bytes) // bytes to string
```
