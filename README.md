
#  Order Management API

A Django REST Framework (DRF) based API for managing **users, products, and orders** with role-based access for **customers** and **admins**.

---

## 1. ⚙ Setup Instructions & Installation

### Prerequisites
- Python 3.8+
- pip (Python package manager)
- Git (optional)

### Step-by-Step Installation
```bash
# Create and activate virtual environment
python -m venv venv

# Linux/Mac
source venv/bin/activate

# Windows
venv\Scripts\activate

# Install dependencies
pip install django djangorestframework djangorestframework-simplejwt

# Clone or create project directory (if needed)
# git clone <your-repository>
# cd order-management-api

# Run migrations
python manage.py makemigrations
python manage.py migrate

# Create superuser (admin account)
python manage.py createsuperuser
# Follow prompts to create admin username, email, and password

# Start development server
python manage.py runserver
````

### Environment Configuration

* The server will run at: **[http://127.0.0.1:8000/](http://127.0.0.1:8000/)**
* Uses **SQLite** by default, no extra configuration needed

### Verification

Check server status:

```bash
curl http://127.0.0.1:8000/api/products/
```

Should return a JSON response (data or auth error).

---

## 2.  Basic Usage Guide

### Getting Started

1. **Register a customer account** via registration endpoint
2. **Login** to get JWT tokens
3. **Use the access token** in `Authorization` header for API requests
4. **Admins** can manage products & users, **customers** can place orders

### Authentication Flow

* Register → Login → Get JWT → Use `Authorization: Bearer <token>`
* Access token expires after **60 minutes**
* Use refresh token to generate a new access token

### Role-Based Access

* **Admins**: manage all products, users, and orders
* **Customers**: register, login, create/view their orders

### Example 

```bash
# Register
 POST http://127.0.0.1:8000/api/auth/register/ \
 "Content-Type: application/json" \
'{"username":"testuser","email":"test@example.com","password":"password123","password2":"password123","first_name":"Test","last_name":"User"}'

# Login
POST http://127.0.0.1:8000/api/auth/login/ \
"Content-Type: application/json" \
'{"username":"testuser","password":"password123"}'

# Use token
 GET http://127.0.0.1:8000/api/orders/ \
 "Authorization: Bearer <your-access-token>"
```

---

## 3. API Reference

### Authentication

#### Register Customer

* **POST** `/api/auth/register/` (Public)

```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "password123",
  "password2": "password123",
  "first_name": "John",
  "last_name": "Doe"
}
```

#### Login

* **POST** `/api/auth/login/` (Public)

```json
{
  "username": "admin",
  "password": "adminpassword"
}
```

#### Refresh Token

* **POST** `/api/auth/token/refresh/` (Authenticated)

```json
{
  "refresh": "<refresh-token>"
}
```

---

### Products

#### List Products

* **GET** `/api/products/` (Public Read, Admin Create)

```json
[
  {
    "id": 1,
    "name": "MacBook Pro",
    "description": "16-inch MacBook Pro",
    "price": "2499.99",
    "stock": 25,
    "created_at": "2023-12-15T10:30:00Z"
  }
]
```

#### Create Product (Admin Only)

* **POST** `/api/products/`

```json
{
  "name": "iPhone 15 Pro",
  "description": "Latest iPhone with A17 Pro chip",
  "price": "999.99",
  "stock": 50
}
```

---

###  Orders

#### Create Order

* **POST** `/api/orders/` (Customer only)

```json
{
  "items": [
    { "product_id": 1, "quantity": 2 },
    { "product_id": 2, "quantity": 1 }
  ]
}
```

#### List Orders

* **GET** `/api/orders/`
* Customer → Own orders
* Admin → All orders

```json
[
  {
    "id": 1,
    "customer": { "username": "john_doe", "email": "john@example.com" },
    "total_amount": "5999.97",
    "created_at": "2023-12-15T10:40:00Z"
  }
]
```

---

###  Admin Endpoints

#### List All Users

* **GET** `/api/admin/users/` (Admin only)

```json
[
  {
    "id": 1,
    "username": "admin",
    "email": "admin@example.com",
    "first_name": "Admin",
    "last_name": "User",
    "role": "admin"
  },
  {
    "id": 2,
    "username": "john_doe",
    "email": "john@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "role": "customer"
  }
]
```

#### Update Order

* **PUT** `/api/admin/orders/<id>/update/` (Admin only)

```json
{
  "items": [{ "product_id": 1, "quantity": 3 }]
}
```




