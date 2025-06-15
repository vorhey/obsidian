

# Factory simple
```csharp
var car = VehicleFactory.CreateVehicle(VehicleType.Car);
car.Start(); // Output: Car is starting

var motorcycle = VehicleFactory.CreateVehicle(VehicleType.Motorcycle);
motorcycle.Start(); // Output: Motorcycle is starting

public enum VehicleType
{
    Car,
    Motorcycle,
    Truck,
}

public interface IVehicle
{
    void Start();
    void Stop();
}

public class Car : IVehicle
{
    public void Start() => Console.WriteLine("Car is starting");

    public void Stop() => Console.WriteLine("Car is stopping");
}

public class Motorcycle : IVehicle
{
    public void Start() => Console.WriteLine("Motorcycle is starting");

    public void Stop() => Console.WriteLine("Motorcycle is stopping");
}

public class Truck : IVehicle
{
    public void Start() => Console.WriteLine("Truck is starting");

    public void Stop() => Console.WriteLine("Truck is stopping");
}

public class VehicleFactory
{
    public static IVehicle CreateVehicle(VehicleType type)
    {
        return type switch
        {
            VehicleType.Car => new Car(),
            VehicleType.Motorcycle => new Motorcycle(),
            VehicleType.Truck => new Truck(),
            _ => throw new ArgumentException("Invalid vehicle type", nameof(type)),
        };
    }
}
```
# Factory method

```csharp
var db = new DatabaseHandler(
    new MySQLFactory(),
    new DatabaseCredentials("George", "Hola123", "Primal")
);
db.Execute("SELECT * from TABLE");


public interface IDatabaseEngine
{
    void Connect();
    void Execute(string query);
    void Close();
}

public class MySQLEngine : IDatabaseEngine
{
    private readonly string _database;
    private readonly string _username;
    private readonly string _password;
    private readonly bool _ssl;
    private readonly string _connectionString;

    public MySQLEngine(string database, string username, string password, bool ssl)
    {
        _database = database;
        _username = username;
        _password = password;
        _ssl = ssl;
        _connectionString =
            $"Server=localhost;Database={_database};User ID={_username};Password={_password};";
    }

    public void Close()
    {
        Console.WriteLine("Closing MySQL connection.");
    }

    public void Connect()
    {
        Console.WriteLine($"Connecting to MySQL database '{_database}' with user '{_username}'.");
        Console.WriteLine($"SSL: {_ssl}");
        Console.WriteLine($"Connection string: {_connectionString}");
    }

    public void Execute(string query)
    {
        if (string.IsNullOrWhiteSpace(query))
        {
            throw new ArgumentException("Query cannot be null or empty.", nameof(query));
        }

        Console.WriteLine($"Executing query on MySQL database: {query}");
    }
}

public class PostgresSQLEngine : IDatabaseEngine
{
    private readonly string _database;
    private readonly string _username;
    private readonly string _password;
    private readonly string _connectionString;

    public PostgresSQLEngine(string database, string username, string password)
    {
        _database = database;
        _username = username;
        _password = password;
        _connectionString =
            $"Host=localhost;Database={_database};Username={_username};Password={_password};";
    }

    public void Close()
    {
        Console.WriteLine("Closing PostgreSQL connection.");
    }

    public void Connect()
    {
        Console.WriteLine(
            $"Connecting to PostgreSQL database '{_database}' with user '{_username}'."
        );
        Console.WriteLine($"Connection string: {_connectionString}");
    }

    public void Execute(string query)
    {
        if (string.IsNullOrWhiteSpace(query))
        {
            throw new ArgumentException("Query cannot be null or empty.", nameof(query));
        }

        Console.WriteLine($"Executing query on PostgreSQL database: {query}");
    }
}

// Each factory creates ONE type of product (IDatabaseEngine)
public abstract class DatabaseFactory
{
    public abstract IDatabaseEngine Create(string database, string username, string password);
}

public class MySQLFactory : DatabaseFactory
{
    public override IDatabaseEngine Create(string database, string username, string password)
    {
        return new MySQLEngine(database, username, password, true);
    }
}

public class PostgreSQL : DatabaseFactory
{
    public override IDatabaseEngine Create(string database, string username, string password)
    {
        return new PostgresSQLEngine(database, username, password);
    }
}

public enum DatabaseType
{
    MySQL,
    PostgreSQL,
}

// Client code
public record DatabaseCredentials(string Username, string Password, string Database);

public class DatabaseHandler(DatabaseFactory databaseFactory, DatabaseCredentials credentials)
{
    public void Execute(string query)
    {
        var engine = databaseFactory.Create(
            credentials.Username,
            credentials.Password,
            credentials.Database
        );
        engine.Connect();
        engine.Execute(query);
        engine.Close();
    }
}
```