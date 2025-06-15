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

# Factory abstract

```csharp
// Abstract products - the things we want to create
public abstract class Document
{
    public abstract void Open();
    public abstract void Save();
}

public abstract class Image
{
    public abstract void Display();
    public abstract void Resize(int width, int height);
}

// PDF family of products
public class PDFDocument : Document
{
    public override void Open()
    {
        Console.WriteLine("Opening PDF document with Adobe Reader");
    }

    public override void Save()
    {
        Console.WriteLine("Saving as PDF format");
    }
}

public class PDFImage : Image
{
    public override void Display()
    {
        Console.WriteLine("Displaying PDF embedded image");
    }

    public override void Resize(int width, int height)
    {
        Console.WriteLine($"Resizing PDF image to {width}x{height}");
    }
}

// Word family of products
public class WordDocument : Document
{
    public override void Open()
    {
        Console.WriteLine("Opening Word document with Microsoft Word");
    }

    public override void Save()
    {
        Console.WriteLine("Saving as DOCX format");
    }
}

public class WordImage : Image
{
    public override void Display()
    {
        Console.WriteLine("Displaying Word embedded image");
    }

    public override void Resize(int width, int height)
    {
        Console.WriteLine($"Resizing Word image to {width}x{height}");
    }
}

// Abstract Factory - creates families of related products
public abstract class DocumentFactory
{
    public abstract Document CreateDocument();
    public abstract Image CreateImage();
}

// Concrete factories - each creates a family of related products
public class PDFFactory : DocumentFactory
{
    public override Document CreateDocument()
    {
        return new PDFDocument();
    }

    public override Image CreateImage()
    {
        return new PDFImage();
    }
}

public class WordFactory : DocumentFactory
{
    public override Document CreateDocument()
    {
        return new WordDocument();
    }

    public override Image CreateImage()
    {
        return new WordImage();
    }
}

// Client code
public class DocumentProcessor
{
    private readonly DocumentFactory _factory;

    public DocumentProcessor(DocumentFactory factory)
    {
        _factory = factory;
    }

    public void ProcessDocument()
    {
        // Create related products from the same family
        Document doc = _factory.CreateDocument();
        Image img = _factory.CreateImage();

        // Use the products together
        doc.Open();
        img.Display();
        img.Resize(800, 600);
        doc.Save();
    }
}

// Usage
class Program
{
    static void Main()
    {
        Console.WriteLine("=== Processing PDF Document ===");
        var pdfProcessor = new DocumentProcessor(new PDFFactory());
        pdfProcessor.ProcessDocument();

        Console.WriteLine("\n=== Processing Word Document ===");
        var wordProcessor = new DocumentProcessor(new WordFactory());
        wordProcessor.ProcessDocument();
    }
}

```