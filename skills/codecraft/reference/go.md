# codecraft examples: Go

Apply Go's own idioms: return errors, do not panic for expected failure; wrap
errors with context; accept interfaces, return concrete types; keep zero values
useful. Examples back the principles in `SKILL.md`.

## Linear flow with guard clauses (principle 4)

```go
// before: the happy path is buried under nested conditions
func Withdraw(a *Account, amount int) bool {
    if a != nil {
        if a.Active {
            if amount <= a.Balance {
                a.Balance -= amount
                return true
            }
        }
    }
    return false
}

// after: reject the edge cases first, then the happy path reads straight
func Withdraw(a *Account, amount int) bool {
    if a == nil || !a.Active {
        return false
    }
    if amount > a.Balance {
        return false
    }
    a.Balance -= amount
    return true
}
```

## Handle errors honestly: wrap with context (principle 9)

```go
// before: the error is returned bare, the caller cannot tell what failed
func loadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, err
    }
    return parse(data)
}

// after: wrap with context so the cause is actionable up the stack
func loadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("read config %q: %w", path, err)
    }
    return parse(data)
}
```

## Make the contract visible, document it (principle 8)

```go
// before: opaque parameters and return; the caller must read the body
func Handle(req interface{}) interface{} { /* ... */ }

// after: typed signature plus a doc comment in Go's convention
// RecentOrders returns a user's most recent orders, newest first.
// It returns an empty slice if the user has none.
func RecentOrders(userID string, limit int) []Order { /* ... */ }
```

## Dependency Inversion: accept an interface (SOLID, apply with judgement)

```go
// before: OrderService is welded to one concrete notifier
type OrderService struct {
    mailer *SendGridClient
}

// after: depend on a small interface; any implementation works
type Notifier interface {
    Notify(recipient, message string) error
}

type OrderService struct {
    notifier Notifier // injected; tests pass a fake
}

func (s *OrderService) Confirm(o Order) error {
    return s.notifier.Notify(o.Email, "Confirmed")
}
```

## Keep side effects at the edges (principle 10)

```go
// before: the decision and the IO are tangled
func ApplyDiscount(db *DB, id string) error {
    order, err := db.Load(id)
    if err != nil {
        return err
    }
    if order.Total > 100 {
        order.Total = order.Total * 9 / 10
    }
    return db.Save(order)
}

// after: a pure function decides, the shell does the IO
// discountedTotal is pure, so it is trivial to test.
func discountedTotal(total int) int {
    if total > 100 {
        return total * 9 / 10
    }
    return total
}

func ApplyDiscount(db *DB, id string) error {
    order, err := db.Load(id)
    if err != nil {
        return err
    }
    order.Total = discountedTotal(order.Total)
    return db.Save(order)
}
```
