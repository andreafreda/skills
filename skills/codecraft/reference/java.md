# codecraft examples: Java

Apply Java's own idioms: typed signatures, Javadoc on the public surface, specific
checked or unchecked exceptions over catch-all, constructor injection over
new-ing dependencies inside a class. Examples back the principles in `SKILL.md`.

## Make the contract visible, document it (principle 8)

```java
// before: Object in, Object out; the signature tells the caller nothing
public Object handle(Object req) { ... }

// after: the signature alone tells the caller how to use it
/** Validate and book a reservation; throws SlotTakenException if the slot is gone. */
public Booking book(ReservationRequest request) { ... }
```

## Name things fully, name the magic number (principle 2)

```java
// before: the name and the literal hide the intent
double calc(double n, double b) {
    return n + b * 0.22;
}

// after: the name states the job, the constant names the value
static final double TAX_RATE = 0.22;

double grossWithTax(double net, double taxableBase) {
    return net + taxableBase * TAX_RATE;
}
```

## Handle errors honestly (principle 9)

```java
// before: catching Exception buries the real cause
try {
    return load(id);
} catch (Exception e) {
    return null;
}

// after: catch the case you expect, let the rest surface
try {
    return load(id);
} catch (FileNotFoundException e) {
    return null;
}
```

## Single Responsibility: split the class (SOLID, apply with judgement)

```java
// before: one class computes a report AND emails it; two reasons to change
class ReportService {
    String build(Data d) { /* report maths */ }
    void email(String report, String to) { /* SMTP */ }
}

// after: each class has one reason to change
class ReportBuilder {
    String build(Data d) { /* report maths */ }
}

class ReportMailer {
    void send(String report, String to) { /* SMTP */ }
}
```

## Dependency Inversion: constructor injection (SOLID, apply with judgement)

```java
// before: OrderService is welded to one concrete vendor
class OrderService {
    private final SendGridClient mailer = new SendGridClient();
    void confirm(Order o) { mailer.send(o.email(), "Confirmed"); }
}

// after: depend on a Notifier abstraction; the vendor is injected
interface Notifier {
    void notify(String recipient, String message);
}

class OrderService {
    private final Notifier notifier;
    OrderService(Notifier notifier) { this.notifier = notifier; }
    void confirm(Order o) { notifier.notify(o.email(), "Confirmed"); }
}
```
