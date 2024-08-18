```csharp
namespace Sandbox
{
    public class App
    {
        public void Run(string[] args)
        {
            var margheritaFactory = new MargheritaPizzaFactory();
            var pepperoniFactory = new PepperoniPizzaFactory();
            var veggieFactory = new VeggiePizzaFactory();

            var margheritaToppings = new List<string> { "Extra Cheese", "Basil" };
            var pepperoniToppings = new List<string> { "Extra Pepperoni", "Olives" };
            var veggieToppings = new List<string> { "Mushrooms", "Bell Peppers", "Onions" };

            margheritaFactory.OrderPizza(Size.Large, Crust.Thin, margheritaToppings);
            pepperoniFactory.OrderPizza(Size.Medium, Crust.Regular, pepperoniToppings);
            veggieFactory.OrderPizza(Size.ExtraLarge, Crust.Pan, veggieToppings);
        }
    }

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

    public interface IPizzaPreparation
    {
        void Prepare();
        void Bake();
        void Cut();
        void Box();
    }

    public abstract class Pizza : IPizzaPreparation
    {
        public Size Size { get; set; }
        public Crust Crust { get; set; }
        public List<string> Toppings { get; set; } = new();
        public List<string> BaseIngredients { get; set; } = new();

        public abstract string GetDescription();
        public abstract double GetCost();

        public abstract void Prepare();
        public abstract void Bake();
        public abstract void Cut();
        public abstract void Box();
    }

    public class MargheritaPizza : Pizza
    {
        public MargheritaPizza()
        {
            BaseIngredients.AddRange(new[] { "Dough", "Sauce", "Cheese", "Tomato", "Oregano" });
        }

        public override string GetDescription()
        {
            return "Margherita Pizza";
        }

        public override double GetCost()
        {
            return 8.50 + Toppings.Count * 1.00; // Base price + extra toppings
        }

        public override void Prepare()
        {
            Console.WriteLine("Preparing Margherita Pizza:");
            Console.WriteLine("- Rolling out thin, crispy dough");
            Console.WriteLine("- Spreading a layer of San Marzano tomato sauce");
            Console.WriteLine("- Adding fresh mozzarella cheese");
            Console.WriteLine("- Placing fresh basil leaves");
            foreach (var topping in Toppings)
            {
                Console.WriteLine($"- Adding extra topping: {topping}");
            }
        }

        public override void Bake()
        {
            Console.WriteLine("Baking Margherita Pizza:");
            Console.WriteLine("- Preheating wood-fired oven to 800┬░F (427┬░C)");
            Console.WriteLine("- Baking for 90 seconds, rotating halfway through");
        }

        public override void Cut()
        {
            Console.WriteLine("Cutting Margherita Pizza:");
            Console.WriteLine("- Slicing into 6 traditional triangular pieces");
        }

        public override void Box()
        {
            Console.WriteLine("Boxing Margherita Pizza:");
            Console.WriteLine("- Placing in a eco-friendly cardboard box");
            Console.WriteLine("- Adding a packet of red pepper flakes and oregano");
        }
    }

    public class PepperoniPizza : Pizza
    {
        public PepperoniPizza()
        {
            BaseIngredients.AddRange(new[] { "Dough", "Sauce", "Cheese", "Pepperoni" });
        }

        public override string GetDescription()
        {
            return "Pepperoni Pizza";
        }

        public override double GetCost()
        {
            return 10.00 + Toppings.Count * 1.00; // Base price + extra toppings
        }

        public override void Prepare()
        {
            Console.WriteLine("Preparing Pepperoni Pizza:");
            Console.WriteLine("- Stretching dough to desired thickness");
            Console.WriteLine("- Spreading zesty tomato sauce");
            Console.WriteLine("- Sprinkling a generous layer of mozzarella cheese");
            Console.WriteLine("- Arranging pepperoni slices in a circular pattern");
            foreach (var topping in Toppings)
            {
                Console.WriteLine($"- Adding extra topping: {topping}");
            }
        }

        public override void Bake()
        {
            Console.WriteLine("Baking Pepperoni Pizza:");
            Console.WriteLine("- Preheating oven to 450┬░F (232┬░C)");
            Console.WriteLine(
                "- Baking for 12-15 minutes until cheese is bubbly and crust is golden brown"
            );
        }

        public override void Cut()
        {
            Console.WriteLine("Cutting Pepperoni Pizza:");
            Console.WriteLine("- Slicing into 8 equal triangular pieces");
        }

        public override void Box()
        {
            Console.WriteLine("Boxing Pepperoni Pizza:");
            Console.WriteLine("- Placing in a sturdy cardboard box with vents");
            Console.WriteLine("- Including a side of garlic dipping sauce");
        }
    }

    public class VeggiePizza : Pizza
    {
        public VeggiePizza()
        {
            BaseIngredients.AddRange(
                new[] { "Dough", "Sauce", "Mushrooms", "Bell Peppers", "Onions" }
            );
        }

        public override string GetDescription()
        {
            return "Veggie Pizza";
        }

        public override double GetCost()
        {
            return 9.00 + Toppings.Count * 1.00; // Base price + extra toppings
        }

        public override void Prepare()
        {
            Console.WriteLine("Preparing Veggie Pizza:");
            Console.WriteLine("- Rolling out whole wheat dough");
            Console.WriteLine("- Spreading a layer of garlic-herb tomato sauce");
            Console.WriteLine("- Adding a mix of mozzarella and feta cheese");
            Console.WriteLine("- Arranging sliced mushrooms, bell peppers, and red onions");
            foreach (var topping in Toppings)
            {
                Console.WriteLine($"- Adding extra topping: {topping}");
            }
            Console.WriteLine("- Drizzling with olive oil and sprinkling dried oregano");
        }

        public override void Bake()
        {
            Console.WriteLine("Baking Veggie Pizza:");
            Console.WriteLine("- Preheating oven to 425┬░F (218┬░C)");
            Console.WriteLine(
                "- Baking for 15-18 minutes, until vegetables are tender and crust is crispy"
            );
        }

        public override void Cut()
        {
            Console.WriteLine("Cutting Veggie Pizza:");
            Console.WriteLine("- Slicing into 10 square pieces for easy sharing");
        }

        public override void Box()
        {
            Console.WriteLine("Boxing Veggie Pizza:");
            Console.WriteLine("- Placing in a recycled paper box");
            Console.WriteLine("- Including a small container of balsamic glaze for drizzling");
        }
    }

    public abstract class BasePizzaFactory
    {
        protected abstract Pizza CreatePizza(Size size, Crust crust, List<string> toppings);

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
        protected override Pizza CreatePizza(Size size, Crust crust, List<string> toppings)
        {
            return new MargheritaPizza
            {
                Size = size,
                Crust = crust,
                Toppings = toppings,
            };
        }
    }

    public class PepperoniPizzaFactory : BasePizzaFactory
    {
        protected override Pizza CreatePizza(Size size, Crust crust, List<string> toppings)
        {
            return new PepperoniPizza
            {
                Size = size,
                Crust = crust,
                Toppings = toppings,
            };
        }
    }

    public class VeggiePizzaFactory : BasePizzaFactory
    {
        protected override Pizza CreatePizza(Size size, Crust crust, List<string> toppings)
        {
            return new VeggiePizza
            {
                Size = size,
                Crust = crust,
                Toppings = toppings,
            };
        }
    }
}

```