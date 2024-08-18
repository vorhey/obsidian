```csharp
using Sandbox;

var scientists = new List<Person>
{
    new("Albert", "Einstein", new(1879, 3, 14)),
    new("Marie", "Curie", new(1867, 11, 7)),
    new("Charles", "Darwin", new(1809, 2, 12)),
    new("Stephen", "Hawking", new(1942, 1, 8)),
    new("Nikola", "Tesla", new(1856, 7, 10)),
    new("Galileo", "Galilei", new(1564, 2, 15)),
    new("Ada", "Lovelace", new(1815, 12, 10)),
    new("Thomas", "Edison", new(1847, 2, 11)),
    new("Rosalind", "Franklin", new(1920, 7, 25)),
    new("Dmitri", "Mendeleev", new(1834, 2, 8))
};

IComparer<Person> ageComparer = Comparer<Person>.Create((a, b) => a.AgeToday.CompareTo(b.AgeToday));
IComparer<Person> firstNameComparer = Comparer<Person>.Create((a, b) => StringComparer.CurrentCultureIgnoreCase.Compare(a.Firstname, b.Firstname));
IComparer<Person> lastNameComparer = Comparer<Person>.Create((a, b) => StringComparer.CurrentCultureIgnoreCase.Compare(a.Lastname, b.Lastname));
IComparer<Person> birthCenturyComparer = Comparer<Person>.Create((a, b) => ((a.BirthDate.Year - 1 / 100).CompareTo(b.BirthDate.Year - 1) / 100));

scientists.SortAndPrint(birthCenturyComparer.Then(firstNameComparer.Reverse().Then(lastNameComparer)));

namespace Sandbox
{
    public record Person(string Firstname, string Lastname, DateOnly BirthDate)
    {
        public int AgeToday => AgeOn(DateOnly.FromDateTime(DateTime.UtcNow.Date));

        public int AgeOn(DateOnly date) => date.Year - BirthDate.Year - (IsPastBirthdayOn(date) ? 0 : 1);

        public bool IsPastBirthdayOn(DateOnly date) =>
            BirthDate.Month < date.Month || BirthDate.Month == date.Month && BirthDate.Day < date.Day;
    }

    public static class ComparerExtensions
    {
        public static IComparer<T> Reverse<T>(this IComparer<T> comparer)
        {
            return Comparer<T>.Create((a, b) => comparer.Compare(b, a));
        }
    }

    // Chain of responsibility
    public class ComparerChainNode<T>(IComparer<T> first, IComparer<T> next) : IComparer<T>
    {
        private IComparer<T> First { get; } = first;
        private IComparer<T> Next { get; } = next;

        public int Compare(T? x, T? y)
        {
            return First.Compare(x, y) is int decision && decision != 0 ? decision : Next.Compare(x, y);
        }
    }

    public static class ComparerChainNode
    {
        public static IComparer<T> Then<T>(this IComparer<T> first, IComparer<T> next)
        {
            return new ComparerChainNode<T>(first, next);
        }
    }

    public static class PersonHelpers
    {
        public static void SortAndPrint(this List<Person> people, IComparer<Person> sortOrder)
        {
            int firstNameLength = people.Select(p => p.Firstname.Length).DefaultIfEmpty(0).Max();
            int lastNameLength = people.Select(p => p.Lastname.Length).DefaultIfEmpty(0).Max();

            people.Sort(sortOrder);

            foreach (var person in people)
            {
                var output = $"""
                    {new string('-', 40)}
                    {person.Firstname.PadRight(firstNameLength)} {person.Lastname.PadRight(lastNameLength)} - {person.BirthDate.Year.ToString().PadLeft(lastNameLength)}
                """;
                Console.WriteLine(output);
            }

            Console.WriteLine(new string('*', 60));
        }
    }
}
```