```csharp
using Sandbox;

var margheritaFactory = new MargheritaPizzaFactory();
var pepperoniFactory = new PepperoniPizzaFactory();
var veggieFactory = new VeggiePizzaFactory();

var margheritaToppings = new List<string> { "Extra Cheese", "Basil" };
var pepperoniToppings = new List<string> { "Extra Pepperoni", "Olives" };
var veggieToppings = new List<string> { "Mushrooms", "Bell Peppers", "Onions" };

margheritaFactory.OrderPizza(Size.Large, Crust.Thin, margheritaToppings);
pepperoniFactory.OrderPizza(Size.Medium, Crust.Regular, pepperoniToppings);
veggieFactory.OrderPizza(Size.ExtraLarge, Crust.Pan, veggieToppings);

namespace Sandbox
{
    public enum Size
    {
        None,
        Medium,
        Large,
        ExtraLarge,
    }

    public enum Crust
    {
        None,
        Thin,
        Regular,
        Pan,
    }

    public abstract class Pizza
    {
        public Size Size { get; set; }
        public Crust Crust { get; set; }
        public List<string> Toppings { get; set; } = new List<string>();

        public abstract string GetDescription();
        public abstract double GetCost();

        public void Prepare()
        {
            Console.WriteLine($"Preparing {Size} {GetDescription()} with {Crust} crust.");
            Console.WriteLine($"Toppings: {string.Join(", ", Toppings)}");
        }

        public void Bake()
        {
            Console.WriteLine("Baking pizza...");
        }

        public void Cut()
        {
            Console.WriteLine("Cutting pizza...");
        }

        public void Box()
        {
            Console.WriteLine("Boxing pizza...");
        }
    }

    public class MargheritaPizza : Pizza
    {
        public override string GetDescription()
        {
            return "Margherita Pizza";
        }

        public override double GetCost()
        {
            return 8.50 + Toppings.Count * 1.00; // Base price + extra toppings
        }
    }

    public class PepperoniPizza : Pizza
    {
        public override string GetDescription()
        {
            return "Pepperoni Pizza";
        }

        public override double GetCost()
        {
            return 10.00 + Toppings.Count * 1.00; // Base price + extra toppings
        }
    }

    public class VeggiePizza : Pizza
    {
        public override string GetDescription()
        {
            return "Veggie Pizza";
        }

        public override double GetCost()
        {
            return 9.00 + Toppings.Count * 1.00; // Base price + extra toppings
        }
    }

    public abstract class BasePizzaFactory
    {
        public abstract Pizza CreatePizza(Size size, Crust crust, List<string> toppings);

        public Pizza OrderPizza(Size size, Crust crust, List<string> toppings)
        {
            Pizza pizza = CreatePizza(size, crust, toppings);
            pizza.Prepare();
            pizza.Bake();
            pizza.Cut();
            pizza.Box();
            Console.WriteLine($"Cost: ${pizza.GetCost()}\n");
            return pizza;
        }
    }

    public class MargheritaPizzaFactory : BasePizzaFactory
    {
        public override Pizza CreatePizza(Size size, Crust crust, List<string> toppings)
        {
            var pizza = new MargheritaPizza
            {
                Size = size,
                Crust = crust,
                Toppings = toppings,
            };
            return pizza;
        }
    }

    public class PepperoniPizzaFactory : BasePizzaFactory
    {
        public override Pizza CreatePizza(Size size, Crust crust, List<string> toppings)
        {
            var pizza = new PepperoniPizza
            {
                Size = size,
                Crust = crust,
                Toppings = toppings,
            };
            return pizza;
        }
    }

    public class VeggiePizzaFactory : BasePizzaFactory
    {
        public override Pizza CreatePizza(Size size, Crust crust, List<string> toppings)
        {
            var pizza = new VeggiePizza
            {
                Size = size,
                Crust = crust,
                Toppings = toppings,
            };
            return pizza;
        }
    }
}

```