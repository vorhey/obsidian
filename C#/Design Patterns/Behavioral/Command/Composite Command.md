```csharp
using Sandbox;

// Initialize bank accounts with insufficient funds for the transfer amount
var accountFrom = new BankAccount { Balance = 100 };
var accountTo = new BankAccount { Balance = 50 };

// Set up a transfer that is bound to fail due to insufficient funds
var amountToTransfer = 900;  // More than what's in accountFrom
var transferCommand = new MoneyTransferCommand(accountFrom, accountTo, amountToTransfer);

Console.WriteLine($"Before transfer: From Account Balance = {accountFrom.Balance}, To Account Balance = {accountTo.Balance}");

try
{
    transferCommand.Execute();
    Console.WriteLine("Transfer succeeded.");
}
catch (Exception ex)
{
    Console.WriteLine($"Transfer failed: {ex.Message}");
}

Console.WriteLine($"After transfer: From Account Balance = {accountFrom.Balance}, To Account Balance = {accountTo.Balance}");

namespace Sandbox
{
    public record BankAccount
    {
        private const int _overdraftLimit = -500;

        public int Balance { get; set; }

        public bool Deposit(int amount)
        {
            if (amount <= 0)
            {
                return false;
            }

            Balance += amount;
            Console.WriteLine($"Deposited : ${amount}, balance is now: {Balance}");
            return true;
        }

        public bool WithDraw(int amount)
        {
            if (amount <= 0 || Balance - amount < _overdraftLimit)
            {
                return false;
            }

            Balance -= amount;
            Console.WriteLine($"Withdrew: ${amount}");
            return true;
        }
    }

    public interface ICommand
    {
        void Execute();
        void Undo();
        bool Success { get; }
    }

    public enum CommandAction
    {
        Deposit,
        WithDraw
    }

    public class BankAccountCommand(BankAccount bankAccount, CommandAction action, int amount)
        : ICommand
    {
        public bool Success { get; set; }

        public void Execute()
        {
            Success = action switch
            {
                CommandAction.Deposit => bankAccount.Deposit(amount),
                CommandAction.WithDraw => bankAccount.WithDraw(amount),
                _ => throw new ArgumentOutOfRangeException($"Invalid action provided: {action}"),
            };
        }

        public void Undo()
        {
            if (!Success)
            {
                return;
            }
            switch (action)
            {
                case CommandAction.Deposit:
                    bankAccount.WithDraw(amount);
                    break;
                case CommandAction.WithDraw:
                    bankAccount.Deposit(amount);
                    break;
                default:
                    throw new ArgumentOutOfRangeException($"Invalid action provided: {amount}");
            }
        }
    }

    public class CompositeBankAccountCommand(IEnumerable<BankAccountCommand> collection) : ICommand
    {
        protected readonly List<BankAccountCommand> Commands = new(collection);

        public bool Success
        {
            get { return Commands.TrueForAll(c => c.Success); }
            set
            {
                if (Success != value)
                {
                    foreach (var command in Commands)
                    {
                        command.Success = value;
                    }
                }
            }
        }

        public virtual void Execute()
        {
            foreach (var command in Commands)
            {
                command.Execute();
            }
        }

        public void Undo()
        {
            var reversed = Enumerable.Reverse(Commands);

            foreach (var command in reversed)
            {
                if (command.Success)
                {
                    command.Undo();
                }
            }
        }
    }



    public class MoneyTransferCommand(BankAccount from, BankAccount to, int amount)
        : CompositeBankAccountCommand(
            new List<BankAccountCommand>()
            {
                    new(from, CommandAction.WithDraw, amount),
                    new(to, CommandAction.Deposit, amount)
            }
        )
    {
        public override void Execute()
        {
            var executedCommands = new List<ICommand>();

            try
            {
                foreach (var command in Commands)
                {
                    command.Execute();
                    if (!command.Success)
                    {
                        throw new InvalidOperationException("Command execution failed.");
                    }
                    executedCommands.Add(command);
                }
            }
            catch (Exception)
            {
                foreach (var executedCommand in Enumerable.Reverse(executedCommands))
                {
                    executedCommand.Undo();
                }
                throw;
            }
        }
    }
}

```