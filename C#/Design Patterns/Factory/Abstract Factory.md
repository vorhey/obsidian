```csharp
using Microsoft.Extensions.DependencyInjection;
using Sandbox;

static IServiceProvider BuildServiceProvider()
{
    var services = new ServiceCollection();
    services.AddSingleton<App>();
    services.AddSingleton<FedexShippingService>();
    services.AddSingleton<UpsShippingService>();
    services.AddSingleton<IInventoryService, InventoryService>();
    services.AddSingleton<INotificationService, NotificationService>();
    services.AddSingleton<Func<string, ShippingServiceType, IStore>>(sp => (string name, ShippingServiceType shippingServiceType) =>
    {
        IShippingService shippingService = shippingServiceType switch
        {
            ShippingServiceType.Fedex => sp.GetRequiredService<FedexShippingService>(),
            ShippingServiceType.Ups => sp.GetRequiredService<UpsShippingService>(),
            _ => throw new ArgumentOutOfRangeException(nameof(shippingServiceType), $"Not expected shipping value: {shippingServiceType}")
        };
        IInventoryService inventoryService = sp.GetRequiredService<IInventoryService>();
        INotificationService notificationService = sp.GetRequiredService<INotificationService>();

        return new ShippingStore(name, shippingService, inventoryService, notificationService);
    });
    services.AddSingleton<IShippingStoreFactory, ShippingStoreFactory>();
    services.AddSingleton<Func<string, IStore>>(sp => name => new PickupStore(name));
    services.AddSingleton<IPickupStoreFactory, PickupStoreFactory>();
    return services.BuildServiceProvider();
}

BuildServiceProvider().GetRequiredService<App>().Run(args);

namespace Sandbox
{
    public class App(IShippingStoreFactory shippingStoreFactory, IPickupStoreFactory pickupStoreFactory)
    {
        public void Run(string[] args)
        {
            var s1 = shippingStoreFactory.CreateShippingStore("Amazon MEX", ShippingServiceType.Fedex);
            var s2 = pickupStoreFactory.CreatePickupStore("Dallas");
            s1.OrderItem("george", "pizza");
            // s2.OrderItem("peter", "pizza");
        }
    }

    public interface IShippingService
    {
        void ProcessOrder(string buyerName, string itemName);
    }

    public enum ShippingServiceType
    {
        Fedex,
        Ups
    }

    public class UpsShippingService : IShippingService
    {
        public void ProcessOrder(string buyerName, string itemName)
        {
            Console.WriteLine($"Processing order from {buyerName} for 1 {itemName} through UPS shipping.");
        }
    }

    public class FedexShippingService : IShippingService
    {
        public void ProcessOrder(string buyerName, string itemName)
        {
            Console.WriteLine($"Processing order from {buyerName} for 1 {itemName} through Fedex shipping.");
        }
    }

    public interface IStore
    {
        void OrderItem(string buyerName, string itemName);
    }

    public enum StoreType
    {
        Shipping,
        Pickup
    }

    public class ShippingStore(string name, IShippingService shippingService, IInventoryService inventoryService, INotificationService notificationService) : IStore
    {
        public void OrderItem(string buyerName, string itemName)
        {
            Console.WriteLine($"'{name}' received an order!");
            Console.WriteLine("Verifying order.");
            Thread.Sleep(500);
            if (inventoryService.CheckInventory(itemName))
            {
                shippingService.ProcessOrder(buyerName, itemName);
                inventoryService.UpdateInventory(itemName);
                Console.WriteLine("Order complete.");
                notificationService.Notify(buyerName, $"Your order for {itemName} has been processed and is on its way!");
            }
            else
            {
                Console.WriteLine("Order failed. Item is out of stock.");
                notificationService.Notify(buyerName, $"Your order for {itemName} could not be processed due to insufficient stock.");
            }
        }
    }

    public interface IInventoryService
    {
        bool CheckInventory(string itemName);
        void UpdateInventory(string itemName);
    }

    public class InventoryService : IInventoryService
    {
        public bool CheckInventory(string itemName)
        {
            return true;
        }

        public void UpdateInventory(string itemName)
        {
            Console.WriteLine($"Inventory updated for {itemName}.");
        }
    }

    public interface INotificationService
    {
        void Notify(string buyerName, string message);
    }

    public class NotificationService : INotificationService
    {
        public void Notify(string buyerName, string message)
        {
            Console.WriteLine($"Notification sent to {buyerName}: {message}");
        }
    }

    public class PickupStore(string name) : IStore
    {
        public void OrderItem(string buyerName, string itemName)
        {
            Console.WriteLine($"'{name}' received an order!");
            Console.WriteLine("Verifying order.");
            Thread.Sleep(500);
            Console.WriteLine("Packaging product....");
            Console.WriteLine("Order available for pickup.");
        }
    }

    public interface IShippingStoreFactory
    {
        IStore CreateShippingStore(string name, ShippingServiceType shippingServiceType);
    }

    public class ShippingStoreFactory(Func<string, ShippingServiceType, IStore> build) : IShippingStoreFactory
    {
        public IStore CreateShippingStore(string name, ShippingServiceType shippingServiceType)
        {
            return build(name, shippingServiceType);
        }
    }

    public interface IPickupStoreFactory
    {
        IStore CreatePickupStore(string name);
    }

    public class PickupStoreFactory(Func<string, IStore> build) : IPickupStoreFactory
    {
        public IStore CreatePickupStore(string name)
        {
            return build(name);
        }
    }
}
```