# Primitives (Stored on stack)

```tsx
const _str: string = "Hello World"        // Text
const _float64: number = 42               // Numbers (integers and decimals)
const _bigInt: bigint = 9007199254740991n // Very large numbers
const _bool: boolean = true               // True/false values
const _undf: undefined = undefined        // Unassigned values
const _sym: symbol = Symbol('key')        // Unique identifiers
const _nil: null = null                   // Intentionally empty values
```

# Reference types (Stored on heap)

```tsx
// Objects
const obj: object = {                                 // Object literal
    name: "John",
    age: 30
}
// Arrays
const arr: Array<number> = [1, 2, 3]                  // Array (specialized object)
const tuple: [string, number] = ["a", 1]              // Tuple (TypeScript only)
// Functions
const func: Function = function() {}                  // Function object
const arrow: () => void = () => {}                    // Function Arrow function
function regular() {}                                 // Function declaration
// Built-in Objects
const date: Date = new Date()                         // Date object
const regex: RegExp = /pattern/                       // Regular Expression
const map: Map<string, any> = new Map()               // Map
const set: Set<number> = new Set()                    // Set
const weakMap: WeakMap<object, any> = new WeakMap()   // WeakMap
const weakSet: WeakSet<object> = new WeakSet()        // WeakSet
// Structured data
const json: JSON = JSON.parse('{"key": "value"}')     // JSON
const typedArray: Int32Array = new Int32Array(3)      // TypedArray
```

# TypeScript-specific (compile-time only)

## Compile-time only (disappear in JavaScript):

```tsx
interface User {                
    name: string;
    age: number;
}

type StringOrNumber = string | number;  // Union types are compile-time
type Coords = number & string;          // Intersection types are compile-time
type Point = { x: number, y: number }   // Type aliases are compile-time

```