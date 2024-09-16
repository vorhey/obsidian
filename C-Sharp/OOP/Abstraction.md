```csharp
using System;

namespace SandboxConsole
{
    public partial class Program
    {
        static void Main(string[] args)
        {
            // var command = new DbCommand(new OracleConnection("jdbc://user@password:oraclethin"), "SELECT * FROM users");
            var command = new DbCommand(new SqlConnection("mssql://user@password?database=Customers"), "SELECT * FROM users");

            command.Execute();

            Console.ReadKey();
        }
    }

    public abstract class DbConnection
    {
        public string ConnectionString { get; set; }
        public TimeSpan Timeout { get; set; }

        public DbConnection(string connectionString)
        {
            if (string.IsNullOrWhiteSpace(connectionString))
                throw new ArgumentException("The connection string is either null or empty", nameof(connectionString));
            ConnectionString = connectionString;
        }

        public abstract void Open();
        public abstract void Close();
    }

    public class SqlConnection : DbConnection
    {
        private readonly TimeSpan _timeout = new(0, 1, 0);
        public SqlConnection(string connectionString) : base(connectionString)
        {
            Timeout = _timeout;
        }
        public override void Close()
        {
            Console.WriteLine("Closing connection...");
        }

        public override void Open()
        {
            Console.WriteLine("Opening connection using: " + ConnectionString);
        }
    }

    public class OracleConnection : DbConnection
    {
        private readonly TimeSpan _timeout = new(0, 2, 0);

        public OracleConnection(string connectionString) : base(connectionString)
        {
            Timeout = _timeout;
        }
        public override void Close()
        {
            Console.WriteLine("Closing connection...");
        }

        public override void Open()
        {
            Console.WriteLine("Opening connection using: " + ConnectionString);
        }
    }

    public class DbCommand
    {
        private readonly DbConnection _dbConnection;
        private readonly string _instruction;

        public DbCommand(DbConnection dbConnection, string instruction)
        {
            _dbConnection = dbConnection ?? 
                throw new ArgumentNullException(nameof(dbConnection), "You must pass an instance of a Database Connection");
            if (string.IsNullOrWhiteSpace(instruction))
                throw new ArgumentException("The SQL command instruction can't be null or empty", nameof(instruction));
            _instruction = instruction;
        }

        public void Execute()
        {
            // Open Connection
            _dbConnection.Open();

            // Run Instruction
            Console.WriteLine("Running instruction: " + _instruction);

            // Close Connection
            _dbConnection.Close();
        }
    }

}
```