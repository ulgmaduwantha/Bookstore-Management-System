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
