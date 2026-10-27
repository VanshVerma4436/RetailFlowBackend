# RetailFlow Backend

A production-oriented **Spring Boot REST API for retail inventory, billing, sales, and stock management**.

RetailFlow is designed for small and medium-sized retail businesses to manage products, track inventory, process sales, generate invoices, and maintain a reliable record of stock movements.

The backend provides the business logic and REST APIs consumed by the RetailFlow frontend.

---

## ✨ Features

### 🔐 Authentication & Security

- JWT-based authentication
- User authentication and authorization
- Secure password handling
- Role-based access control
- Protected API endpoints

### 📦 Product Management

- Create products
- Update products
- Delete products
- Get product details
- Search products by barcode
- Track selling price and cost price
- Track current stock
- Configure minimum stock thresholds
- Product categories

### 📊 Inventory Management

- Stock-in transactions
- Stock-out transactions
- Manual stock adjustments
- Inventory transaction history
- Previous and new stock tracking
- Low-stock threshold support
- Transaction reasons

### 🛒 Sales & Checkout

- Cart-based checkout
- Multiple products per sale
- Duplicate product aggregation
- Stock availability validation
- Automatic inventory deduction
- Multiple payment methods
- Transactional checkout
- Unique invoice generation
- Sale total calculation
- Discount handling

### 🧾 Invoice Management

- Generate unique invoice numbers
- Find sales by invoice number
- Retrieve individual sales
- Invoice lookup for previous transactions
- Support for invoice reprinting workflows

### 📈 Sales Reports

- Sales report by date range
- Latest sales ordering
- Historical sales lookup
- Sale information including subtotal, discount, total and payment method

### ↩️ Sale Management

- Void completed sales
- Prevent already-voided sales from being voided again
- Automatically restore inventory when a sale is voided

### 🏷️ Barcode Support

- Barcode generation support using ZXing
- Product barcode lookup
- Designed for barcode-scanner based retail workflows

---

## 🧠 Key Backend Design

RetailFlow is not just a CRUD application. The backend handles important retail business rules such as:

### Transactional Checkout

Checkout is performed inside a database transaction.

The backend:

```text
Receive Cart
     ↓
Validate Request
     ↓
Aggregate Product Quantities
     ↓
Lock Products
     ↓
Validate Stock
     ↓
Calculate Subtotal
     ↓
Validate Discount
     ↓
Create Sale Items
     ↓
Reduce Inventory
     ↓
Calculate Final Total
     ↓
Save Sale
```

This helps prevent inconsistent stock and sale data when checkout fails.

### Concurrent Stock Protection

Products are locked during checkout before stock is modified.

Products are also processed in a consistent order to reduce the possibility of deadlocks when multiple checkouts operate on overlapping products.

### Sale Voiding

When a sale is voided, the associated stock is restored automatically.

```text
Sale
 ↓
Void Sale
 ↓
Restore Stock
 ↓
Mark Sale as Voided
```

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Java 25 | Backend language |
| Spring Boot 4.1 | Application framework |
| Spring Web MVC | REST APIs |
| Spring Data JPA | Database access |
| Spring Security | Authentication & authorization |
| JWT | Stateless authentication |
| PostgreSQL | Production database |
| H2 | Testing/local database support |
| Maven | Build & dependency management |
| Docker Compose | Local PostgreSQL environment |
| ZXing | Barcode generation |
| Lombok | Boilerplate reduction |
| Jakarta Validation | Request validation |

---

## 🏗️ Project Structure

```text
src/
├── main/
│   ├── java/com/creator/RetailFlow/
│   │
│   ├── config/
│   │   └── SecurityConfig
│   │
│   ├── auth/
│   │   ├── controller/
│   │   ├── dto/
│   │   ├── entity/
│   │   ├── repository/
│   │   └── service/
│   │
│   ├── product/
│   │   ├── controller/
│   │   ├── dto/
│   │   ├── entity/
│   │   ├── repository/
│   │   └── service/
│   │
│   ├── inventory/
│   │   ├── controller/
│   │   ├── dto/
│   │   ├── entity/
│   │   ├── repository/
│   │   └── service/
│   │
│   ├── sale/
│   │   ├── controller/
│   │   ├── dto/
│   │   ├── entity/
│   │   ├── repository/
│   │   └── service/
│   │
│   └── exception/
│       ├── GlobalExceptionHandler
│       └── ResourceNotFoundException
│
└── resources/
    ├── application.properties
    └── migration/
```

---

## 🔌 API Modules

### Authentication

```text
/api/auth
```

Handles user authentication and JWT token generation.

### Products

```text
/api/products
```

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/products` | Create product |
| GET | `/api/products` | Get products |
| GET | `/api/products/{id}` | Get product |
| GET | `/api/products/barcode/{barcode}` | Find by barcode |
| PUT | `/api/products/{id}` | Update product |
| DELETE | `/api/products/{id}` | Delete product |

### Inventory

```text
/api/inventory
```

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/inventory/adjust` | Adjust stock |
| GET | `/api/inventory/logs/product/{productId}` | Get inventory history |

### Sales

```text
/api/sales
```

Includes:

- Checkout
- Invoice lookup
- Sale retrieval
- Sales reports
- Sale voiding

---

## 🗄️ Database

RetailFlow uses PostgreSQL as its primary database.

Core entities include:

```text
users
products
inventory_logs
sales
sale_items
```

### Product

```text
id
name
barcode
selling_price
cost_price
stock
min_stock
category
created_at
updated_at
```

### Inventory Log

```text
id
product_id
type
quantity
previous_stock
new_stock
reason
created_at
```

### Sale

```text
id
invoice_number
subtotal
discount
total
payment_method
created_at
voided
```

### Sale Item

```text
id
sale_id
product_id
product_name
barcode
price
quantity
subtotal
```

---

## 🚀 Getting Started

### Prerequisites

Make sure you have:

- Java 25
- Maven
- Docker
- Docker Compose
- PostgreSQL (if running without Docker)

### 1. Clone the repository

```bash
git clone https://github.com/mayankgupta-001/RetailFlowBackend.git

cd RetailFlowBackend
```

### 2. Configure environment variables

Create a `.env` file containing the required database and authentication configuration.

Example:

```env
DATABASE_USER=your_username
DATABASE_PASS=your_password

JWT_SECRET=your_secure_jwt_secret
```

Never commit real credentials or secrets to GitHub.

### 3. Start PostgreSQL

```bash
docker compose up -d
```

### 4. Run the application

Using Maven Wrapper:

```bash
./mvnw spring-boot:run
```

On Windows:

```bash
mvnw.cmd spring-boot:run
```

The API will be available locally through the configured Spring Boot port.

---

## 🧪 Testing

Run the test suite with:

```bash
./mvnw test
```

Windows:

```bash
mvnw.cmd test
```

Current tests cover important checkout scenarios including:

- Successful checkout
- Empty cart
- Missing product
- Insufficient stock
- Invalid product ID
- Invalid quantity
- Negative discount
- Discount greater than subtotal

---

## 🔄 Checkout Example

A checkout request follows the general flow:

```json
{
  "items": [
    {
      "productId": 1,
      "quantity": 2
    }
  ],
  "discount": 25.00,
  "paymentMethod": "CASH"
}
```

The backend calculates the sale:

```text
Product Price × Quantity
        ↓
      Subtotal
        ↓
     Discount
        ↓
    Final Total
```

The server then saves the sale and updates inventory within the transaction.

---

## 📋 Development Roadmap

### Completed

- [x] Product management
- [x] Inventory management
- [x] Barcode lookup
- [x] JWT authentication
- [x] Checkout
- [x] Invoice generation
- [x] Invoice lookup
- [x] Sale reports
- [x] Sale voiding
- [x] Inventory restoration on sale void
- [x] Checkout validation
- [x] Checkout service tests

### In Progress

- [ ] Enhanced discount validation
- [ ] Admin-controlled scheduled discounts
- [ ] Role-based authorization improvements
- [ ] Sales report pagination
- [ ] Additional integration tests

### Planned

- [ ] Flyway database migrations
- [ ] CI/CD with GitHub Actions
- [ ] Advanced analytics
- [ ] Low-stock notifications
- [ ] Improved audit logging

---

## 🎯 Project Goals

RetailFlow is being developed with a focus on real-world backend engineering rather than only basic CRUD functionality.

Key engineering goals include:

- Secure authentication
- Reliable transactional operations
- Consistent inventory management
- Backend-controlled business rules
- Database integrity
- Testable service logic
- Scalable REST APIs
- Clean separation of responsibilities

---

## 🔗 Related Project

### RetailFlow Frontend

The frontend provides the user interface for interacting with the RetailFlow backend.

[RetailFlow Frontend](https://github.com/mayankgupta-001/RetailFlowFrontend)

---

## 👨‍💻 Author

**Mayank Gupta**

B.Tech CSE (Hons) — Artificial Intelligence & Analytics  
GLA University, Mathura

---

## 📄 License

This project is currently intended for educational and portfolio development purposes.
