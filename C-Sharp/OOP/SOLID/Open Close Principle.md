```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;

namespace SandboxConsole
{
    public partial class Program
    {
        static void Main(string[] args)
        {
            var apple = new Product("apple", Color.Green, Size.Small);

            var tree = new Product("tree", Color.Green, Size.Small);

            var house = new Product("house", Color.Red, Size.Large);

            var products = new Product[] { apple, tree, house };

            var productFilter = new ProductFilter();

            Console.WriteLine("Green Products: ");

            foreach (var p in productFilter.Filter(products, new ProductColorSpecification(Color.Green)))
            {
                Console.WriteLine($" - {p.Name} is green");
            }

            Console.WriteLine("Green Small Items: ");

            foreach (var p in productFilter.Filter(products,
                new AndSpecification<Product>(
                    new List<ISpecification<Product>>()
                    {
                        new ProductColorSpecification(Color.Green),
                        new ProductSizeSpecification(Size.Small)
                    }
                    )))
            {
                Console.WriteLine($" - {p.Name} is green and small");
            }
            Console.ReadKey();
        }
    }

    public enum Color
    {
        Red,
        Green,
        Blue
    }

    public enum Size
    {
        Small,
        Medium,
        Large
    }

    public class Product
    {
        public string Name { get; set; }
        public Color Color { get; set; }
        public Size Size { get; set; }

        public Product(string name, Color color, Size size)
        {
            Name = name;
            Color = color;
            Size = size;
        }

    }

    public interface ISpecification<T>
    {
        bool IsSatisfied(T t);
    }

    public interface IFilter<T>
    {
        IEnumerable<T> Filter(IEnumerable<T> items, ISpecification<T> specification);
    }

    public class AndSpecification<T> : ISpecification<T>
    {
        private readonly List<ISpecification<T>> _specifications;

        public AndSpecification(List<ISpecification<T>> specifications)
        {
            _specifications = specifications;
        }
        public bool IsSatisfied(T t) => _specifications.All(spec => spec.IsSatisfied(t));
    }

    public class ProductColorSpecification : ISpecification<Product>
    {
        private readonly Color _color;

        public ProductColorSpecification(Color color)
        {
            _color = color;
        }
        public bool IsSatisfied(Product t) => t.Color == _color;
    }

    public class ProductSizeSpecification : ISpecification<Product>
    {
        private readonly Size _size;

        public ProductSizeSpecification(Size size)
        {
            _size = size;
        }
        public bool IsSatisfied(Product t) => t.Size == _size;
    }

    public class ProductFilter : IFilter<Product>
    {
        public IEnumerable<Product> Filter(IEnumerable<Product> items, ISpecification<Product> specification)
        {
            foreach (var i in items)
                if (specification.IsSatisfied(i))
                    yield return i;
        }
    }
}
```