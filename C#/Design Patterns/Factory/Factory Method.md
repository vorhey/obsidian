```csharp

using Microsoft.Extensions.DependencyInjection;
using Sandbox;

static IServiceProvider BuildServiceProvider()
{
    var services = new ServiceCollection();
    services.AddSingleton<App>();
    return services.BuildServiceProvider();
}

BuildServiceProvider().GetRequiredService<App>().Run(args);

namespace Sandbox
{
    public class App
    {
        public void Run(string[] args)
        {
            DatabaseEngine dbEngine1 = new SqlServerEngine();
            dbEngine1.ProcessData("Query to process data");

            DatabaseEngine dbEngine2 = new OracleEngine();
            dbEngine1.ProcessData("Query to process data");
        }
    }

    // Product Interface
    public interface IDatabase
    {
        void Connect();
        void ExecuteQuery(string query);
    }

    // Concrete Product: SQL Server
    public class SqlServerDatabase : IDatabase
    {
        public void Connect()
        {
            Console.WriteLine("Connecting to SQL Server database...");
        }

        public void ExecuteQuery(string query)
        {
            Console.WriteLine("Executing query in SQL Server database: " + query);
        }
    }

    // Concrete Product: Oracle
    public class OracleDatabase : IDatabase
    {
        public void Connect()
        {
            Console.WriteLine("Connecting to Oracle database...");
        }

        public void ExecuteQuery(string query)
        {
            Console.WriteLine("Executing query in Oracle database: " + query);
        }
    }

    // Abstract Creator
    public abstract class DatabaseEngine
    {
        public abstract IDatabase CreateDatabase();

        public void ProcessData(string query)
        {
            IDatabase database = CreateDatabase();
            database.Connect();
            database.ExecuteQuery(query);
        }
    }

    // Concrete Creator 1: SQL Server
    public class SqlServerEngine : DatabaseEngine
    {
        public override IDatabase CreateDatabase()
        {
            return new SqlServerDatabase();
        }
    }

    // Concrete Creator 2: Oracle
    public class OracleEngine : DatabaseEngine
    {
        public override IDatabase CreateDatabase()
        {
            return new OracleDatabase();
        }
    }

}
```