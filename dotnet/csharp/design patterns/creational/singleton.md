```csharp
// First access - creates the instance
var db1 = DatabaseManager.Instance;
db1.Connect();

// Second access - returns the same instance
var db2 = DatabaseManager.Instance;
db2.ExecuteQuery("SELECT * FROM Users");

// Verify it's the same instance
Console.WriteLine($"Same instance: {ReferenceEquals(db1, db2)}");

public sealed class DatabaseManager
{
    // Lazy<T> ensures thread-safe, lazy initialization
    private static readonly Lazy<DatabaseManager> _instance = 
        new Lazy<DatabaseManager>(() => new DatabaseManager());

    // Private constructor prevents direct instantiation
    private DatabaseManager()
    {
        // Initialize your singleton here
        ConnectionString = "Server=localhost;Database=MyApp;";
        Console.WriteLine("DatabaseManager instance created");
    }

    // Public property to access the singleton instance
    public static DatabaseManager Instance => _instance.Value;

    // Example properties/methods
    public string ConnectionString { get; private set; }

    public void Connect()
    {
        Console.WriteLine($"Connecting to database: {ConnectionString}");
    }

    public void ExecuteQuery(string query)
    {
        Console.WriteLine($"Executing query: {query}");
    }
}
```