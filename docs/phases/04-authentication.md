# Phase 4 — Authentication

## Goal

Users can register and log in. The API issues a JWT, protects private endpoints, and restricts admin endpoints. Angular has login/register pages, attaches the token automatically, and guards private routes.

## You'll learn

- Authentication (who are you?) vs authorization (what can you do?)
- Password hashing with BCrypt
- JWT: header, payload, signature; why it's stateless
- Spring Security's filter chain and `SecurityFilterChain` config
- Angular interceptors, route guards, Reactive Forms

## Steps

### Backend
- [ ] Add `spring-boot-starter-security`. Restart: everything now returns 401. That's expected.
- [ ] Create `user/Role` enum (`USER`, `ADMIN`) and `user/User` entity (`id`, `email` unique, `passwordHash`, `role`, `createdAt`). Name the table `users` (`user` is a reserved word in some databases).
- [ ] Create `UserRepository` with `Optional<User> findByEmail(String email)`.
- [ ] Create a `PasswordEncoder` bean: `new BCryptPasswordEncoder()`.
- [ ] Choose a JWT approach:
  - Option A: `spring-boot-starter-oauth2-resource-server` with a symmetric (HMAC) key, using `JwtEncoder`/`JwtDecoder` beans. Less custom code.
  - Option B: the `jjwt` library with a custom `JwtAuthFilter extends OncePerRequestFilter`. More code, but you see every step.
- [ ] Store the JWT secret in config (`app.jwt.secret`), read from an environment variable. Never commit a real secret.
- [ ] Create `auth/AuthController`:
  - `POST /api/auth/register` → `RegisterRequest(email, password)` → saves a user with a hashed password → 201
  - `POST /api/auth/login` → `LoginRequest(email, password)` → verifies → returns `{ "token": "..." }`
- [ ] Create `auth/SecurityConfig`:
  ```java
  @Bean
  SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
      return http
          .csrf(csrf -> csrf.disable())            // OK for a stateless token API
          .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
          .authorizeHttpRequests(auth -> auth
              .requestMatchers("/api/auth/**").permitAll()
              .requestMatchers(HttpMethod.GET, "/api/products/**", "/api/categories/**").permitAll()
              .requestMatchers("/api/admin/**").hasRole("ADMIN")
              .anyRequest().authenticated())
          // + register your JWT filter or .oauth2ResourceServer(o -> o.jwt(...))
          .build();
  }
  ```
- [ ] Add `GET /api/users/me` returning the current user's email and role.
- [ ] Seed an admin user in `DataSeeder`.
- [ ] Test with `.http` requests: login → copy the token → call `/api/users/me` with `Authorization: Bearer <token>`.
- [ ] Paste a token into [jwt.io](https://jwt.io) and read the payload.

### Frontend
- [ ] `ng g service services/auth` with `login()`, `register()`, `logout()`, `token` and `currentUser` signals, `isLoggedIn` computed signal. Store the token in `localStorage` for now.
- [ ] `ng g interceptor interceptors/auth` → add the `Authorization` header when a token exists; register it with `provideHttpClient(withInterceptors([authInterceptor]))`.
- [ ] Handle 401 responses in the interceptor: log out and redirect to `/login`.
- [ ] `ng g guard guards/auth` (a `CanActivateFn`) and an admin guard.
- [ ] Login and register pages using Reactive Forms with validation messages.
- [ ] Navbar: show Login/Register or the user's email + Logout.

- [ ] Commit: `Phase 4: JWT authentication`

## Done when

- Register → login → the navbar shows you as logged in; refresh keeps you logged in.
- Calling a protected endpoint without a token gets 401; as a USER on an admin endpoint gets 403.
- Visiting a guarded route while logged out redirects to `/login`.

## Check yourself

- 401 vs 403: what's the difference?
- Why hash passwords instead of encrypting them? Why BCrypt instead of SHA-256?
- Anyone can decode a JWT. So what stops a user from changing `"role": "USER"` to `"ADMIN"`?
- Why is it OK to disable CSRF here? When would it not be OK?
- What are the risks of storing a token in `localStorage`? What's the httpOnly cookie alternative?

## Notes

-
