# ERP Dashboard

> **Note:** This repository is used as part of my mentor program.

A full-stack Enterprise Resource Planning (ERP) dashboard. This project is **technology-agnostic** — you can implement it using **any backend or frontend stack** of your choice.

---

## Table of Contents

- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Backend Setup](#backend-setup)
  - [Frontend Setup](#frontend-setup)
- [CRUD Operations](#crud-operations)
  - [1. Users & Roles](#1-users--roles)
  - [2. Customers](#2-customers)
  - [3. Products / Inventory](#3-products--inventory)
  - [4. Suppliers / Purchasing](#4-suppliers--purchasing)
  - [5. Orders / Sales](#5-orders--sales)
  - [6. Invoices / Finance](#6-invoices--finance)
  - [7. Employees / HR](#7-employees--hr)
- [API Reference](#api-reference)
- [Database Schema Overview](#database-schema-overview)

---

## Tech Stack

This project is **framework-agnostic**. You are free to choose any technology for each layer. Below are popular options for each:

| Layer      | Options (choose any)                                                                 |
|------------|--------------------------------------------------------------------------------------|
| Frontend   | Angular, React, Vue, Next.js, Blazor, or any SPA / SSR framework                    |
| Backend    | .NET (ASP.NET Core), Java (Spring Boot), PHP (Laravel), Python (Django / FastAPI), Node.js (Express / NestJS), or any REST API framework |
| Database   | SQL Server, PostgreSQL, MySQL, MongoDB, SQLite, or any relational / NoSQL database  |
| ORM        | Entity Framework, Hibernate, Eloquent, Django ORM, Prisma, TypeORM, etc.            |
| Auth       | JWT Bearer Tokens (recommended for all stacks)                                      |
| API Docs   | Swagger / OpenAPI (available for all major frameworks)                              |

---

## Project Structure

The recommended folder structure below is a **general guideline**. Adapt it to match your chosen framework's conventions.

```
dashboard/
├── backend/                    # Your backend API (any framework)
│   ├── controllers/            # API Controllers / Route handlers (one per module)
│   ├── models/                 # Entity / domain models
│   ├── dtos/                   # Data Transfer Objects (request & response shapes)
│   ├── services/               # Business logic layer
│   ├── repositories/           # Data access layer / ORM queries
│   └── config/                 # Database & app configuration
│
├── frontend/                   # Your frontend app (any framework)
│   ├── src/
│   │   ├── core/               # Auth guards, HTTP interceptors, global services
│   │   ├── shared/             # Reusable UI components
│   │   └── modules/            # Feature modules (one per ERP module)
│   └── (framework config files)
│
└── README.md
```

---

## Prerequisites

Install the runtime/SDK for your chosen stack. Examples:

**Backend**
- .NET: [.NET 8 SDK](https://dotnet.microsoft.com/download)
- Java: [JDK 17+](https://adoptium.net/) + [Maven](https://maven.apache.org/) or Gradle
- PHP: [PHP 8+](https://www.php.net/) + [Composer](https://getcomposer.org/) (for Laravel)
- Python: [Python 3.10+](https://www.python.org/) + pip (for Django / FastAPI)
- Node.js: [Node.js 18+](https://nodejs.org/) (for Express / NestJS)

**Frontend**
- Angular: [Node.js 18+](https://nodejs.org/) + `npm install -g @angular/cli`
- React / Vue / Next.js: [Node.js 18+](https://nodejs.org/) + npm or yarn
- Blazor: [.NET 8 SDK](https://dotnet.microsoft.com/download)

**Database** *(install any one)*
- [SQL Server](https://www.microsoft.com/en-us/sql-server) / [PostgreSQL](https://www.postgresql.org/) / [MySQL](https://www.mysql.com/) / [MongoDB](https://www.mongodb.com/)

---

## Getting Started

### Backend Setup

**Step 1 — Clone the repository**
```bash
git clone <repository-url>
cd dashboard/backend
```

**Step 2 — Install dependencies**

| Framework     | Command                        |
|---------------|--------------------------------|
| .NET          | *(restore happens automatically on build)* |
| Spring Boot   | `mvn install` or `gradle build` |
| Laravel       | `composer install`              |
| Django        | `pip install -r requirements.txt` |
| Express/NestJS| `npm install`                   |

**Step 3 — Configure the database connection**

Update your framework's config file with your database credentials:

| Framework     | Config file                    |
|---------------|--------------------------------|
| .NET          | `appsettings.json`             |
| Spring Boot   | `src/main/resources/application.properties` |
| Laravel       | `.env`                          |
| Django        | `settings.py`                   |
| Express/NestJS| `.env`                          |

**Step 4 — Run database migrations**

| Framework     | Command                                  |
|---------------|------------------------------------------|
| .NET (EF Core)| `dotnet ef database update`              |
| Spring Boot   | *(auto-runs on start with Hibernate)*    |
| Laravel       | `php artisan migrate`                    |
| Django        | `python manage.py migrate`               |
| TypeORM       | `npm run migration:run`                  |

**Step 5 — Start the API server**

| Framework     | Command                   | Default URL                  |
|---------------|---------------------------|------------------------------|
| .NET          | `dotnet run`              | `https://localhost:5001`     |
| Spring Boot   | `mvn spring-boot:run`     | `http://localhost:8080`      |
| Laravel       | `php artisan serve`       | `http://localhost:8000`      |
| Django        | `python manage.py runserver` | `http://localhost:8000`   |
| NestJS        | `npm run start:dev`       | `http://localhost:3000`      |

Swagger / API docs are typically available at `/swagger` or `/api/docs`.

---

### Frontend Setup

**Step 1 — Navigate to the frontend folder**
```bash
cd dashboard/frontend
```

**Step 2 — Install dependencies**

| Framework  | Command         |
|------------|-----------------|
| Angular    | `npm install`   |
| React      | `npm install`   |
| Vue        | `npm install`   |
| Next.js    | `npm install`   |
| Blazor     | *(no step needed — .NET handles it)* |

**Step 3 — Configure the API base URL**

Point your frontend to the backend API. Update the relevant config file:

| Framework  | File                                  | Example                              |
|------------|---------------------------------------|--------------------------------------|
| Angular    | `src/environments/environment.ts`     | `apiUrl: 'http://localhost:5001/api'` |
| React/Next | `.env`                                | `REACT_APP_API_URL=http://localhost:5001/api` |
| Vue        | `.env`                                | `VITE_API_URL=http://localhost:5001/api` |

**Step 4 — Start the development server**

| Framework  | Command       | Default URL               |
|------------|---------------|---------------------------|
| Angular    | `ng serve`    | `http://localhost:4200`   |
| React (CRA)| `npm start`   | `http://localhost:3000`   |
| Next.js    | `npm run dev` | `http://localhost:3000`   |
| Vue (Vite) | `npm run dev` | `http://localhost:5173`   |
| Blazor     | `dotnet watch`| `https://localhost:5001`  |

---

## CRUD Operations

> All API endpoints require a **Bearer Token** in the `Authorization` header unless marked as public.
> Obtain a token via `POST /api/auth/login`.

---

### 1. Users & Roles

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Users | `/users` | Users List Page |
| Create User | `/users/new` | User Form Page |
| Edit User | `/users/:id/edit` | User Form Page |
| Delete User | (button in list) | Users List Page |

**Step-by-step: Create a User**
1. Navigate to **Settings → Users → New User**.
2. Fill in: `First Name`, `Last Name`, `Email`, `Password`, `Role`.
3. Click **Save**. The form calls `POST /api/users`.
4. On success, you are redirected to the users list.

**Step-by-step: Edit a User**
1. In the users list, click the **Edit** (pencil) icon on the desired row.
2. Modify the required fields.
3. Click **Update**. The form calls `PUT /api/users/{id}`.

**Step-by-step: Delete a User**
1. In the users list, click the **Delete** (trash) icon.
2. Confirm the deletion in the dialog.
3. The UI calls `DELETE /api/users/{id}` and removes the row.

#### API Endpoints

```
GET    /api/users           — List all users
GET    /api/users/{id}      — Get user by ID
POST   /api/users           — Create user
PUT    /api/users/{id}      — Update user
DELETE /api/users/{id}      — Delete user

GET    /api/roles           — List all roles
POST   /api/roles           — Create role
PUT    /api/roles/{id}      — Update role
DELETE /api/roles/{id}      — Delete role
```

#### Sample Request: Create User
```http
POST /api/users
Content-Type: application/json
Authorization: Bearer <token>

{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@company.com",
  "password": "SecureP@ss1",
  "roleId": 2
}
```

---

### 2. Customers

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Customers | `/customers` | Customers List Page |
| Create Customer | `/customers/new` | Customer Form Page |
| Edit Customer | `/customers/:id/edit` | Customer Form Page |
| View Customer | `/customers/:id` | Customer Detail Page |
| Delete Customer | (button in list) | Customers List Page |

**Step-by-step: Create a Customer**
1. Navigate to **Customers → New Customer**.
2. Fill in: `Name`, `Email`, `Phone`, `Address`, `City`, `Country`, `Tax ID`.
3. Click **Save**. Calls `POST /api/customers`.
4. The new customer appears in the list.

**Step-by-step: Edit a Customer**
1. In the customers list, click **Edit** on the target row.
2. Update the required fields.
3. Click **Update**. Calls `PUT /api/customers/{id}`.

**Step-by-step: Delete a Customer**
1. Click **Delete** on the customer row.
2. Confirm in the modal dialog.
3. Calls `DELETE /api/customers/{id}`.

#### API Endpoints

```
GET    /api/customers           — List all customers (supports ?search=&page=&pageSize=)
GET    /api/customers/{id}      — Get customer by ID
POST   /api/customers           — Create customer
PUT    /api/customers/{id}      — Update customer
DELETE /api/customers/{id}      — Delete customer
```

#### Sample Request: Create Customer
```http
POST /api/customers
Content-Type: application/json
Authorization: Bearer <token>

{
  "name": "Acme Corp",
  "email": "info@acme.com",
  "phone": "+1-555-000-1234",
  "address": "123 Main St",
  "city": "New York",
  "country": "USA",
  "taxId": "US123456789"
}
```

---

### 3. Products / Inventory

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Products | `/products` | Products List Page |
| Create Product | `/products/new` | Product Form Page |
| Edit Product | `/products/:id/edit` | Product Form Page |
| Stock Adjustment | `/products/:id/stock` | Stock Adjustment Page |
| Delete Product | (button in list) | Products List Page |

**Step-by-step: Create a Product**
1. Navigate to **Inventory → Products → New Product**.
2. Fill in: `Name`, `SKU`, `Category`, `Unit Price`, `Cost Price`, `Stock Quantity`, `Reorder Level`.
3. Upload a product image *(optional)*.
4. Click **Save**. Calls `POST /api/products`.

**Step-by-step: Adjust Stock**
1. Open a product and click **Adjust Stock**.
2. Enter the quantity change (positive = restock, negative = deduction) and a reason.
3. Click **Apply**. Calls `PATCH /api/products/{id}/stock`.

**Step-by-step: Delete a Product**
1. Click **Delete** on the product row.
2. Note: Products tied to existing orders cannot be deleted — archive them instead.
3. Confirms with `DELETE /api/products/{id}`.

#### API Endpoints

```
GET    /api/products                   — List products (supports ?category=&lowStock=true)
GET    /api/products/{id}              — Get product by ID
POST   /api/products                   — Create product
PUT    /api/products/{id}              — Update product
PATCH  /api/products/{id}/stock        — Adjust stock quantity
DELETE /api/products/{id}              — Delete product
GET    /api/categories                 — List product categories
```

#### Sample Request: Create Product
```http
POST /api/products
Content-Type: application/json
Authorization: Bearer <token>

{
  "name": "Laptop Pro 15",
  "sku": "LP-15-001",
  "categoryId": 3,
  "unitPrice": 1299.99,
  "costPrice": 950.00,
  "stockQuantity": 50,
  "reorderLevel": 10
}
```

---

### 4. Suppliers / Purchasing

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Suppliers | `/suppliers` | Suppliers List Page |
| Create Supplier | `/suppliers/new` | Supplier Form Page |
| Edit Supplier | `/suppliers/:id/edit` | Supplier Form Page |
| Create Purchase Order | `/purchasing/new` | Purchase Order Form Page |
| Delete Supplier | (button in list) | Suppliers List Page |

**Step-by-step: Create a Supplier**
1. Navigate to **Purchasing → Suppliers → New Supplier**.
2. Fill in: `Company Name`, `Contact Name`, `Email`, `Phone`, `Address`, `Payment Terms`.
3. Click **Save**. Calls `POST /api/suppliers`.

**Step-by-step: Create a Purchase Order**
1. Navigate to **Purchasing → New Purchase Order**.
2. Select a `Supplier` from the dropdown.
3. Add line items: select `Product`, enter `Quantity` and `Unit Cost`.
4. Set `Expected Delivery Date`.
5. Click **Submit Order**. Calls `POST /api/purchase-orders`.

**Step-by-step: Delete a Supplier**
1. Click **Delete** on the supplier row.
2. Confirm in the dialog. Calls `DELETE /api/suppliers/{id}`.

#### API Endpoints

```
GET    /api/suppliers                  — List suppliers
GET    /api/suppliers/{id}             — Get supplier by ID
POST   /api/suppliers                  — Create supplier
PUT    /api/suppliers/{id}             — Update supplier
DELETE /api/suppliers/{id}             — Delete supplier

GET    /api/purchase-orders            — List purchase orders
GET    /api/purchase-orders/{id}       — Get purchase order
POST   /api/purchase-orders            — Create purchase order
PUT    /api/purchase-orders/{id}       — Update purchase order
PATCH  /api/purchase-orders/{id}/status — Update order status (Pending/Received/Cancelled)
```

---

### 5. Orders / Sales

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Orders | `/orders` | Orders List Page |
| Create Order | `/orders/new` | Order Form Page |
| View Order | `/orders/:id` | Order Detail Page |
| Edit Order | `/orders/:id/edit` | Order Form Page |
| Delete Order | (button in list) | Orders List Page |

**Step-by-step: Create a Sales Order**
1. Navigate to **Sales → Orders → New Order**.
2. Select a `Customer` from the dropdown (or create a new one inline).
3. Add order lines: select `Product`, enter `Quantity`; the unit price auto-fills.
4. Apply a discount *(optional)*.
5. Select `Payment Method` and `Shipping Address`.
6. Click **Place Order**. Calls `POST /api/orders`.
7. An invoice is automatically created.

**Step-by-step: Update Order Status**
1. Open the order detail page.
2. Click **Update Status** and choose: `Pending → Confirmed → Shipped → Delivered`.
3. Calls `PATCH /api/orders/{id}/status`.

**Step-by-step: Cancel/Delete an Order**
1. Only orders in **Pending** status can be deleted.
2. Click **Cancel Order**, confirm in the dialog.
3. Calls `DELETE /api/orders/{id}`.

#### API Endpoints

```
GET    /api/orders                     — List orders (supports ?status=&customerId=&from=&to=)
GET    /api/orders/{id}                — Get order by ID
POST   /api/orders                     — Create order
PUT    /api/orders/{id}                — Update order
PATCH  /api/orders/{id}/status         — Update order status
DELETE /api/orders/{id}                — Cancel/delete order
```

#### Sample Request: Create Order
```http
POST /api/orders
Content-Type: application/json
Authorization: Bearer <token>

{
  "customerId": 12,
  "orderDate": "2026-06-13",
  "paymentMethod": "CreditCard",
  "shippingAddress": "123 Main St, New York, USA",
  "lines": [
    { "productId": 5, "quantity": 2, "unitPrice": 1299.99, "discount": 0 }
  ]
}
```

---

### 6. Invoices / Finance

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Invoices | `/invoices` | Invoices List Page |
| Create Invoice | `/invoices/new` | Invoice Form Page |
| View Invoice | `/invoices/:id` | Invoice Detail Page |
| Mark as Paid | (button in detail/list) | Invoice Detail Page |
| Delete Invoice | (button in list) | Invoices List Page |

**Step-by-step: Create a Manual Invoice**
1. Navigate to **Finance → Invoices → New Invoice**.
2. Select a `Customer`.
3. Set `Invoice Date` and `Due Date`.
4. Add line items with `Description`, `Quantity`, and `Unit Price`.
5. Select `Tax Rate` *(optional)*.
6. Click **Generate Invoice**. Calls `POST /api/invoices`.

**Step-by-step: Mark Invoice as Paid**
1. Open the invoice detail page.
2. Click **Mark as Paid**.
3. Enter `Payment Date` and `Payment Method`.
4. Calls `PATCH /api/invoices/{id}/pay`.

**Step-by-step: Delete an Invoice**
1. Only **Draft** invoices can be deleted.
2. Click **Delete**, confirm the dialog.
3. Calls `DELETE /api/invoices/{id}`.

**Step-by-step: Export Invoice as PDF**
1. Open the invoice detail page.
2. Click **Download PDF**.
3. Calls `GET /api/invoices/{id}/pdf` — returns a PDF file stream.

#### API Endpoints

```
GET    /api/invoices                   — List invoices (supports ?status=&customerId=&from=&to=)
GET    /api/invoices/{id}              — Get invoice by ID
GET    /api/invoices/{id}/pdf          — Download invoice as PDF
POST   /api/invoices                   — Create invoice
PUT    /api/invoices/{id}              — Update invoice
PATCH  /api/invoices/{id}/pay          — Mark invoice as paid
DELETE /api/invoices/{id}              — Delete invoice (draft only)
```

---

### 7. Employees / HR

#### Frontend Steps

| Action | Route | Page / Component |
|--------|-------|-----------------|
| List Employees | `/employees` | Employees List Page |
| Create Employee | `/employees/new` | Employee Form Page |
| Edit Employee | `/employees/:id/edit` | Employee Form Page |
| View Profile | `/employees/:id` | Employee Detail Page |
| Delete Employee | (button in list) | Employees List Page |

**Step-by-step: Create an Employee**
1. Navigate to **HR → Employees → New Employee**.
2. Fill in: `First Name`, `Last Name`, `Email`, `Phone`, `Department`, `Position`, `Hire Date`, `Salary`.
3. Upload a profile photo *(optional)*.
4. Click **Save**. Calls `POST /api/employees`.

**Step-by-step: Edit an Employee**
1. In the employees list, click **Edit** on the target row.
2. Update the fields (e.g., change department or salary).
3. Click **Update**. Calls `PUT /api/employees/{id}`.

**Step-by-step: Delete an Employee**
1. Click **Delete** on the employee row.
2. Confirm in the dialog.
3. Calls `DELETE /api/employees/{id}`.

#### API Endpoints

```
GET    /api/employees                  — List employees (supports ?department=&search=)
GET    /api/employees/{id}             — Get employee by ID
POST   /api/employees                  — Create employee
PUT    /api/employees/{id}             — Update employee
DELETE /api/employees/{id}             — Delete employee
GET    /api/departments                — List departments
```

#### Sample Request: Create Employee
```http
POST /api/employees
Content-Type: application/json
Authorization: Bearer <token>

{
  "firstName": "Sara",
  "lastName": "Ahmed",
  "email": "sara.ahmed@company.com",
  "phone": "+20-555-123456",
  "departmentId": 4,
  "position": "Senior Developer",
  "hireDate": "2026-06-13",
  "salary": 8500.00
}
```

---

## API Reference

### Authentication

```
POST /api/auth/login        — Login and obtain JWT token
POST /api/auth/refresh      — Refresh JWT token
POST /api/auth/logout       — Logout
```

**Login Request:**
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "admin@company.com",
  "password": "Admin@1234"
}
```

**Login Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "dGhpcyBpcy...",
  "expiresAt": "2026-06-14T10:00:00Z"
}
```

Use the `token` value in all subsequent requests:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

---

## Database Schema Overview

```
Users          — id, firstName, lastName, email, passwordHash, roleId
Roles          — id, name, permissions (JSON)

Customers      — id, name, email, phone, address, city, country, taxId
Suppliers      — id, companyName, contactName, email, phone, paymentTerms

Categories     — id, name
Products       — id, name, sku, categoryId, unitPrice, costPrice, stockQuantity, reorderLevel

PurchaseOrders — id, supplierId, orderDate, expectedDelivery, status, totalAmount
PurchaseLines  — id, purchaseOrderId, productId, quantity, unitCost

Orders         — id, customerId, orderDate, status, paymentMethod, shippingAddress, totalAmount
OrderLines     — id, orderId, productId, quantity, unitPrice, discount

Invoices       — id, orderId, customerId, invoiceDate, dueDate, status, totalAmount, paidAt
InvoiceLines   — id, invoiceId, description, quantity, unitPrice, taxRate

Employees      — id, firstName, lastName, email, phone, departmentId, position, hireDate, salary
Departments    — id, name
```

---

## Notes

- All list endpoints support **pagination** via `?page=1&pageSize=20`.
- All list endpoints support **search** via `?search=keyword`.
- Soft deletes are used for Customers, Products, and Employees (records are marked as `isDeleted = true` rather than removed).
- Date fields follow **ISO 8601** format: `YYYY-MM-DD`.
- Monetary values are in the base currency configured in your backend's settings file (e.g., `appsettings.json`, `.env`, `settings.py`, etc.).
