# Phase 8 — Testing

## Goal

A test suite that gives you confidence to change code: unit tests for business logic, slice tests for web and data layers, integration tests against real Postgres, and frontend tests.

> Tip: you don't have to wait until Phase 8. Writing a few tests in each phase is better. This phase is for filling gaps and learning the tools properly.

## You'll learn

- The testing pyramid: many unit tests, fewer integration tests, few end-to-end tests
- JUnit 5, AssertJ, Mockito
- Spring test slices: `@WebMvcTest`, `@DataJpaTest`
- Testcontainers: a real Postgres in Docker for tests
- Angular unit tests and an end-to-end tool (Playwright or Cypress)

## Steps

### Backend
- [ ] Unit test `OrderService.checkout()` with Mockito (mock repositories):
  - empty cart → throws
  - not enough stock → throws, nothing saved
  - success → stock decremented, order total correct, cart cleared
- [ ] `@WebMvcTest(ProductController.class)` with `MockMvc`: 200 for list, 404 for missing, 400 for invalid body. Mock the service with `@MockitoBean`.
- [ ] Security tests: 401 without token, 403 for USER on admin endpoints (`@WithMockUser(roles = "ADMIN")`).
- [ ] Add Testcontainers (`spring-boot-testcontainers`, `org.testcontainers:postgresql`) and a `@DataJpaTest` for custom repository queries using `@ServiceConnection`.
- [ ] One full `@SpringBootTest` integration test: register → login → add to cart → checkout.
- [ ] Run everything: `./mvnw verify`.
- [ ] Optional: add JaCoCo for a coverage report.

### Frontend
- [ ] Unit tests for `AuthService` and `CartService` (use `provideHttpClientTesting()` / `HttpTestingController`).
- [ ] Component test for `ProductCardComponent`.
- [ ] Guard test: logged-out user is redirected.
- [ ] E2E with Playwright: browse → login → add to cart → checkout.

- [ ] Commit: `Phase 8: test suite`

## Done when

- `./mvnw verify` and `ng test` both pass.
- Breaking the stock check on purpose makes a test fail.

## Check yourself

- What's the difference between a mock and a stub?
- Why use Testcontainers instead of H2 for repository tests?
- What does `@WebMvcTest` load, and what does it not load?
- Which of your tests would catch a broken security rule?

## Notes

-
