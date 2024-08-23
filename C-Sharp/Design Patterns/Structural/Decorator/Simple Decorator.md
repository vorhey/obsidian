```csharp
using System.Diagnostics;
using Microsoft.Extensions.Caching.Memory;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Sandbox;

var services = ConfigureServices();

static IServiceCollection ConfigureServices()
{
    var services = new ServiceCollection();
    services.AddLogging(options => options.AddConsole());
    services.AddMemoryCache();
    services.AddSingleton<LocationService>();
    services.AddSingleton<ILocationService>(sp =>
    {
        ILocationService locationService = sp.GetRequiredService<LocationService>();
        // Add Caching
        var caching = sp.GetRequiredService<IMemoryCache>();
        locationService = new CachingLocationService(locationService, caching);
        // Add Logging
        var logging = sp.GetRequiredService<ILogger<LoggingLocationService>>();
        locationService = new LoggingLocationService(locationService, logging);

        // Return Service
        return locationService;
    });
    services.AddTransient<App>();
    return services;
}

var serviceProvider = services.BuildServiceProvider();
serviceProvider.GetService<App>()!.Run();

namespace Sandbox
{
    public class App
    {
        private readonly ILocationService _locationService;

        public App(ILocationService locationService)
        {
            _locationService = locationService;
        }

        public void Run()
        {
            foreach (var kvp in _locationService.GetLocations())
                Console.WriteLine($"{kvp.Key}: {kvp.Value}");

            foreach (var kvp in _locationService.GetLocations())
                Console.WriteLine($"{kvp.Key}: {kvp.Value}");
        }
    }

    public interface ILocationService
    {
        Dictionary<int, string> GetLocations();
    }

    public class LocationService : ILocationService
    {
        private readonly Dictionary<int, string> _locations = new()
        {
            { 1, "Chile" },
            { 2, "Brazil" },
            { 3, "Colombia" }
        };

        public Dictionary<int, string> GetLocations()
        {
            Thread.Sleep(2500);
            return _locations;
        }
    }

    public class LoggingLocationService : ILocationService
    {
        private readonly ILocationService _locationService;
        private readonly ILogger<LoggingLocationService> _logger;

        public LoggingLocationService(
            ILocationService locationService,
            ILogger<LoggingLocationService> logger)
        {
            _locationService = locationService;
            _logger = logger;
        }

        public Dictionary<int, string> GetLocations()
        {
            _logger.LogWarning("Starting to get locations...");
            var sw = Stopwatch.StartNew();
            var locations = _locationService.GetLocations();
            sw.Stop();
            _logger.LogWarning(
                "Retrieved location data in: {ElapsedTime}ms",
                sw.ElapsedMilliseconds);
            return locations;
        }
    }

    public class CachingLocationService : ILocationService
    {
        private readonly ILocationService _locationService;
        private readonly IMemoryCache _memoryCache;

        public CachingLocationService(
            ILocationService locationService,
            IMemoryCache memoryCache)
        {
            _memoryCache = memoryCache;
            _locationService = locationService;
        }

        public Dictionary<int, string> GetLocations()
        {
            const string cacheKey = "Locations::faef89DWWD890";
            Console.WriteLine("Attempting to get cached results...");
            var memoryCacheExists = _memoryCache
                .TryGetValue(cacheKey, out Dictionary<int, string>? locations);
            if (memoryCacheExists)
            {
                Console.WriteLine("Returning cached results...");
                return locations!;
            }

            Console.WriteLine("Failed to get cached results...");
            locations = _locationService.GetLocations();
            Console.WriteLine("Adding locations to the cache...");
            _memoryCache.Set(cacheKey, locations, TimeSpan.FromMinutes(30));
            return locations;
        }
    }
}
```