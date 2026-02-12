# Microservices Architecture - Golang & React

A simple full-stack microservices architecture demonstrating inter-service communication using Golang backend services and a React frontend. This project consists of three services: **User Service**, **Order Service**, and a **React Frontend**.

## Architecture Overview

```
┌──────────────────┐
│  React Frontend  │
│   (Port 3000)    │
└────────┬─────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌─────────────────┐         ┌──────────────────┐
│  User Service   │ ◄───────┤  Order Service   │
│    (Port 8080)  │         │    (Port 8081)   │
└─────────────────┘         └──────────────────┘
```

- **Frontend**: React application for managing users and orders
- **User Service**: Manages user data (CRUD operations)
- **Order Service**: Manages orders and validates users by calling User Service

## Features

- ✅ RESTful APIs with JSON responses
- ✅ Inter-service HTTP communication
- ✅ React frontend with API integration
- ✅ In-memory data storage
- ✅ Health check endpoints
- ✅ Docker support with multi-stage builds
- ✅ Docker Compose orchestration

## Prerequisites

- Go 1.21 or higher
- Node.js 18 or higher
- Docker and Docker Compose (optional, for containerized deployment)

## Project Structure

```
microservice_ci_cd_proj/
├── user-service/
│   ├── main.go
│   ├── go.mod
│   ├── go.sum
│   └── Dockerfile
├── order-service/
│   ├── main.go
│   ├── go.mod
│   ├── go.sum
│   └── Dockerfile
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Users.js
│   │   │   ├── Users.css
│   │   │   ├── Orders.js
│   │   │   └── Orders.css
│   │   ├── App.js
│   │   ├── App.css
│   │   ├── api.js
│   │   └── index.js
│   ├── package.json
│   ├── Dockerfile
│   └── nginx.conf
├── docker-compose.yml
└── README.md
```

## Running Locally (Without Docker)

### 1. Start User Service

```bash
cd user-service
go mod download
go run main.go
```

User Service will start on `http://localhost:8080`

### 2. Start Order Service

Open a new terminal:

```bash
cd order-service
go mod download
go run main.go
```

Order Service will start on `http://localhost:8081`

### 3. Start Frontend

Open a new terminal:

```bash
cd frontend
npm install
npm start
```

Frontend will start on `http://localhost:3000`

## Running with Docker Compose (Recommended)

Build and start all services:

```bash
docker-compose up --build
```

Access the application:
- **Frontend**: http://localhost:3000
- **User Service**: http://localhost:8080
- **Order Service**: http://localhost:8081

Stop services:

```bash
docker-compose down
```

## Frontend Features

### Users Tab
- 👥 View all users in a card-based grid layout
- ➕ Create new users with ID, name, and email
- 🗑️ Delete users
- 🔄 Real-time updates after operations

### Orders Tab
- 📦 View all orders with status indicators
- ➕ Create new orders (automatically validates user exists)
- 👁️ View detailed order information with customer details
- 🎨 Color-coded order status (pending, shipped, delivered)
- 🔗 Demonstrates inter-service communication

The frontend showcases how the Order Service communicates with the User Service to validate users and fetch user details.

## API Endpoints

### User Service (Port 8080)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Health check |
| GET | `/users` | Get all users |
| GET | `/users/{id}` | Get user by ID |
| POST | `/users` | Create new user |
| PUT | `/users/{id}` | Update user |
| DELETE | `/users/{id}` | Delete user |

### Order Service (Port 8081)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Health check |
| GET | `/orders` | Get all orders |
| GET | `/orders?user_id={id}` | Get orders by user ID |
| GET | `/orders/{id}` | Get order by ID |
| GET | `/orders/{id}/details` | Get order with user details |
| POST | `/orders` | Create new order (validates user) |

## Example API Calls

### Get All Users

```bash
curl http://localhost:8080/users
```

### Get Specific User

```bash
curl http://localhost:8080/users/1
```

### Create New User

```bash
curl -X POST http://localhost:8080/users \
  -H "Content-Type: application/json" \
  -d '{
    "id": "3",
    "name": "Alice Johnson",
    "email": "alice@example.com"
  }'
```

### Create New Order

```bash
curl -X POST http://localhost:8081/orders \
  -H "Content-Type: application/json" \
  -d '{
    "id": "3",
    "user_id": "1",
    "item": "Tablet",
    "amount": 299.99
  }'
```

Note: Order creation will fail if the user doesn't exist (demonstrates inter-service validation).

### Get Order with User Details

```bash
curl http://localhost:8081/orders/1/details
```

This endpoint demonstrates inter-service communication by fetching order data and enriching it with user details from User Service.

### Get Orders by User ID

```bash
curl http://localhost:8081/orders?user_id=1
```

## Testing Inter-Service Communication

1. Create a user:
```bash
curl -X POST http://localhost:8080/users \
  -H "Content-Type: application/json" \
  -d '{"id": "100", "name": "Test User", "email": "test@example.com"}'
```

2. Create an order for that user:
```bash
curl -X POST http://localhost:8081/orders \
  -H "Content-Type: application/json" \
  -d '{"id": "100", "user_id": "100", "item": "Test Item", "amount": 50.00}'
```

3. Try to create an order for a non-existent user (will fail):
```bash
curl -X POST http://localhost:8081/orders \
  -H "Content-Type: application/json" \
  -d '{"id": "101", "user_id": "999", "item": "Test Item", "amount": 50.00}'
```

## Health Checks

Check service health:

```bash
curl http://localhost:8080/health
curl http://localhost:8081/health
```

## Technologies Used

- **Backend Language**: Go 1.21
- **Frontend Framework**: React 18
- **Router**: Gorilla Mux
- **Styling**: Pure CSS
- **Containerization**: Docker
- **Orchestration**: Docker Compose
- **Web Server**: Nginx (for production frontend)

## Future Enhancements

- Add database persistence (PostgreSQL/MongoDB)
- Implement API Gateway
- Add authentication and authorization (JWT)
- Implement message queuing (RabbitMQ/Kafka)
- Add logging and monitoring (Prometheus, Grafana)
- Implement circuit breakers and retry logic
- Add unit and integration tests
- Set up CI/CD pipelines
- Add pagination for lists
- Implement WebSocket for real-time updates
- Add search and filter functionality
