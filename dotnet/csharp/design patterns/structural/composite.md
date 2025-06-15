```csharp
var computer = new Composite("Computer", 25);
var motherboard = new Composite("Motherboard", 10);
var monitor = new Composite("Monitor", 5);
var ram = new Leaf("Ram memory", 5);

computer.Add(motherboard);
computer.Add(monitor);
motherboard.Add(ram);

Console.WriteLine($"Total PC Price: {computer.GetPrice().ToString("C0")}");
computer.Display();

public interface IComputerComponent
{
    string Name { get; }
    int Price { get; }
    int GetPrice();
    void Display(int n = 0);
}

public class Composite : IComputerComponent
{
    private readonly List<IComputerComponent> _components = [];

    public string Name { get; }

    public int Price { get; }

    public Composite(string name, int price)
    {
        Name = name;
        Price = price;
    }

    public void Add(IComputerComponent component)
    {
        _components.Add(component);
    }

    public int GetPrice()
    {
        return Price + _components.Sum(c => c.GetPrice());
    }

    public void Display(int n = 0)
    {
        Console.WriteLine($"{new string(' ', n)} {Name} {Price.ToString("C0")}");
        foreach (var c in _components)
        {
            c.Display(n + 2);
        }
    }
}

public class Leaf : IComputerComponent
{
    public string Name { get; }

    public int Price { get; }

    public int GetPrice()
    {
        return Price;
    }

    public Leaf(string name, int price)
    {
        Name = name;
        Price = price;
    }

    public void Display(int n)
    {
        Console.WriteLine($"{new string(' ', n)} {Name} {Price.ToString("C0")}");
    }
}
```