# Single Responsibility Principle (SRP)

## **Definition**

 A module/class/function should have **one responsibility**—meaning **one primary reason to change**. SRP isn’t “do one thing” in a tiny sense; it’s about **cohesive ownership of a concept** (e.g., pricing rules, persistence, formatting, transport).

## **Why it matters (in Python teams):**

- **Change isolation:** When requirements shift (new output format, new DB, new validation rule), you update one place without unintended side effects.
- **Testability:** Smaller, focused units are easier to unit test without heavy fixtures/mocking.
- **Reuse and composability:** Focused components compose cleanly (especially with dependency injection and protocols/ABCs).
- **Lower cognitive load:** Readers can understand intent faster when responsibilities aren’t mixed (I/O + business rules + formatting + error handling).

## **How to spot SRP violations (common smells):**

- A class/file has mixed concerns: **business logic + persistence + API/HTTP + formatting + logging** all together.
- Methods with names like `process_*`, `handle_*`, `do_everything()` that branch on flags, types, or environment.
- Frequent changes for unrelated reasons (e.g., “we changed the email template and accidentally broke invoice calculation”).
- Unit tests require lots of patching of `requests`, filesystem, DB, and time in the same test.

## **Practical guidance (Python-friendly):**

- Separate concerns into layers:
    - **Domain/business rules** (pure logic; minimal dependencies)
    - **Application orchestration** (coordinates steps; calls dependencies)
    - **Infrastructure/adapters** (DB, HTTP clients, file I/O, queues)
    - **Presentation/formatting** (CLI output, HTML/JSON rendering, email templates)
- Prefer **dependency injection** (pass collaborators in) over importing globals; it keeps responsibilities explicit and testable.
- Aim for “**pure core, impure edges**”: keep side effects (I/O, network, DB) at boundaries so core logic stays cohesive.

## Code Example of What to Avoid

```python
import json
import smtplib
import sqlite3
from email.message import EmailMessage
from datetime import datetime

class OrderProcessor:
    """
    Intentionally BAD SRP:
    - validates input
    - applies business rules (pricing/tax)
    - persists to DB
    - formats JSON
    - sends email
    - logs/prints
    """
    def __init__(self, db_path: str, smtp_host: str):
        self.db_path = db_path
        self.smtp_host = smtp_host

    def process(self, order: dict) -> str:
        # 1) Validation (concern #1)
        if "customer_email" not in order:
            raise ValueError("Missing customer_email")
        if "items" not in order or not order["items"]:
            raise ValueError("Missing items")

        # 2) Business rules (concern #2)
        subtotal = sum(i["unit_price"] * i["qty"] for i in order["items"])
        discount = 0.10 * subtotal if subtotal > 200 else 0
        taxed = (subtotal - discount) * 1.08  # hard-coded tax rule
        total = round(taxed, 2)

        # 3) Persistence (concern #3)
        conn = sqlite3.connect(self.db_path)
        cur = conn.cursor()
        cur.execute(
            "CREATE TABLE IF NOT EXISTS orders (id TEXT, total REAL, created_at TEXT)"
        )
        order_id = order.get("id") or f"ORD-{int(datetime.utcnow().timestamp())}"
        cur.execute(
            "INSERT INTO orders (id, total, created_at) VALUES (?, ?, ?)",
            (order_id, total, datetime.utcnow().isoformat()),
        )
        conn.commit()
        conn.close()

        # 4) Formatting / serialization (concern #4)
        payload = {
            "order_id": order_id,
            "total": total,
            "currency": order.get("currency", "USD"),
            "line_count": len(order["items"]),
        }
        json_receipt = json.dumps(payload, indent=2)

        # 5) Email transport (concern #5)
        msg = EmailMessage()
        msg["Subject"] = f"Your receipt: {order_id}"
        msg["From"] = "no-reply@example.com"
        msg["To"] = order["customer_email"]
        msg.set_content(json_receipt)

        with smtplib.SMTP(self.smtp_host) as smtp:
            smtp.send_message(msg)

        # 6) Logging (concern #6)
        print(f"[INFO] Processed {order_id} total={total}")

        return json_receipt
```

## Code Example of Strong SRP Design

```python
from __future__ import annotations

import json
import sqlite3
import smtplib
from dataclasses import dataclass
from datetime import datetime
from email.message import EmailMessage
from typing import Protocol, Iterable

# -----------------------------
# Domain model (data only)
# -----------------------------
@dataclass(frozen=True)
class LineItem:
    sku: str
    unit_price: float
    qty: int

@dataclass(frozen=True)
class Order:
    id: str
    customer_email: str
    items: tuple[LineItem, ...]
    currency: str = "USD"

# -----------------------------
# Validation (one reason to change: rules for input validity)
# -----------------------------
class OrderValidator:
    def validate(self, order: Order) -> None:
        if not order.customer_email:
            raise ValueError("customer_email is required")
        if not order.items:
            raise ValueError("at least one line item is required")
        for item in order.items:
            if item.qty <= 0:
                raise ValueError(f"qty must be > 0 for {item.sku}")
            if item.unit_price < 0:
                raise ValueError(f"unit_price must be >= 0 for {item.sku}")

# -----------------------------
# Business rules (one reason to change: pricing/tax policy)
# -----------------------------
@dataclass(frozen=True)
class PricingResult:
    subtotal: float
    discount: float
    tax: float
    total: float

class PricingPolicy:
    def price(self, items: Iterable[LineItem]) -> PricingResult:
        subtotal = sum(i.unit_price * i.qty for i in items)
        discount = 0.10 * subtotal if subtotal > 200 else 0.0
        taxable = subtotal - discount
        tax = 0.08 * taxable
        total = round(taxable + tax, 2)
        return PricingResult(
            subtotal=round(subtotal, 2),
            discount=round(discount, 2),
            tax=round(tax, 2),
            total=total,
        )

# -----------------------------
# Persistence (one reason to change: storage mechanism/schema)
# -----------------------------
class OrderRepository(Protocol):
    def save_total(self, order_id: str, total: float, created_at: datetime) -> None: ...

class SqliteOrderRepository:
    def __init__(self, db_path: str):
        self._db_path = db_path

    def save_total(self, order_id: str, total: float, created_at: datetime) -> None:
        conn = sqlite3.connect(self._db_path)
        cur = conn.cursor()
        cur.execute(
            "CREATE TABLE IF NOT EXISTS orders (id TEXT PRIMARY KEY, total REAL, created_at TEXT)"
        )
        cur.execute(
            "INSERT OR REPLACE INTO orders (id, total, created_at) VALUES (?, ?, ?)",
            (order_id, float(total), created_at.isoformat()),
        )
        conn.commit()
        conn.close()

# -----------------------------
# Rendering/formatting (one reason to change: receipt structure/output format)
# -----------------------------
class ReceiptRenderer:
    def render_json(self, order: Order, pricing: PricingResult) -> str:
        payload = {
            "order_id": order.id,
            "currency": order.currency,
            "line_count": len(order.items),
            "subtotal": pricing.subtotal,
            "discount": pricing.discount,
            "tax": pricing.tax,
            "total": pricing.total,
        }
        return json.dumps(payload, indent=2)

# -----------------------------
# Notification (one reason to change: email provider/transport)
# -----------------------------
class EmailSender(Protocol):
    def send(self, to: str, subject: str, body: str) -> None: ...

class SmtpEmailSender:
    def __init__(self, host: str, from_addr: str):
        self._host = host
        self._from = from_addr

    def send(self, to: str, subject: str, body: str) -> None:
        msg = EmailMessage()
        msg["Subject"] = subject
        msg["From"] = self._from
        msg["To"] = to
        msg.set_content(body)
        with smtplib.SMTP(self._host) as smtp:
            smtp.send_message(msg)

# -----------------------------
# Orchestration (one reason to change: workflow sequencing)
# -----------------------------
class OrderCheckoutService:
    def __init__(
        self,
        validator: OrderValidator,
        pricing: PricingPolicy,
        repo: OrderRepository,
        renderer: ReceiptRenderer,
        emailer: EmailSender,
    ):
        self._validator = validator
        self._pricing = pricing
        self._repo = repo
        self._renderer = renderer
        self._emailer = emailer

    def checkout(self, order: Order) -> str:
        self._validator.validate(order)

        pricing_result = self._pricing.price(order.items)

        now = datetime.utcnow()
        self._repo.save_total(order.id, pricing_result.total, now)

        receipt = self._renderer.render_json(order, pricing_result)

        self._emailer.send(
            to=order.customer_email,
            subject=f"Your receipt: {order.id}",
            body=receipt,
        )

        return receipt

# -----------------------------
# Example usage
# -----------------------------
if __name__ == "__main__":
    order = Order(
        id="ORD-123",
        customer_email="customer@example.com",
        items=(
            LineItem(sku="SKU-1", unit_price=120.0, qty=1),
            LineItem(sku="SKU-2", unit_price=50.0, qty=2),
        ),
    )

    service = OrderCheckoutService(
        validator=OrderValidator(),
        pricing=PricingPolicy(),
        repo=SqliteOrderRepository(db_path="orders.db"),
        renderer=ReceiptRenderer(),
        emailer=SmtpEmailSender(host="localhost", from_addr="no-reply@example.com"),
    )

    print(service.checkout(order))
```