```csharp
using Microsoft.Extensions.DependencyInjection;
using Sandbox;

var services = ConfigureServices();
var serviceProvider = services.BuildServiceProvider();
serviceProvider.GetService<App>()?.Run();

static IServiceCollection ConfigureServices()
{
    IServiceCollection services = new ServiceCollection();
    services.AddCreditCardFactory();
    services.AddTransient<App>();

    return services;
}

namespace Sandbox
{
    public class App
    {
        private readonly ICreditCardFactory _creditCardFactory;
        public App(ICreditCardFactory creditCardFactory)
        {
            _creditCardFactory = creditCardFactory;
        }
        public void Run()
        {
            var goldCreditCard = _creditCardFactory.Build(CreditCardType.Gold);
            goldCreditCard.Pay(100);
            Console.ReadKey();
        }
    }

    public enum CreditCardType
    {
        Gold,
        Platinum,
        Titanium
    }

    public interface ICreditCard
    {
        int Limit { get; set; }
        void Pay(double amount);
    }

    public class GoldCreditCard : ICreditCard
    {
        public int Limit { get; set; } = 200;
        public void Pay(double amount)
        {
            Console.WriteLine($"Processing payment for {amount} on your Gold Credit Card...");
            Thread.Sleep(3000);
            Console.WriteLine("Payment processed succesfully");
        }
    }

    public class PlatinumCreditCard : ICreditCard
    {
        public int Limit { get; set; } = 400;
        public void Pay(double amount)
        {
            Console.WriteLine($"Processing payment for {amount} on your Platinum Credit Card...");
            Thread.Sleep(3000);
            Console.WriteLine("Payment processed succesfully");
        }
    }

    public class TitaniumCreditCard : ICreditCard
    {
        public int Limit { get; set; } = 600;
        public void Pay(double amount)
        {
            Console.WriteLine($"Processing payment for {amount} on your Titanium Credit Card...");
            Thread.Sleep(3000);
            Console.WriteLine("Payment processed succesfully");
        }
    }

    public interface ICreditCardFactory
    {
        ICreditCard Build(CreditCardType creditCardType);
    }

    public class CreditCardFactory : ICreditCardFactory
    {
        private readonly Func<CreditCardType, ICreditCard> _factory;
        public CreditCardFactory(Func<CreditCardType, ICreditCard> factory)
        {
            _factory = factory;
        }
        public ICreditCard Build(CreditCardType creditCardType)
        {
            return _factory(creditCardType);
        }
    }

    public static class Extensions
    {
        public static void AddCreditCardFactory(this IServiceCollection services)
        {
            services.AddSingleton<GoldCreditCard>();
            services.AddSingleton<PlatinumCreditCard>();
            services.AddSingleton<TitaniumCreditCard>();

            services.AddSingleton<Func<CreditCardType, ICreditCard>>(sp => (CreditCardType creditCardType) =>
            creditCardType switch
                {
                    CreditCardType.Gold => sp.GetRequiredService<GoldCreditCard>(),
                    CreditCardType.Platinum => sp.GetRequiredService<PlatinumCreditCard>(),
                    CreditCardType.Titanium => sp.GetRequiredService<TitaniumCreditCard>(),
                    _ => throw new ArgumentOutOfRangeException(nameof(creditCardType), $"type '{creditCardType}' is not registered")
                });

            services.AddSingleton<ICreditCardFactory, CreditCardFactory>();
        }
    }

}
```