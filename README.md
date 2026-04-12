# E-Shop Microservices — Scalable E-Commerce Platform

[![Nx Monorepo](https://img.shields.io/badge/Nx-Monorepo-blue?logo=nx)](https://nx.dev)
[![Node.js](https://img.shields.io/badge/Node.js-v18%2B-green?logo=node.js)](https://nodejs.org/)
[![Next.js](https://img.shields.io/badge/Next.js-16-black?logo=next.js)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-Strict-blue?logo=typescript)](https://www.typescriptlang.org/)
[![Apache Kafka](https://img.shields.io/badge/Kafka-Event%20Streaming-red?logo=apachekafka)](https://kafka.apache.org/)
[![Prisma](https://img.shields.io/badge/Prisma-ORM-1B222D?logo=prisma)](https://www.prisma.io/)
[![Redis](https://img.shields.io/badge/Redis-Cache-DC382D?logo=redis)](https://redis.io/)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> A production-ready, cloud-native **E-Commerce Microservices Platform** built with Node.js, Next.js 16, Apache Kafka, and PostgreSQL — managed as a unified **Nx Monorepo**. Designed for scale, resilience, and developer velocity.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Running the Application](#running-the-application)
- [API Documentation](#api-documentation)
- [Nx Workspace Commands](#nx-workspace-commands)
- [Environment Variables](#environment-variables)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

**E-Shop Microservices** is a full-stack, event-driven e-commerce platform that separates concerns into discrete, independently deployable backend services. It serves two user groups — **shoppers** via a consumer-facing storefront and **vendors** via a dedicated seller dashboard — both powered by a shared microservices backend.

The platform is built to solve common scaling bottlenecks in monolithic e-commerce systems: tight coupling, single points of failure, and deployment friction. Each service owns its own data, communicates asynchronously over **Apache Kafka**, and is individually scalable.

**Who is this for?**

- Developers building scalable e-commerce backends
- Teams evaluating microservices and event-driven architecture patterns
- Engineers learning Nx monorepo management with multiple full-stack apps
- Projects needing a production-ready Next.js + Node.js + Kafka starting point

---

## Architecture

```
┌─────────────────┐        ┌─────────────────┐
│    User UI       │        │   Seller UI      │
│  (Next.js 16)    │        │  (Next.js 16)    │
└────────┬────────┘        └────────┬────────┘
         │                          │
         └──────────┬───────────────┘
                    ▼
         ┌─────────────────────┐
         │     API Gateway      │
         │  (Express · Swagger) │
         └──────┬──────┬───────┘
                │      │
     ┌──────────┘      └──────────┐
     ▼                            ▼
┌──────────┐             ┌──────────────────┐
│   Auth    │             │ Product Service  │
│  Service  │             │                  │
└────┬──────┘             └────────┬─────────┘
     │                             │
     └──────────┬──────────────────┘
                ▼
     ┌─────────────────────┐
     │    Kafka Service     │
     │ (Producers/Consumers)│
     └──────────┬──────────┘
                ▼
     ┌─────────────────────┐
     │  Apache Kafka Broker │
     └──────────┬──────────┘
                │
     ┌──────────┼──────────┐
     ▼          ▼          ▼
┌──────────┐ ┌──────┐ ┌────────┐
│PostgreSQL│ │Redis │ │Docker  │
│(Prisma)  │ │Cache │ │Compose │
└──────────┘ └──────┘ └────────┘
```

All frontend traffic flows through the **API Gateway**, which routes requests to the appropriate backend service. Inter-service communication happens asynchronously over Kafka, keeping services decoupled and resilient to partial failures.

---

## Features

### Platform Features

- **Dual Storefront** — Separate, optimized interfaces for shoppers and sellers
- **Product Catalog** — Full product management with categories, inventory tracking, and search
- **Authentication & Authorization** — Secure JWT-based auth with refresh token rotation
- **Session Management** — Redis-backed sessions with configurable TTL
- **Real-Time Events** — Asynchronous messaging between services via Kafka
- **API Gateway** — Centralized routing, rate limiting, and request validation
- **Auto-generated API Docs** — Swagger UI for all backend services

### Developer Experience

- **Nx Monorepo** — Shared code, unified tooling, and dependency graph visualization
- **Type Safety** — End-to-end TypeScript with strict mode enabled
- **Code Generation** — Nx generators for scaffolding new services and libraries
- **Docker Compose** — One-command local infrastructure setup (Kafka, Redis, PostgreSQL)
- **Hot Reload** — Fast refresh across all services during development

### Architecture Highlights

- **Event-Driven Design** — Kafka decouples services for fault tolerance and scalability
- **Database per Service** — Each service owns its schema, preventing tight data coupling
- **Reverse Proxy Gateway** — Single entry point simplifies client configuration and security
- **Horizontal Scalability** — Stateless services can scale independently behind the gateway

---

## Tech Stack

| Layer                | Technology                                        |
| -------------------- | ------------------------------------------------- |
| **Frontend**         | React 19, Next.js 16, Tailwind CSS, Framer Motion |
| **State Management** | Zustand, Jotai, React Query                       |
| **UI Primitives**    | Radix UI, React Hook Form, Styled Components      |
| **Backend**          | Node.js, Express.js, TypeScript                   |
| **ORM & Database**   | Prisma ORM, PostgreSQL                            |
| **Messaging**        | Apache Kafka (KafkaJS)                            |
| **Caching**          | Redis (ioredis)                                   |
| **API Docs**         | Swagger AutoGen                                   |
| **Monorepo**         | Nx Workspace                                      |
| **Build Tools**      | Webpack, TypeScript Compiler                      |
| **Containerization** | Docker, Docker Compose                            |

---

## Project Structure

```
eshop_microservice/
├── apps/
│   ├── user-ui/              # Customer-facing storefront (Next.js)
│   └── seller-ui/            # Seller management dashboard (Next.js)
├── services/
│   ├── api-gateway/          # Central reverse proxy and router
│   ├── auth-service/         # User auth, JWT, session management
│   ├── product-service/      # Product catalog, inventory, categories
│   └── kafka-service/        # Kafka producer/consumer configurations
├── libs/                     # Shared libraries (types, utilities, UI components)
├── nx.json                   # Nx workspace configuration
├── package.json
└── docker-compose.yml        # Local infrastructure setup
```

### Apps (Frontends)

| App         | Description                              | Port   |
| ----------- | ---------------------------------------- | ------ |
| `user-ui`   | Primary shopping interface for customers | `3000` |
| `seller-ui` | Store management dashboard for vendors   | `3001` |

### Services (Backends)

| Service           | Description                                                    | Port   |
| ----------------- | -------------------------------------------------------------- | ------ |
| `api-gateway`     | Central router and reverse proxy for all microservices traffic | `4000` |
| `auth-service`    | User registration, login, JWT issuing, and authorization       | `4001` |
| `product-service` | Product catalog, inventory, categories, and search             | `4002` |
| `kafka-service`   | Centralized event producer/consumer and topic management       | `4003` |

---

## Getting Started

### Prerequisites

Ensure the following are installed on your machine:

| Dependency                                         | Version     | Notes                                 |
| -------------------------------------------------- | ----------- | ------------------------------------- |
| [Node.js](https://nodejs.org/)                     | v18 or v20+ | LTS recommended                       |
| [Docker](https://www.docker.com/)                  | Latest      | Required for Kafka, Redis, PostgreSQL |
| [Docker Compose](https://docs.docker.com/compose/) | v2+         | Bundled with Docker Desktop           |
| [Nx CLI](https://nx.dev/)                          | Latest      | `npm i -g nx`                         |

### Installation

**1. Clone the repository**

```bash
git clone https://github.com/your-username/eshop_microservice.git
cd eshop_microservice
```

**2. Install dependencies**

```bash
npm install
```

**3. Start infrastructure services**

Spin up Kafka, Redis, and PostgreSQL using Docker Compose:

```bash
docker-compose up -d
```

**4. Configure environment variables**

Copy the example environment file and fill in your values:

```bash
cp .env.example .env
```

Key variables to configure (see [Environment Variables](#environment-variables) for the full list):

```env
DATABASE_URL=postgresql://user:password@localhost:5432/eshop
REDIS_URL=redis://localhost:6379
KAFKA_BROKERS=localhost:9092
JWT_SECRET=your-secret-key
```

**5. Run database migrations**

```bash
npx prisma migrate dev
```

---

## Running the Application

### Run Everything

Start all services and frontends in parallel:

```bash
npm run dev
```

### Run Individual Services

```bash
# Frontend apps
npm run user-ui         # Customer storefront → http://localhost:3000
npm run seller-ui       # Seller dashboard   → http://localhost:3001

# Backend services (using Nx)
npx nx serve api-gateway       # API Gateway      → http://localhost:4000
npx nx serve auth-service      # Auth Service     → http://localhost:4001
npx nx serve product-service   # Product Service  → http://localhost:4002
npx nx serve kafka-service     # Kafka Service    → http://localhost:4003
```

### Build for Production

```bash
# Build all projects
npx nx run-many --target=build --all

# Build a specific app
npx nx build user-ui
npx nx build auth-service
```

---

## API Documentation

All backend services expose Swagger UI documentation. Generate or view docs with the following commands:

```bash
npm run auth-docs        # Auth Service API docs
npm run product-docs     # Product Service API docs
```

Once running, access Swagger UI at:

- Auth Service: `http://localhost:4001/api-docs`
- Product Service: `http://localhost:4002/api-docs`

### Key Endpoints

#### Auth Service

| Method | Endpoint         | Description                  |
| ------ | ---------------- | ---------------------------- |
| `POST` | `/auth/register` | Register a new user          |
| `POST` | `/auth/login`    | Authenticate and receive JWT |
| `POST` | `/auth/refresh`  | Refresh access token         |
| `POST` | `/auth/logout`   | Invalidate session           |
| `GET`  | `/auth/me`       | Get current user profile     |

#### Product Service

| Method   | Endpoint        | Description                   |
| -------- | --------------- | ----------------------------- |
| `GET`    | `/products`     | List all products (paginated) |
| `GET`    | `/products/:id` | Get a single product          |
| `POST`   | `/products`     | Create a new product (seller) |
| `PUT`    | `/products/:id` | Update product details        |
| `DELETE` | `/products/:id` | Remove a product              |
| `GET`    | `/categories`   | List all categories           |

---

## Nx Workspace Commands

| Command                                | Description                                     |
| -------------------------------------- | ----------------------------------------------- |
| `npx nx graph`                         | Visualize the dependency graph of the workspace |
| `npx nx run-many --target=build --all` | Build all projects                              |
| `npx nx run-many --target=test --all`  | Run all tests                                   |
| `npx nx affected --target=build`       | Build only projects affected by recent changes  |
| `npx nx g @nx/node:app new-service`    | Scaffold a new Node.js microservice             |
| `npx nx g @nx/next:app new-frontend`   | Scaffold a new Next.js application              |
| `npx nx g @nx/js:lib shared-lib`       | Generate a shared library                       |
| `npx nx reset`                         | Clear the Nx cache                              |

---

## Environment Variables

Create a `.env` file at the root or inside each service directory. Below is a reference for all required variables.

### Root / Shared

```env
NODE_ENV=development
```

### API Gateway

```env
PORT=4000
AUTH_SERVICE_URL=http://localhost:4001
PRODUCT_SERVICE_URL=http://localhost:4002
```

### Auth Service

```env
PORT=4001
DATABASE_URL=postgresql://user:password@localhost:5432/auth_db
JWT_SECRET=your-jwt-secret
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_SECRET=your-refresh-secret
REFRESH_TOKEN_EXPIRES_IN=7d
REDIS_URL=redis://localhost:6379
```

### Product Service

```env
PORT=4002
DATABASE_URL=postgresql://user:password@localhost:5432/product_db
KAFKA_BROKERS=localhost:9092
KAFKA_GROUP_ID=product-service-group
```

### Kafka Service

```env
PORT=4003
KAFKA_BROKERS=localhost:9092
KAFKA_CLIENT_ID=eshop-kafka-service
```

---

## Roadmap

The platform is under active development. Upcoming features include:

- **Real-Time Chat** — Buyer-seller messaging with Socket.io and Kafka-backed message persistence
- **ML Recommendation Engine** — Personalized product recommendations using collaborative filtering
- **Order Service** — Full order lifecycle management (placement, tracking, fulfillment, returns)
- **Payment Integration** — Stripe and PayPal payment gateway support
- **Notification Service** — Email and push notification delivery via Kafka consumers
- **Search Service** — Elasticsearch integration for full-text product search and faceted filtering
- **Admin Dashboard** — Platform-wide analytics, user management, and moderation tools
- **CI/CD Pipeline** — GitHub Actions workflows for automated testing and deployment
- **Kubernetes Support** — Helm charts for production deployment on Kubernetes clusters

---

## Contributing

Contributions are welcome! Please read these guidelines before submitting a PR.

### Development Workflow

1. **Fork** the repository and clone it locally
2. **Create a feature branch** from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes** and ensure all tests pass:
   ```bash
   npx nx run-many --target=test --all
   ```
4. **Lint your code**:
   ```bash
   npx nx run-many --target=lint --all
   ```
5. **Commit** using a descriptive message:
   ```bash
   git commit -m "feat(auth): add OAuth2 Google provider"
   ```
6. **Push** your branch and open a Pull Request against `main`

### Commit Convention

This project follows [Conventional Commits](https://www.conventionalcommits.org/):

| Prefix      | Use for                                    |
| ----------- | ------------------------------------------ |
| `feat:`     | New features                               |
| `fix:`      | Bug fixes                                  |
| `docs:`     | Documentation updates                      |
| `refactor:` | Code restructuring without behavior change |
| `test:`     | Adding or updating tests                   |
| `chore:`    | Tooling, config, dependency updates        |

### Reporting Issues

Found a bug or have a feature request? [Open an issue](https://github.com/your-username/eshop_microservice/issues) with:

- A clear description of the problem or request
- Steps to reproduce (for bugs)
- Expected vs actual behavior
- Relevant logs or screenshots

---

## License

Distributed under the **MIT License**. See [`LICENSE`](./LICENSE) for full terms.

---

## Acknowledgements

Built with the following open-source projects:

- [Nx](https://nx.dev) — Monorepo tooling and build system
- [Next.js](https://nextjs.org) — React framework for production
- [Express.js](https://expressjs.com) — Minimal Node.js web framework
- [Apache Kafka](https://kafka.apache.org) — Distributed event streaming platform
- [Prisma](https://www.prisma.io) — Next-generation Node.js ORM
- [Redis](https://redis.io) — In-memory data structure store
- [KafkaJS](https://kafka.js.org) — Kafka client for Node.js
- [Tailwind CSS](https://tailwindcss.com) — Utility-first CSS framework
- [Radix UI](https://www.radix-ui.com) — Accessible, unstyled UI primitives

---

<p align="center">
  Made with ❤️ — Contributions, stars ⭐, and feedback welcome!
</p>
