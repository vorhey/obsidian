%% Begin Waypoint %%
```csharp
using System;
using System.Collections;
using System.Collections.Generic;

namespace SandboxConsole
{
    public class Program
    {
        static void Main(string[] args)
        {
            var numbers = new GenericList<int>();
            
            numbers.Add(1, 2, 3);
            
            Console.WriteLine("Numbers in position 0: " + numbers[0]);
            Console.WriteLine("Numbers in position 1: " + numbers[1]);
            Console.WriteLine("Numbers in position 2: " + numbers[2]);


            var dict = new KeyValuePair<int, string>
            {
                Key = 1,
                Value = "Hello"
            };

            Console.WriteLine($"{dict}");
        }

    }

    public class GenericList<T>
    {
        private T[] _obj = new T[10];
        
        public void Add(params T[] value)
        {
            _obj = value;
        }

        public T this[int index]
        {
            get => _obj[index];
        }
    }

    public class KeyValuePair<TKey, TValue>
    {
        public TKey Key { get; set; }
        
        public TValue Value { get; set; }

        public override string ToString()
        {
            return $"Current Key: {Key}, Current Value: {Value}";
        }

    }
}

```
%% End Waypoint %%