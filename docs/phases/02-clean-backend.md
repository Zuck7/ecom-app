# Phase 2 — Clean Backend

## Goal

The API has a proper contract (DTOs), rejects bad input, returns clean error JSON, and supports pagination, sorting, and categories.

## You'll learn

- Why you don't return entities from controllers
- Java `record`s as DTOs
- Bean Validation (`@NotBlank`, `@Positive`, `@Valid`)
- Global exception handling with `@RestControllerAdvice` and `ProblemDetail`
- `Pageable` and `Page<T>`
- Your first relationship: `@ManyToOne`

## Steps

### DTOs
- [ ] Create `product/dto/ProductRequest`:
  ```java
  public record ProductRequest(
      @NotBlank @Size(max = 200) String name,
      @Size(max = 2000) String description,
      @NotNull @Positive BigDecimal price,
      @NotNull @Min(0) Integer stock,
      String imageUrl,
      Long categoryId
  ) {}
  ```
- [ ] Create `product/dto/ProductResponse` (a record with the fields the client should see).
- [ ] Add a mapper: a small `ProductMapper` class with `toResponse(Product)` and `toEntity(ProductRequest)`. (Later, you can try MapStruct.)
- [ ] Change the controller and service to accept `ProductRequest` and return `ProductResponse`.
- [ ] Add `@Valid` before `@RequestBody`.

### Error handling
- [ ] Create `common/GlobalExceptionHandler`:
  ```java
  @RestControllerAdvice
  public class GlobalExceptionHandler {

      @ExceptionHandler(ResourceNotFoundException.class)
      public ProblemDetail notFound(ResourceNotFoundException ex) {
          return ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
      }

      @ExceptionHandler(MethodArgumentNotValidException.class)
      public ProblemDetail invalid(MethodArgumentNotValidException ex) {
          ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.BAD_REQUEST, "Validation failed");
          Map<String, String> errors = new HashMap<>();
          ex.getBindingResult().getFieldErrors()
            .forEach(e -> errors.put(e.getField(), e.getDefaultMessage()));
          pd.setProperty("errors", errors);
          return pd;
      }
  }
  ```

### Pagination and search
- [ ] Change list endpoint to `Page<ProductResponse> all(Pageable pageable)`.
- [ ] Test `GET /api/products?page=0&size=5&sort=price,desc`.
- [ ] Add search: `findByNameContainingIgnoreCase(String q, Pageable p)` in the repository and a `q` request param.

### Categories
- [ ] Create `category/Category` entity (`id`, `name`), repository, and `GET /api/categories`.
- [ ] Add `@ManyToOne private Category category;` to `Product`.
- [ ] Add filtering by category: `GET /api/products?category=1`.

### Seed data
- [ ] Create a `CommandLineRunner` bean (e.g. `common/DataSeeder`) that inserts ~5 categories and ~20 products if the table is empty.

- [ ] Commit: `Phase 2: DTOs, validation, errors, pagination, categories`

## Done when

- Posting `{ "name": "", "price": -1 }` returns 400 with field errors.
- `GET /api/products/999` returns 404 as JSON.
- Pagination and sort work from query params.
- On startup, the database has sample data.

## Check yourself

- Name two problems with returning JPA entities directly as JSON.
- What's the difference between `@Controller` and `@RestController`? `@ControllerAdvice` and `@RestControllerAdvice`?
- What does the JSON of a `Page<T>` look like?
- `@ManyToOne` is `EAGER` by default. What does that mean, and why do many people set it to `LAZY`?

## Notes

-
