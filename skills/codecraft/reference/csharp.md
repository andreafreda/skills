# codecraft examples: C#

Apply C#'s own idioms: typed signatures, XML doc comments on the public surface,
specific exception types over catch-all, interfaces plus dependency injection
over new-ing concretes. Examples back the principles in `SKILL.md`.

## Handle errors honestly (principle 9)

```csharp
// before: a blanket catch hides every bug as "not found"
try { return Load(id); }
catch { return null; }

// after: catch the case you expect, let the rest surface
try { return Load(id); }
catch (FileNotFoundException) { return null; }
```

## Make the contract visible, document it (principle 8)

```csharp
// before: object in, object out; the caller learns nothing
public object Handle(object req) { /* ... */ }

// after: typed signature plus an XML doc comment of intent
/// <summary>A user's most recent orders, newest first. Empty if they have none.</summary>
public IReadOnlyList<Order> RecentOrders(string userId, int limit) { /* ... */ }
```

## Linear flow with guard clauses (principle 4)

```csharp
// before: the happy path is buried under nested conditions
public bool Withdraw(Account account, int amount) {
    if (account != null) {
        if (account.Active) {
            if (amount <= account.Balance) {
                account.Balance -= amount;
                return true;
            }
        }
    }
    return false;
}

// after: reject the edge cases first, then the happy path reads straight
public bool Withdraw(Account account, int amount) {
    if (account is null || !account.Active) return false;
    if (amount > account.Balance) return false;
    account.Balance -= amount;
    return true;
}
```

## Open/Closed with an interface (SOLID, apply with judgement)

```csharp
// before: every new shape edits the same switch
public double Area(Shape s) => s.Kind switch {
    "circle" => Math.PI * s.R * s.R,
    "square" => s.Side * s.Side,
    _ => throw new ArgumentException(nameof(s)),
};

// after: each shape implements IShape; new shapes add a file, no edit here
public interface IShape {
    double Area();
}

public class Circle : IShape {
    public double R { get; init; }
    public double Area() => Math.PI * R * R;
}

public class Square : IShape {
    public double Side { get; init; }
    public double Area() => Side * Side;
}
```

## Dependency Inversion: inject the abstraction (SOLID, apply with judgement)

```csharp
// before: OrderService is welded to one concrete vendor
public class OrderService {
    private readonly SendGridClient _mailer = new();
    public void Confirm(Order o) => _mailer.Send(o.Email, "Confirmed");
}

// after: depend on INotifier; the vendor is injected by the container
public interface INotifier {
    void Notify(string recipient, string message);
}

public class OrderService {
    private readonly INotifier _notifier;
    public OrderService(INotifier notifier) => _notifier = notifier;
    public void Confirm(Order o) => _notifier.Notify(o.Email, "Confirmed");
}
```
