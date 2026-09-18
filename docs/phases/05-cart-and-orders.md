# Phase 5 — Cart and Orders

## Goal

Logged-in users can add products to a cart, check out to create an order, and view their order history. The app runs on PostgreSQL with Flyway migrations.

## You'll learn

- `@OneToMany` / `@ManyToOne`, `mappedBy`, cascade, orphan removal
- `@Transactional`: all-or-nothing operations
- The N+1 query problem and `JOIN FETCH` / `@EntityGraph`
- Concurrency: two people buying the last item (optimistic locking with `@Version`)
- Docker Compose, Spring profiles, and database migrations

## Steps

### Switch to PostgreSQL
- [ ] Install Docker Desktop.
- [ ] Create `docker-compose.yml` at the repo root:
  ```yaml
  services:
    db:
      image: postgres:17
      environment:
        POSTGRES_DB: ecom
        POSTGRES_USER: ecom
        POSTGRES_PASSWORD: ecom
      ports:
        - "5432:5432"
      volumes:
        - pgdata:/var/lib/postgresql/data
  volumes:
    pgdata:
  ```
- [ ] Add the `org.postgresql:postgresql` dependency (runtime scope).
- [ ] Create `application-dev.properties` with the Postgres URL/user/password; run with `SPRING_PROFILES_ACTIVE=dev`.
- [ ] Add Flyway (`spring-boot-starter-flyway`, plus `flyway-database-postgresql`). Write `db/migration/V1__init.sql` with your current tables.
- [ ] Set `spring.jpa.hibernate.ddl-auto=validate`. From now on, schema changes go in new migration files (`V2__...sql`), never by editing old ones.

### Cart
- [ ] Entities: `Cart` (`@OneToOne User`, `@OneToMany(mappedBy = "cart", cascade = ALL, orphanRemoval = true) List<CartItem> items`) and `CartItem` (`@ManyToOne Cart`, `@ManyToOne Product`, `quantity`).
- [ ] `CartService`: get-or-create cart for current user, add item (increase quantity if already there), update quantity, remove item, compute total.
- [ ] `CartController`: `GET /api/cart`, `POST /api/cart/items`, `PUT /api/cart/items/{id}`, `DELETE /api/cart/items/{id}`.
- [ ] Get the current user from the security context (`@AuthenticationPrincipal` or `Authentication`), never from a request param.

### Orders
- [ ] Entities: `Order` (table `orders`; `user`, `status` enum, `total`, `createdAt`, items) and `OrderItem` (`product`, `quantity`, `priceAtPurchase`).
- [ ] `OrderService.checkout()` as one `@Transactional` method:
  1. Load the cart; fail if empty.
  2. For each item: check stock, throw if not enough.
  3. Decrement stock.
  4. Create `Order` + `OrderItem`s, copying the current price.
  5. Clear the cart.
- [ ] Add `@Version private Long version;` to `Product` and handle `OptimisticLockingFailureException` → 409 Conflict.
- [ ] `GET /api/orders` (my orders, newest first) and `GET /api/orders/{id}` (only if it's mine → otherwise 404).
- [ ] Turn on SQL logging, load the order list, and count the queries. Fix N+1 with `@EntityGraph` or a `JOIN FETCH` query.

### Frontend
- [ ] `CartService` holding cart state in a signal, with a `count` computed signal.
- [ ] "Add to cart" button on list and detail pages (redirects to login if logged out).
- [ ] Cart badge in the navbar.
- [ ] Cart page: change quantities, remove items, total, Checkout button.
- [ ] Orders page and order detail page.

- [ ] Commit: `Phase 5: cart, checkout, orders, Postgres + Flyway`

## Done when

- Full flow works: browse → add to cart → checkout → see the order → stock went down.
- Checking out more than the available stock fails and nothing changes in the database.
- Data survives restarting the app (it's in Postgres now).

## Check yourself

- If step 4 of checkout throws, what happens to the stock decrement in step 3? Why?
- Why does `@Transactional` not work when you call the method from inside the same class?
- What is N+1, and how did you spot it?
- Why copy the price into `OrderItem` instead of referencing `Product.price`?
- Why must you never edit a Flyway migration that has already run?

## Notes

-
