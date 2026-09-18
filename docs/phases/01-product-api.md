# Phase 1 — Product API

## Goal

A working REST API to create, read, update, and delete products.

## You'll learn

- REST basics: resources, HTTP methods, status codes
- `@Entity`, `@Id`, `@GeneratedValue`: mapping a class to a table
- `JpaRepository`: what you get for free (`findAll`, `findById`, `save`, `deleteById`)
- Dependency injection via constructors (`@RequiredArgsConstructor`)
- Controller → Service → Repository layering

## Steps

- [ ] Create package `com.app.ecom.product`.
- [ ] Create `Product` entity:
  ```java
  @Entity
  @Getter @Setter @NoArgsConstructor
  public class Product {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Long id;
      private String name;
      @Column(length = 2000)
      private String description;
      private BigDecimal price;   // never double for money
      private Integer stock;
      private String imageUrl;
  }
  ```
- [ ] Create `ProductRepository`:
  ```java
  public interface ProductRepository extends JpaRepository<Product, Long> {}
  ```
- [ ] Create `com.app.ecom.common.ResourceNotFoundException extends RuntimeException`.
- [ ] Create `ProductService`:
  ```java
  @Service
  @RequiredArgsConstructor
  public class ProductService {
      private final ProductRepository repo;

      public List<Product> findAll() { return repo.findAll(); }

      public Product findById(Long id) {
          return repo.findById(id)
                  .orElseThrow(() -> new ResourceNotFoundException("Product " + id + " not found"));
      }

      public Product create(Product p) { return repo.save(p); }

      public Product update(Long id, Product changes) {
          Product existing = findById(id);
          existing.setName(changes.getName());
          existing.setDescription(changes.getDescription());
          existing.setPrice(changes.getPrice());
          existing.setStock(changes.getStock());
          existing.setImageUrl(changes.getImageUrl());
          return repo.save(existing);
      }

      public void delete(Long id) { repo.delete(findById(id)); }
  }
  ```
- [ ] Create `ProductController`:
  ```java
  @RestController
  @RequestMapping("/api/products")
  @RequiredArgsConstructor
  public class ProductController {
      private final ProductService service;

      @GetMapping
      public List<Product> all() { return service.findAll(); }

      @GetMapping("/{id}")
      public Product one(@PathVariable Long id) { return service.findById(id); }

      @PostMapping
      @ResponseStatus(HttpStatus.CREATED)
      public Product create(@RequestBody Product p) { return service.create(p); }

      @PutMapping("/{id}")
      public Product update(@PathVariable Long id, @RequestBody Product p) { return service.update(id, p); }

      @DeleteMapping("/{id}")
      @ResponseStatus(HttpStatus.NO_CONTENT)
      public void delete(@PathVariable Long id) { service.delete(id); }
  }
  ```
- [ ] Create `requests.http` at the repo root (VS Code REST Client) and test every endpoint:
  ```http
  ### Create
  POST http://localhost:8080/api/products
  Content-Type: application/json

  { "name": "Keyboard", "description": "Mechanical", "price": 79.99, "stock": 10 }

  ### List
  GET http://localhost:8080/api/products

  ### One
  GET http://localhost:8080/api/products/1
  ```
- [ ] Look at the SQL in the console log (from `show-sql=true`) for each call.
- [ ] Commit: `Phase 1: product CRUD API`

## Done when

- You can create, list, get, update, and delete products via HTTP.
- You can see the rows in the H2 console.

## Check yourself

- Why does `POST` return 201 and `DELETE` return 204?
- What happens right now if you `GET /api/products/999`? (You'll fix it in Phase 2.)
- Why is the service a separate class instead of putting the logic in the controller?
- Where did the SQL table come from? You never wrote `CREATE TABLE`.

## Notes

-
