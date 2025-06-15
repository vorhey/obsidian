# Value types (Stored on stack)

```csharp
// Primitives
byte a = 255;
short b = 32767;
int c = 2147483647;
long d = 9223372036854775807;
float e = 3.14F;
double f = 3.14;
char h = 'a';
bool i = true;

struct Coords { public int X, Y;} // struct
enum Level { Low, Medium, High } // enum
decimal g = 3.14M; // decimal (defined as a struct)
(double, int) t1 = (4.5, 3); // tuples
```

# Reference types (Stored on heap)

```csharp
class Person {} // class
delegate int PerformCalculation(int x, int y); // delegate
interface IShape {} // interface
dynamic dyn = 1; // dynamic
int[] arr = new int[5]; // array (single-dimension)
int[,] array2D = new int[3, 2] // rectangular array (multi-dimension)
{
  { 4, 5 },
  { 5, 0 },
  { 3, 1 }
};
int[][] matrix = new int[3][] // jagged array (jagged)
{
    new int[] {0,1,2},
    new int[] {3,4,5,6},
    new int[] {7,8,9,10,11},
};
```

# String type

```csharp
string str = "hello"
```

# Record type

```csharp
public record Person(string Name, int Age); // (value-type)
public record struct Point(int X, int Y); // (reference-type)
```