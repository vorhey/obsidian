```csharp
var chain = new ConsoleLogger(LogLevel.All)
    .SetNext(new EmailLogger(LogLevel.Error))
    .SetNext(new FileLogger(LogLevel.Warning));

chain.Message("Order retrieved.", LogLevel.Info);
chain.Message("Order not fulfilled", LogLevel.Error);
chain.Message("Order might be delayed", LogLevel.Warning);

[Flags]
public enum LogLevel
{
    None = 0,
    Info = 1,
    Debug = 2,
    Warning = 4,
    Error = 8,
    All = 15,
}

public abstract class Logger(LogLevel logLevel)
{
    private Logger? _next; // Linkedlist

    public Logger SetNext(Logger logger)
    {
        Logger last = this;
        while (last._next != null)
        {
            last = last._next;
        }
        last._next = logger;
        return this;
    }

    public void Message(string message, LogLevel severity)
    {
        var matchesLogLevel = (severity & logLevel) != 0;

        if (matchesLogLevel)
        {
            WriteMessage(message);
        }

        _next?.Message(message, severity);
    }

    protected abstract void WriteMessage(string message);
}

public class ConsoleLogger(LogLevel logLevel) : Logger(logLevel)
{
    protected override void WriteMessage(string message)
    {
        Console.WriteLine("Writing to console: " + message);
    }
}

public class EmailLogger(LogLevel logLevel) : Logger(logLevel)
{
    protected override void WriteMessage(string message)
    {
        Console.WriteLine("Sending via email:" + message);
    }
}

public class FileLogger(LogLevel logLevel) : Logger(logLevel)
{
    protected override void WriteMessage(string message)
    {
        Console.WriteLine("Writing to file: " + message);
    }
}
```