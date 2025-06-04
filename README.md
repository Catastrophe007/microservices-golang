
---

# 🧱 Microservices E-Commerce Project (Go + gRPC + GraphQL)

This project showcases a microservices-based e-commerce backend built using **Go**, **gRPC**, **GraphQL**, **PostgreSQL**, **Elasticsearch**, and **Docker Compose**.

It includes distinct services for account management, product catalog, and order processing, all tied together with a GraphQL API Gateway.

---

## 📁 Project Structure

```
.
├── account/               # Account service (PostgreSQL)
├── catalog/               # Catalog service (Elasticsearch)
├── graphql/               # GraphQL API Gateway
├── order/                 # Order service (PostgreSQL)
├── .gitignore             # Git ignore file
├── docker-compose.yaml    # Docker Compose config to run all services
├── go.mod                 # Go module definition
├── go.sum                 # Go module checksums
```

Each folder corresponds to a self-contained service or config:

* `account/`, `catalog/`, `order/` — Independent Go microservices with gRPC
* `graphql/` — The GraphQL gateway that talks to the services via gRPC
* `docker-compose.yaml` — Spins up everything in containers
* `go.mod` and `go.sum` — Manage Go dependencies for the root module

## 💾 Tech Stack

* **Go**
* **gRPC** – Internal service communication
* **GraphQL** – Unified external API
* **PostgreSQL** – Used by Account & Order services
* **Elasticsearch** – Used by Catalog service
* **Docker Compose** – Service orchestration

---

## 🛠️ Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <project-directory>
```

### 2. Start All Services

```bash
docker-compose up -d --build
```

GraphQL Playground: [http://localhost:8000/playground](http://localhost:8000/playground)

---

## 🔧 gRPC Proto File Setup

### One-time Installation

```bash
# Install protoc
wget https://github.com/protocolbuffers/protobuf/releases/download/v23.0/protoc-23.0-linux-x86_64.zip
unzip protoc-23.0-linux-x86_64.zip -d protoc
sudo mv protoc/bin/protoc /usr/local/bin/

# Install Go plugins
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest

# Set PATH
export PATH="$PATH:$(go env GOPATH)/bin"
source ~/.bashrc
```

### Generate gRPC Code

```bash
mkdir pb
# Add to .proto file: option go_package = "./pb";
protoc --go_out=./pb --go-grpc_out=./pb proto/account.proto
```

Repeat for other proto files as needed.

---

## 📡 GraphQL API Reference

GraphQL Playground available at: [http://localhost:8000/playground](http://localhost:8000/playground)

### 🔍 Query Accounts

```graphql
query {
  accounts {
    id
    name
  }
}
```

### ✍️ Create an Account

```graphql
mutation {
  createAccount(account: { name: "Alice" }) {
    id
    name
  }
}
```

### 🔍 List Products

```graphql
query {
  products {
    id
    name
    price
  }
}
```

### ✍️ Create Product

```graphql
mutation {
  createProduct(product: {
    name: "Keyboard",
    description: "Mechanical switch keyboard",
    price: 49.99
  }) {
    id
    name
    price
  }
}
```

### 📦 Create Order

```graphql
mutation {
  createOrder(order: {
    accountId: "account_id",
    products: [{ id: "product_id", quantity: 2 }]
  }) {
    id
    totalPrice
    products {
      name
      quantity
    }
  }
}
```

---

## 📊 Advanced GraphQL Queries

### Product Filtering + Pagination

```graphql
query {
  products(pagination: { skip: 0, take: 5 }, query: "keyboard") {
    id
    name
    price
    description
  }
}
```

### Account Orders with Product Info

```graphql
query {
  accounts(id: "account_id") {
    name
    orders {
      id
      createdAt
      totalPrice
      products {
        name
        quantity
        price
      }
    }
  }
}
```

### Total Spend per Account

```graphql
query {
  accounts(id: "account_id") {
    name
    orders {
      totalPrice
    }
  }
}
```

---
