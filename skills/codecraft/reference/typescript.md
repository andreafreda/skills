# codecraft examples: TypeScript

Apply TypeScript's own idioms: typed signatures over `any`, JSDoc on the public
surface, discriminated unions over boolean flags, narrow early. Examples back the
principles in `SKILL.md`.

## Make the contract visible, document it (principle 8)

```ts
// before: any in, any out; the caller learns nothing from the signature
function find(u: any, l: any) { /* ... */ }

// after: types state the contract, the comment states the intent
/** A user's most recent orders, newest first. Returns [] if they have none. */
function recentOrders(userId: string, limit: number): Order[] { /* ... */ }
```

## Name things fully, name the magic number (principle 2)

```ts
// before: the name and the literal hide the intent
function calc(n: number, b: number): number {
  return n + b * 0.22;
}

// after: the name states the job, the constant names the value
const TAX_RATE = 0.22;

function grossWithTax(net: number, taxableBase: number): number {
  return net + taxableBase * TAX_RATE;
}
```

## Linear flow with guard clauses (principle 4)

```ts
// before: the happy path is buried under nested conditions
function withdraw(account: Account | null, amount: number): boolean {
  if (account) {
    if (account.active) {
      if (amount <= account.balance) {
        account.balance -= amount;
        return true;
      }
    }
  }
  return false;
}

// after: reject the edge cases first, then the happy path reads straight
function withdraw(account: Account | null, amount: number): boolean {
  if (!account || !account.active) return false;
  if (amount > account.balance) return false;
  account.balance -= amount;
  return true;
}
```

## Handle errors honestly (principle 9)

```ts
// before: a blanket catch swallows every error as null
try {
  return load(id);
} catch {
  return null;
}

// after: narrow to the case you expect, rethrow the rest
try {
  return load(id);
} catch (err) {
  if (err instanceof NotFoundError) return null;
  throw err;
}
```

## Boolean trap: model the concept instead of flags (readability smell)

```ts
// before: createUser(true, false) is unreadable at the call site
function createUser(isAdmin: boolean, sendEmail: boolean): User { /* ... */ }
createUser(true, false);

// after: a typed options object says what each value means
type CreateUserOptions = { role: "admin" | "member"; welcomeEmail: boolean };
function createUser(opts: CreateUserOptions): User { /* ... */ }
createUser({ role: "admin", welcomeEmail: false });
```

## Open/Closed with an interface (SOLID, apply with judgement)

```ts
// before: every new payment method edits the same switch
function charge(method: string, amount: number): void {
  if (method === "card") { /* ... */ }
  else if (method === "paypal") { /* ... */ }
  // a new method means editing this function again
}

// after: each method implements PaymentMethod; new methods add a file
interface PaymentMethod {
  charge(amount: number): void;
}

class Card implements PaymentMethod {
  charge(amount: number): void { /* ... */ }
}

class PayPal implements PaymentMethod {
  charge(amount: number): void { /* ... */ }
}
```
