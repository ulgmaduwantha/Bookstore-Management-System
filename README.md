# Bookstore-Management-System

### Chapter Lane – Bookstore Management System

**Contributor:** Member 4_U.L.G.Maduwantha  
**Module:** Order Management

---
## Overview

This module handles the complete order lifecycle for the Chapter Lane bookstore system — from checkout through fulfilment, cancellation, and admin management. It covers everything a customer needs to place and track orders, and everything an admin needs to manage and update them.

---
## Files in This Module

```
src/
└── main/
    ├── java/com/bookstore/management/
    │   ├── controller/
    │   │   └── OrderController.java          # Customer-facing order HTTP endpoints
    │   ├── service/
    │   │   └── OrderService.java             # Business logic for all order operations
    │   ├── repository/
    │   │   └── OrderRepository.java          # Data access – reads/writes orders.jsonl
    │   ├── model/
    │   │   ├── OrderRecord.java              # Main order entity
    │   │   ├── OrderItem.java                # Single line item within an order
    │   │   └── OrderStatus.java              # Enum: PENDING → PAID → PACKING → SHIPPED → DELIVERED / CANCELLED
    │   └── dto/
    │       ├── CheckoutForm.java             # Checkout form input (shipping address + note)
    │       └── OrderStatusForm.java          # Admin status-update form input
    └── resources/templates/
        ├── orders.html                       # Customer order history & cancel page
        └── admin/orders.html                 # Admin order management page
```

---
## Responsibilities

### Customer Features
- **Checkout** — Customer submits a `CheckoutForm` with a shipping address and optional note. The service validates stock availability, creates the `OrderRecord`, decreases book stock, and clears the cart atomically.
- **Order History** — `/orders` lists all orders for the logged-in customer, sorted newest first, with item summaries, totals, and statuses.
- **Cancel Order** — A customer can cancel any order that has not yet been shipped or delivered. Cancelling restores book stock automatically.

### Admin Features (integrated into `AdminController` / admin views)
- **View All Orders** — `/admin/orders` lists every order in the system with search (by order number, customer name, or email) and status filter.
- **Update Status** — Admin can move an order through the status pipeline: `PENDING → PAID → PACKING → SHIPPED → DELIVERED`. Cancelled orders cannot be re-opened.
- **Cancel & Stock Restore** — Admin status update to `CANCELLED` automatically restores stock for every item in the order.
- **Delete Order** — Only `CANCELLED` or `DELIVERED` orders may be deleted.

---
## Order Status Flow

```
PENDING ──► PAID ──► PACKING ──► SHIPPED ──► DELIVERED
   │                                │
   └──────────────── CANCELLED ◄────┘
        (stock restored on cancel)
```

- Customers can cancel from `PENDING` or `PAID` or `PACKING` states.
- Admins can cancel from any state except `DELIVERED`.
- Once `CANCELLED`, the status cannot be changed back.

---
## Key Classes

### `OrderRecord`
The main entity persisted to `orders.jsonl`. Stores order number, customer snapshot (name, email), shipping address, note, status, timestamps, and the list of `OrderItem`s.

Computed helpers (annotated `@JsonIgnore`, not persisted):
- `getGrandTotal()` — sum of all line totals
- `getItemCount()` — total quantity across all items
- `getStatusTone()` — CSS class name for status badge colouring

### `OrderItem`
A snapshot of a book at the time of purchase: book ID, title, format label, quantity, and unit price. Stock changes do not affect historical order items.

Computed helper:
- `getLineTotal()` — `unitPrice × quantity`

### `OrderStatus` (enum)
`PENDING`, `PAID`, `PACKING`, `SHIPPED`, `DELIVERED`, `CANCELLED`

### `CheckoutForm`
Validated with `@NotBlank` on `shippingAddress`. Optional free-text `note` field.

### `OrderStatusForm`
Validated with `@NotNull` on `status`. Used by the admin update endpoint.

---
## HTTP Endpoints

| Method | URL | Description |
|--------|-----|-------------|
| `GET`  | `/orders` | Customer order history page |
| `POST` | `/orders/checkout` | Place a new order from cart |
| `POST` | `/orders/{orderId}/cancel` | Customer cancels an order |

Admin endpoints (handled in `AdminController`, listed here for context):

| Method | URL | Description |
|--------|-----|-------------|
| `GET`  | `/admin/orders` | Admin order list with search & filter |
| `POST` | `/admin/orders/{id}/status` | Update order status |
| `POST` | `/admin/orders/{id}/delete` | Delete a completed/cancelled order |

---
## Dependencies on Other Modules

This module integrates with the following team members' work:

| Dependency | Used For |
|---|---|
| `CartService` | Reading cart items at checkout; clearing cart after order placed |
| `BookService` | Stock validation at checkout; `decreaseStock` / `increaseStock` |
| `CustomerService` | Looking up the customer entity by ID |
| `SessionService` | Resolving the current logged-in customer from the HTTP session |
| `JsonLineFileRepository` | Base class for `OrderRepository`; handles JSONL persistence |
| `IdGenerator` | Generating unique order IDs and human-readable order numbers |

---
