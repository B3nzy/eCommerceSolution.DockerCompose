# eCommerceSolution.DockerCompose

This repository manages the multi-container orchestration, persistent infrastructure volumes, and network security boundaries for the entire `eCommerceSolution` microservices ecosystem.

## 🏗️ Architecture & Network Boundaries

The system is divided into strict multi-network layer rings to protect the downstream business logic from external vectors:

- **`gateway-network` (Public Boundary):** Only the Ocelot API Gateway lives on this network, listening for incoming public traffic on host port `8000`.
- **`inter-service-network` (Internal Routing Ring):** A private network connecting Ocelot directly to the internal microservices container endpoints on port `8080`.
- **`user-db-network` / `product-db-network` / `order-db-network`:** Completely isolated, individual backend database networks.

---

## 🛠️ Orchestrated Stack

| Service Name         | Container Name                    | Technology / Image                           | External Port | Internal Network           |
| :------------------- | :-------------------------------- | :------------------------------------------- | :------------ | :------------------------- |
| **API Gateway**      | `ocelot-api-gateway`              | Ocelot (.NET 10)                             | `8000`        | Gateway & Inter-Service    |
| **Users Service**    | `ecommerce-users-microservice`    | ASP.NET Core 8                               | _Hidden_      | User DB & Inter-Service    |
| **Products Service** | `ecommerce-products-microservice` | ASP.NET Core 8                               | _Hidden_      | Product DB & Inter-Service |
| **Orders Service**   | `ecommerce-orders-microservice`   | ASP.NET Core 10                              | _Hidden_      | Order DB & Inter-Service   |
| **User Database**    | `postgres-user-db`                | `postgres:latest`                            | `5433`        | User DB Only               |
| **Product Database** | `mssql-product-db`                | `mcr.microsoft.com/mssql/server:2025-latest` | `1433`        | Product DB Only            |
| **Order Database**   | `mongo-order-db`                  | `mongo:latest`                               | `27017`       | Order DB Only              |
| **Caching Layer**    | `eCommerce-redis-cache`           | `redis:latest`                               | `6379`        | Inter-Service Only         |

---

## 🚀 How to Manage the Ecosystem

### Prerequisites

- Ensure you have [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running.
- Ensure ports `8000`, `5433`, `1433`, `27017`, and `6379` are free on your host machine.

### 1. Spin Up the Environment (Detached Mode)

To build the required images and run all service dependencies concurrently in the background, navigate to the root directory of this repository and execute:

```bash
docker-compose up --build -d
```

### 2. View combined logs for all services inside the stack

```bash
docker-compose logs -f
```

### 3. Spin Down the Environment in Detached Mode

To stop all running images and their dependencies in the background, use this command

```bash
docker-compose down
```
