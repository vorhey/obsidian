- Multithreading is the technique of running **multiple threads** within a single process
- Each thread can execute code independently.

```csharp
Thread thread1 = new(() => ProcessData("Thread 1", 1000));
Thread thread2 = new(() => ProcessData("Thread 2", 1500));
Thread thread3 = new(() => ProcessData("Thread 3", 800));

thread1.Start();
thread2.Start();
thread3.Start();

// Wait for all threads to complete
thread1.Join();
thread2.Join();
thread3.Join();

Console.WriteLine("All threads completed");

static void ProcessData(string threadName, int delay)
{
    Console.WriteLine($"{threadName} started on Thread ID: {Environment.CurrentManagedThreadId}");
    Thread.Sleep(delay); // Simulate work
    Console.WriteLine($"{threadName} completed");
}
```