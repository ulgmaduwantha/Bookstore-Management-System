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
