- It's a high-level abstraction built on top of multithreading.
- Involves dividing work among multiple threads that run concurrently.
- Aims to maximize the use of multiple processor cores.
- Uses multithreading as a mechanism to achieve parallelism.
- Parallel programming should be used any time you have a fair amount of computation work that can be split up into independent chunks.

```csharp
// Example 1: Parallel.For
Console.WriteLine("Parallel.For example:");
var numbers = Enumerable.Range(1, 1000000).ToArray();
var results = new long[numbers.Length];

Parallel.For(
    0,
    numbers.Length,
    i =>
    {
        results[i] = ExpensiveCalculation(numbers[i]);
    }
);

Console.WriteLine($"Processed {numbers.Length} items");

// Example 2: Parallel.ForEach
Console.WriteLine("\nParallel.ForEach example:");
var items = new[] { "File1.txt", "File2.txt", "File3.txt", "File4.txt" };

Parallel.ForEach(
    items,
    fileName =>
    {
        ArgumentException.ThrowIfNullOrEmpty(fileName);
        ProcessFile(fileName);
    }
);

// Example 3: PLINQ
Console.WriteLine("\nPLINQ example:");
var largeNumbers = Enumerable.Range(1, 1000000);
var evenSquares = largeNumbers
    .AsParallel()
    .Where(x => x % 2 == 0)
    .Select(x => x * x)
    .Take(10)
    .ToArray();

Console.WriteLine($"First 10 even squares: {string.Join(", ", evenSquares)}");

// Example 4: Task-based parallelism
Console.WriteLine("\nTask-based parallelism:");
var tasks = new[]
{
    Task.Run(() => ProcessData("Task 1", 1000)),
    Task.Run(() => ProcessData("Task 2", 1500)),
    Task.Run(() => ProcessData("Task 3", 800)),
};

Task.WaitAll(tasks);
Console.WriteLine("All tasks completed");

static long ExpensiveCalculation(int number)
{
    // Simulate CPU-intensive work
    long result = 0;
    for (int i = 0; i < number % 1000; i++)
    {
        result += i * i;
    }
    return result;
}

static void ProcessFile(string fileName)
{
    Console.WriteLine($"Processing {fileName} on Thread ID: {Task.CurrentId}");
    Task.Delay(500).Wait(); // Simulate file processing
    Console.WriteLine($"Completed {fileName}");
}

static void ProcessData(string taskName, int delay)
{
    Console.WriteLine($"{taskName} started on Thread ID: {Task.CurrentId}");
    Task.Delay(delay).Wait(); // Simulate work
    Console.WriteLine($"{taskName} completed");
}

```