
# Simple
```csharp
var chain = new TierOneSupport();
var tierTwo = new TierTwoSupport();
var tierThree = new TierThreeSupport();

chain.SetNext(tierTwo);
tierTwo.SetNext(tierThree);

var simpleIssue = new CustomerIssue("Can't log into account", 2);
var moderateIssue = new CustomerIssue("Software crashes on startup", 5);
var complexIssue = new CustomerIssue("Data corruption in database", 9);

List<CustomerIssue> issues = [simpleIssue, moderateIssue, complexIssue];

foreach (var issue in issues)
{
    chain.Handle(issue);
}

public record CustomerIssue(string Description, int Severity);

public abstract class SupportHandler
{
    private SupportHandler? _next;

    public void SetNext(SupportHandler handler)
    {
        _next = handler;
    }

    public void Handle(CustomerIssue issue)
    {
        HandleRequest(issue);
        _next?.Handle(issue);
    }

    protected abstract void HandleRequest(CustomerIssue issue);
}

public class TierOneSupport : SupportHandler
{
    protected override void HandleRequest(CustomerIssue issue)
    {
        if (issue.Severity <= 3)
        {
            Console.WriteLine($"Tier One Support handling issue: {issue.Description}");
        }
    }
}

public class TierTwoSupport : SupportHandler
{
    protected override void HandleRequest(CustomerIssue issue)
    {
        if (issue.Severity >= 3 && issue.Severity <= 7)
        {
            Console.WriteLine($"Tier Two Support handling issue: {issue.Description}");
        }
    }
}

public class TierThreeSupport : SupportHandler
{
    protected override void HandleRequest(CustomerIssue issue)
    {
        if (issue.Severity > 7)
        {
            Console.WriteLine($"Tier Three Support handling issue: {issue.Description}");
        }
    }
}

```

# Fluent
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