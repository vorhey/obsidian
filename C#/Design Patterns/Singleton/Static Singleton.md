```csharp
namespace Sandbox
{
    public class App
    {
        public void Run(string[] args)
        {
            var logger = Logger.Instance;
            logger.Log("Hello world");
        }
    }

    public sealed class Logger
    {
        private string _log = "log.txt";
        private static Logger s_instance = default!;
        private static readonly object s_padlock = new();

        private Logger() { }

        public static Logger Instance
        {
            get
            {
                if (s_instance is null)
                {
                    lock (s_padlock)
                    {
                        if (s_instance is null)
                        {
                            s_instance = new();
                        }
                    }
                }
                return s_instance;
            }
        }

        public void Log(string message)
        {
            var entry = $"{DateTime.Now}: {message}";
            Console.WriteLine(entry);
            File.AppendAllText(_log, entry + Environment.NewLine);
        }
    }
}

```