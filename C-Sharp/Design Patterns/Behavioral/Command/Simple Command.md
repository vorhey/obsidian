```csharp
using Sandbox;

var account = new BankAccount();

var commands = new List<BankAccountCommand>()
{
    new(account, BankOperation.Deposit, 100),
    new(account, BankOperation.Deposit, 100),
    new(account, BankOperation.Withdraw, 50)
};

foreach (var command in commands)
{
    command.Execute();
}

var reversed = Enumerable.Reverse(commands);

Console.WriteLine($"Account Balance: {account.Balance}");

foreach (var command in reversed)
{
    command.Undo();
}

Console.WriteLine($"Account Balance, After Undo: {account.Balance}");

namespace Sandbox
{
    public interface ICommand
    {
        void Execute();
        void Undo();
        bool Succeeded { get; }
    }

    public class BankAccount
    {
        public double Balance { get; private set; }

        public bool Deposit(double amount)
        {
            if (amount < 0)
            {
                return false;
            }

            Balance += amount;
            Console.WriteLine($"Deposited: {amount}");
            return true;
        }

        public bool Withdraw(double amount)
        {
            if (amount < 0 || Balance < amount)
            {
                return false;
            }

            Balance -= amount;
            Console.WriteLine($"Withdrew: {amount}");
            return true;
        }
    }

    public enum BankOperation
    {
        Deposit,
        Withdraw
    }

    public class BankAccountCommand(
        BankAccount bankAccount,
        BankOperation bankOperation,
        double amount
    ) : ICommand
    {
        public bool Succeeded { get; private set; }

        public void Execute()
        {
            Succeeded = bankOperation switch
            {
                BankOperation.Withdraw => bankAccount.Withdraw(amount),
                BankOperation.Deposit => bankAccount.Deposit(amount),
                _ => throw new ArgumentOutOfRangeException($"Invalid argument {bankOperation}"),
            };
        }

        public void Undo()
        {
            if (!Succeeded)
            {
                return;
            }

            _ = bankOperation switch
            {
                BankOperation.Withdraw => bankAccount.Deposit(amount),
                BankOperation.Deposit => bankAccount.Withdraw(amount),
                _ => throw new ArgumentOutOfRangeException($"Invalid argument {bankOperation}")
            };
        }
    }
}

```