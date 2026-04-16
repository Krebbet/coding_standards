# Object Oriented Design

Object-oriented design helps us build software that is easier to change, test, and extend as requirements evolve. By modeling behavior around cohesive “things” (objects) with clear responsibilities, we reduce coupling between parts of the system and create seams for refactoring. Good OOD also makes architecture decisions explicit (boundaries, contracts, substitution rules), which improves team velocity and lowers defect rates—especially as the codebase grows and more engineers contribute.

## Core OOD principles we design for

### Encapsulation (hide volatility)

Keep state private and expose behavior through stable methods. Encapsulation prevents “action at a distance” where many callers depend on internal representation, and it’s the fastest way to contain change.

**Heuristics**

- Prefer “tell, don’t ask”: call behavior rather than pulling data and deciding outside.
- Limit public surface area; make invariants impossible to violate.

### Abstraction (design to intent)

Expose what clients need to do, not how it’s done. 

Abstraction in OOP is the practice of modeling *essential* behavior in a simplified contract (e.g., an interface or abstract class) while hiding implementation details, so callers depend on *what an object can do* rather than *how it does it*. It improves clarity, enables polymorphism, and makes systems easier to change by letting you swap implementations behind a stable API.

**Examples**:

- **`Shape` abstraction:** a `Shape` contract exposes `area()`; `Circle` and `Rectangle` implement it differently, while code that calculates total area depends only on `Shape`.
- **`Vehicle` abstraction:** an abstract `Vehicle` defines `start()` / `stop()`; `Car` and `Motorcycle` provide concrete implementations.
- **Payments abstraction:** `PaymentProcessor` exposes `charge(amount)`; implementations wrap Stripe, Adyen, or a mock processor for tests.
- **Data access abstraction (Repository):** `CustomerRepository` exposes `findById()`; implementations use SQL, NoSQL, or an in-memory store.

**Heuristics**

- Name abstractions after responsibilities (“PaymentMethod”, “Notifier”), not data structures (“PaymentMethodImpl”).
- Keep contracts small; large abstractions become “god interfaces.”

### Polymorphism (replace conditionals with dispatch)

Polymorphism lets multiple implementations share the same contract. It’s a clean alternative to long `if/else` chains based on type or mode.

Polymorphism is a concept where the same operation (e.g., calling `draw()` or `pay()`) can produce different behavior depending on the object’s actual type. It enables you to program to an interface/base type while letting each concrete class supply its own implementation (run-time polymorphism via overriding). Many languages also support compile-time polymorphism such as method overloading (same method name, different parameter lists). The practical payoff is extensibility: you can add new types with minimal changes to calling code.
****

**Heuristics**

- If adding a new variant requires editing many conditionals, consider a polymorphic design.
- If there are only 1–2 stable variants, keep it simple.

## Abstract Base Classes (ABCs): contracts with shared behavior

ABCs define a required interface (what implementers must provide) and can supply reusable default behavior (what many implementers share). They’re useful when you need both: a strong contract plus a single place for shared algorithm scaffolding or invariants.

**When ABCs are a good fit**

- You need to enforce a common lifecycle (“initialize → validate → execute → cleanup”).
- You have meaningful shared behavior (not just method signatures).
- You want to protect invariants (base class can guard/normalize inputs).

**Watch-outs**

- ABCs can create tight coupling and fragile hierarchies if overused.
- If shared behavior is minimal, prefer an interface/protocol + composition.

## Inheritance: use sparingly, only for true “is-a”

Inheritance is powerful for reuse and polymorphism, but it’s also the fastest path to rigid, fragile designs. The key is ensuring substitutability: any subclass must work anywhere the base type is expected, without surprising behavior.

**Use inheritance when**

- The relationship is truly “is-a” and stable over time.
- Subclasses differ mainly by extending behavior, not rewriting it.
- You can state and uphold clear base-class invariants.

**Avoid inheritance when**

- You’re inheriting just to reuse code (that’s often a sign composition is better).
- Subclasses need to override many methods or violate base expectations.
- The base class keeps accumulating special cases for “just one more subclass.”

## Composition: prefer assembling behavior over inheriting it

Composition means building objects out of smaller components (delegation). This tends to produce more flexible designs because you can swap parts without restructuring a type hierarchy.

**Benefits**

- Better testability (mock/replace components).
- More flexibility (mix-and-match behaviors at runtime).
- Less coupling (components can evolve independently).

**Typical signals to choose composition**

- You want “has-a” relationships (a service *has a* cache, logger, transport).
- You anticipate multiple orthogonal variations (e.g., retry policy × transport × auth).
- You want to avoid deep inheritance trees.

## Object Oriented Design Patterns

Please refer to the following catalogue for info about the most common OOP design patterns: [https://refactoring.guru/design-patterns/catalog](https://refactoring.guru/design-patterns/catalog)

## Example of Poor Code Design

```python
import json
import time
import random

# Global mutable state (hard to test, hard to reason about)
DB = {}
LOGS = []
CONFIG = {"discount": 0.17, "tax": 0.0925}  # magic numbers, no validation

def do_everything(user_id, items, coupons=[], verbose=True):  # mutable default arg
    """God function: auth-ish checks, pricing, persistence, logging, notifications."""
    global DB, LOGS, CONFIG

    # Side effects + nondeterminism
    if random.random() < 0.1:
        CONFIG["discount"] = 0.99  # silently mutating global config

    try:
        # "Validation" mixed with business logic and persistence concerns
        if not user_id:
            raise Exception("no user")
        if "admin" in str(user_id).lower():  # bizarre implicit auth rule
            CONFIG["tax"] = 0.0

        # Pricing (magic logic, unclear units)
        subtotal = 0
        for name, price in items:
            if price < 0:
                price = price * -1  # silently "fix" bad data
            subtotal += price

        # Coupons applied inconsistently
        if coupons:
            for c in coupons:
                if "FREE" in c:
                    subtotal = 0
                else:
                    subtotal -= 5  # random flat reduction

        total = subtotal * (1 - CONFIG["discount"])
        total = total + (total * CONFIG["tax"])
        if total < 0:
            total = 0

        # Persistence (no schema, no separation, overwrites silently)
        DB[user_id] = {
            "items": json.dumps(items),      # storing serialized blobs
            "coupons": ",".join(coupons),    # lossy representation
            "total": str(total),             # wrong type
            "timestamp": time.time(),
        }

        # Logging with sensitive-ish data and no structure
        if verbose:
            LOGS.append(f"CHARGE {user_id} ${total} items={items} coupons={coupons}")

        # Hidden I/O behavior (pretend to notify)
        if total > 1000:
            print("EMAIL SENT TO FINANCE!")  # hard-coded, untestable side effect

        return total

    except Exception:
        # Swallowing all errors makes failures silent and debugging painful
        return "ok"

# Example usage
print(do_everything("AdminUser", [("widget", 100), ("gizmo", -50)], coupons=["SAVE10", "FREE"]))
print(DB)
print(LOGS)
```

## Example of Quality OOP Design

```python
from __future__ import annotations

from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import Iterable, Protocol

# --------- Domain model ---------

@dataclass(frozen=True)
class LineItem:
    sku: str
    quantity: int
    unit_price: float

@dataclass(frozen=True)
class Order:
    id: str
    items: tuple[LineItem, ...]

# --------- Abstractions (ABCs / interfaces) ---------

class PricingRule(ABC):
    """A pluggable business rule for pricing (e.g., discounts, promotions)."""

    @abstractmethod
    def apply(self, subtotal: float, order: Order) -> float:
        """Return the new subtotal after applying this rule."""
        raise NotImplementedError

class TaxPolicy(ABC):
    """Tax calculation policy. Different jurisdictions can swap implementations."""

    @abstractmethod
    def tax_for(self, taxable_amount: float, order: Order) -> float:
        raise NotImplementedError

class OrderRepository(ABC):
    """Persistence boundary (infrastructure behind an abstraction)."""

    @abstractmethod
    def save_total(self, order_id: str, total: float) -> None:
        raise NotImplementedError

class Logger(Protocol):
    """Small interface via Protocol (keeps dependencies minimal)."""
    def info(self, message: str) -> None: ...

# --------- Concrete implementations (inheritance) ---------

class PercentDiscount(PricingRule):
    def __init__(self, percent: float) -> None:
        if not (0.0 <= percent <= 1.0):
            raise ValueError("percent must be between 0 and 1")
        self._percent = percent

    def apply(self, subtotal: float, order: Order) -> float:
        return subtotal * (1.0 - self._percent)

class BulkSkuDiscount(PricingRule):
    """$ off per unit if an SKU meets a quantity threshold."""
    def __init__(self, sku: str, min_qty: int, dollars_off_per_unit: float) -> None:
        self._sku = sku
        self._min_qty = min_qty
        self._off = dollars_off_per_unit

    def apply(self, subtotal: float, order: Order) -> float:
        discount = 0.0
        for item in order.items:
            if item.sku == self._sku and item.quantity >= self._min_qty:
                discount += item.quantity * self._off
        return max(0.0, subtotal - discount)

class FlatRateTax(TaxPolicy):
    def __init__(self, rate: float) -> None:
        if not (0.0 <= rate <= 1.0):
            raise ValueError("rate must be between 0 and 1")
        self._rate = rate

    def tax_for(self, taxable_amount: float, order: Order) -> float:
        return taxable_amount * self._rate

class InMemoryOrderRepository(OrderRepository):
    def __init__(self) -> None:
        self._totals: dict[str, float] = {}

    def save_total(self, order_id: str, total: float) -> None:
        self._totals[order_id] = total

    def get_total(self, order_id: str) -> float:
        return self._totals[order_id]

class PrintLogger:
    def info(self, message: str) -> None:
        print(message)

# --------- Composition: orchestrator depends on abstractions ---------

class CheckoutService:
    """
    Single responsibility: compute totals and persist them.
    Uses composition to depend on pluggable rules/policies and infrastructure.
    """

    def __init__(
        self,
        pricing_rules: Iterable[PricingRule],
        tax_policy: TaxPolicy,
        repo: OrderRepository,
        logger: Logger,
    ) -> None:
        self._pricing_rules = tuple(pricing_rules)
        self._tax_policy = tax_policy
        self._repo = repo
        self._logger = logger

    def total_for(self, order: Order) -> float:
        subtotal = sum(i.quantity * i.unit_price for i in order.items)

        for rule in self._pricing_rules:
            subtotal = rule.apply(subtotal, order)

        tax = self._tax_policy.tax_for(subtotal, order)
        total = round(subtotal + tax, 2)

        self._repo.save_total(order.id, total)
        self._logger.info(f"Order {order.id} total = {total:.2f}")

        return total

# --------- Example usage ---------

if __name__ == "__main__":
    order = Order(
        id="A1001",
        items=(
            LineItem("WIDGET", 3, 19.99),
            LineItem("GIZMO", 10, 2.50),
        ),
    )

    repo = InMemoryOrderRepository()
    service = CheckoutService(
        pricing_rules=[
            PercentDiscount(0.10),                # inheritance via PricingRule
            BulkSkuDiscount("GIZMO", 10, 0.25),    # another rule, same abstraction
        ],
        tax_policy=FlatRateTax(0.0925),
        repo=repo,
        logger=PrintLogger(),
    )

    service.total_for(order)
    print("Persisted total:", repo.get_total(order.id))
```