# codecraft examples: language-agnostic

Use this file when you work in a language you do not know well, or one without a
dedicated example file here. The shape of the advice is the same everywhere; only
the syntax changes. Translate the pseudocode into the target language's idioms.

These examples back the principles in `SKILL.md`. The principles, tie-breaks,
Beck's four rules and the smell list all live there and still apply in full,
whatever the language; this file only adds worked examples. Read the principle in
`SKILL.md` first, then the example here.

## Name things fully (principle 2)

```
# before: the name and the literal hide the intent
function calc(n, b):
    return n + b * 0.22

# after: the name states the job, the constant names the value
TAX_RATE = 0.22
function gross_with_tax(net, taxable_base):
    return net + taxable_base * TAX_RATE
```

## Linear flow with guard clauses (principle 4)

```
# before: the happy path is buried under nested conditions
function withdraw(account, amount):
    if account exists:
        if account is active:
            if amount <= account.balance:
                account.balance -= amount
                return true
    return false

# after: reject the edge cases first, then the happy path reads straight
function withdraw(account, amount):
    if account is null or not account.active:
        return false
    if amount > account.balance:
        return false
    account.balance -= amount
    return true
```

## Handle errors honestly (principle 9)

```
# before: a blanket catch hides every bug as "not found"
try:
    return load(id)
catch anything:
    return null

# after: catch the case you expect, let the rest surface
try:
    return load(id)
catch FileNotFound:
    return null
# any other failure propagates with its real cause
```

## Keep side effects at the edges (principle 10)

```
# before: pure decision and IO are tangled, so neither is easy to test
function apply_discount(order_id):
    order = db.load(order_id)          # IO
    if order.total > 100:              # decision
        order.total = order.total * 0.9
    db.save(order)                     # IO

# after: a pure core decides, a thin shell does the IO
function discounted_total(total):      # pure, trivial to test
    if total > 100:
        return total * 0.9
    return total

function apply_discount(order_id):     # thin IO shell
    order = db.load(order_id)
    order.total = discounted_total(order.total)
    db.save(order)
```

## SOLID, illustrated without a specific language

Readability comes first, but these are the shapes the SOLID notes in `SKILL.md`
point at. Reach for them when a real second case or seam exists, never for a
single concrete case (YAGNI).

**Single Responsibility:** one reason to change. Split a class that both computes
a report and emails it into a report builder and a sender; a change to the email
transport must not risk the report maths.

**Open/Closed:** extend by adding, not by editing stable code.

```
# before: every new shape edits the same function
function area(shape):
    if shape.kind == "circle": return 3.14159 * shape.r * shape.r
    if shape.kind == "square": return shape.side * shape.side
    # adding a triangle means editing this function again

# after: each shape implements an interface; new shapes add a file
interface Shape:
    function area()

class Circle implements Shape:
    function area(): return 3.14159 * self.r * self.r

class Square implements Shape:
    function area(): return self.side * self.side
```

**Dependency Inversion:** depend on an abstraction, not a concrete vendor at the
call site.

```
# before: the order service is welded to one email vendor
class OrderService:
    function __init__():
        self.mailer = SendGridClient()      # concrete vendor
    function confirm(order):
        self.mailer.send(order.email, "Confirmed")

# after: it depends on a Notifier abstraction; the vendor is injected
interface Notifier:
    function notify(recipient, message)

class OrderService:
    function __init__(notifier: Notifier):  # any Notifier works
        self.notifier = notifier
    function confirm(order):
        self.notifier.notify(order.email, "Confirmed")
```
