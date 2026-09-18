# Architecture

## Big picture

```
┌──────────────────┐   HTTP/JSON (REST)   ┌──────────────────────────────┐     ┌────────────┐
│  Angular (SPA)   │ ───────────────────▶ │ Spring Boot  :8080           │     │ PostgreSQL │
│  localhost:4200  │ ◀─────────────────── │  Controller → Service → Repo │ ──▶ │  (or H2)   │
│  components,     │   JWT in header      │  Security (JWT filter)       │     └────────────┘
│  services, routes│                      │  DTOs, validation, errors    │
└──────────────────┘                      └──────────────────────────────┘
```

### What happens on a request (e.g. `GET /api/products/5`)

1. Angular's `ProductService` calls `http.get('/api/products/5')`.
2. In dev, the Angular proxy forwards `/api/*` to `localhost:8080`.
3. The Spring Security filter chain checks the request (public or needs a JWT?).
4. `ProductController.one(5)` is called; it delegates to `ProductService`.
5. `ProductService` calls `ProductRepository.findById(5)` → Hibernate runs SQL.
6. The entity is mapped to a `ProductResponse` DTO and serialized to JSON.
7. Angular receives the JSON, updates a signal, and the template re-renders.

## Backend layers

| Layer | Responsibility | Should NOT |
|---|---|---|
| Controller | HTTP: paths, status codes, request/response DTOs, `@Valid` | Contain business logic or talk to repositories |
| Service | Business rules, transactions | Know about HTTP |
| Repository | Database access | Contain business rules |
| Entity | Maps to a table | Be returned directly from controllers |
| DTO | API contract (records) | Contain JPA annotations |

## Data model (target by Phase 5)

```
User(id, email, passwordHash, role)          1 ── * Order(id, user, status, total, createdAt)
Category(id, name)  1 ── * Product(id, name, description, price, stock, imageUrl, category)
Cart(id, user)  1 ── * CartItem(id, product, quantity)
Order 1 ── * OrderItem(id, product, quantity, priceAtPurchase)
```

- Money is always `BigDecimal`, never `double`.
- `priceAtPurchase` keeps old orders correct when product prices change.
- Order `status`: `PENDING → PAID → SHIPPED → DELIVERED` (or `CANCELLED`).

## API endpoints (target)

| Method | Path | Access | Phase |
|---|---|---|---|
| GET | `/api/products?page=&size=&sort=&category=&q=` | public | 1–2 |
| GET | `/api/products/{id}` | public | 1 |
| POST | `/api/products` | ADMIN | 1 → 6 |
| PUT / DELETE | `/api/products/{id}` | ADMIN | 1 → 6 |
| GET | `/api/categories` | public | 2 |
| POST | `/api/auth/register` | public | 4 |
| POST | `/api/auth/login` | public | 4 |
| GET | `/api/users/me` | USER | 4 |
| GET | `/api/cart` | USER | 5 |
| POST | `/api/cart/items` | USER | 5 |
| PUT / DELETE | `/api/cart/items/{id}` | USER | 5 |
| POST | `/api/orders` (checkout) | USER | 5 |
| GET | `/api/orders`, `/api/orders/{id}` | USER | 5 |
| POST | `/api/products/{id}/image` | ADMIN | 6 |
| POST | `/api/payments/checkout-session` | USER | 7 |
| POST | `/api/payments/webhook` | Stripe | 7 |

## Folder layout (target)

Feature-based packages: everything about one feature lives together.

```
ecom-app/
├── pom.xml
├── src/main/java/com/app/ecom/
│   ├── EcomApplication.java
│   ├── common/        # exceptions, GlobalExceptionHandler, config
│   ├── product/       # Product, ProductRepository, ProductService, ProductController, dto/
│   ├── category/
│   ├── user/
│   ├── auth/          # AuthController, JwtService, SecurityConfig
│   ├── cart/
│   ├── order/
│   └── payment/
├── src/main/resources/
│   ├── application.properties
│   ├── application-dev.properties
│   └── db/migration/  # Flyway: V1__init.sql, V2__...
├── frontend/          # Angular app
│   └── src/app/
│       ├── models/    # TypeScript interfaces matching DTOs
│       ├── services/  # HTTP calls, state
│       ├── pages/     # routed components
│       ├── components/# reusable UI (navbar, product-card)
│       ├── guards/
│       └── interceptors/
├── docker-compose.yml
└── docs/              # you are here
```
