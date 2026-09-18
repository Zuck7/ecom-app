# Phase 0 — Setup

## Goal

The Spring Boot app builds, starts, and connects to an in-memory H2 database.

## You'll learn

- What Maven and `pom.xml` do (dependencies, plugins, build lifecycle)
- What a Spring Boot "starter" is
- How `application.properties` configures the app
- Why the Java version in `pom.xml` must match your installed JDK

## Steps

- [ ] **Fix the Java version.** `pom.xml` says `<java.version>26</java.version>`, but JDK 24 is installed (`java -version`).
  - Recommended: install JDK 25 (LTS), e.g. `brew install openjdk@25` or via [SDKMAN](https://sdkman.io), then set `<java.version>25</java.version>`.
  - Quick option: set `<java.version>24</java.version>`.
- [ ] Run `./mvnw spring-boot:run` and confirm you see `Started EcomApplication` in the log. Stop it with `Ctrl+C`.
- [ ] Add dependencies to `pom.xml` inside `<dependencies>`:
  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
  </dependency>
  <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
      <scope>runtime</scope>
  </dependency>
  ```
- [ ] Update `src/main/resources/application.properties`:
  ```properties
  spring.application.name=ecom-application
  spring.datasource.url=jdbc:h2:mem:ecom
  spring.jpa.hibernate.ddl-auto=update
  spring.jpa.show-sql=true
  spring.h2.console.enabled=true
  ```
- [ ] Run the app again and open `http://localhost:8080/h2-console` (JDBC URL: `jdbc:h2:mem:ecom`, user `sa`, empty password).
- [ ] Install a way to call APIs: Postman, Bruno, or the VS Code "REST Client" extension (`.http` files).
- [ ] Commit: `git commit -am "Phase 0: setup JPA + H2"`

## Done when

- `./mvnw spring-boot:run` starts without errors.
- The H2 console opens and connects.

## Check yourself

- What's the difference between a dependency with `<scope>runtime</scope>` and one without a scope?
- What does `spring.jpa.hibernate.ddl-auto=update` do? Why is it dangerous in production?
- What does `@SpringBootApplication` do? (Hint: it combines three annotations.)

## Notes

-
