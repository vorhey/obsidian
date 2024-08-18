```csharp
using Sandbox;

var chain = new TierOneSupport();
var tierTwo = new TierTwoSupport();
var tierThree = new TierThreeSupport();

chain.SetNext(tierTwo);
tierTwo.SetNext(tierThree);

var simpleIssue = new CustomerIssue("Can't log into account", 2);
var moderateIssue = new CustomerIssue("Software crashes on startup", 5);
var complexIssue = new CustomerIssue("Data corruption in database", 9);

List<CustomerIssue> issues = [simpleIssue, moderateIssue, complexIssue];

foreach (var issue in issues)
{
    chain.Handle(issue);
}

namespace Sandbox
{
    public record CustomerIssue(string Description, int Severity);

    public abstract class SupportHandler
    {
        private SupportHandler? _next;

        public void SetNext(SupportHandler handler)
        {
            _next = handler;
        }

        public void Handle(CustomerIssue issue)
        {
            HandleRequest(issue);
            _next?.Handle(issue);
        }

        protected abstract void HandleRequest(CustomerIssue issue);
    }

    public class TierOneSupport : SupportHandler
    {
        protected override void HandleRequest(CustomerIssue issue)
        {
            if (issue.Severity <= 3)
            {
                Console.WriteLine($"Tier One Support handling issue: {issue.Description}");
            }
        }
    }

    public class TierTwoSupport : SupportHandler
    {
        protected override void HandleRequest(CustomerIssue issue)
        {
            if (issue.Severity >= 3 && issue.Severity <= 7)
            {
                Console.WriteLine($"Tier Two Support handling issue: {issue.Description}");
            }
        }
    }

    public class TierThreeSupport : SupportHandler
    {
        protected override void HandleRequest(CustomerIssue issue)
        {
            if (issue.Severity > 7)
            {
                Console.WriteLine($"Tier Three Support handling issue: {issue.Description}");
            }
        }
    }
}

```