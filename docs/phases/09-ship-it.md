# Phase 9 — Ship It

## Goal

The whole app runs with one `docker compose up`, CI checks every push, and it's deployed on the internet.

## You'll learn

- Multi-stage Docker builds
- Serving an Angular build with nginx and proxying `/api`
- Environment-based configuration and secrets
- CI with GitHub Actions
- Deploying to a platform (Render, Railway, or Fly.io)

## Steps

### Docker
- [ ] Backend `Dockerfile` (multi-stage): build stage runs `./mvnw -DskipTests package` on a JDK image; run stage copies the jar into a slim JRE image. Add a `.dockerignore`.
- [ ] Frontend `frontend/Dockerfile`: build stage runs `npm ci && ng build`; run stage copies `dist/` into `nginx`.
- [ ] `frontend/nginx.conf`: serve `index.html` for unknown routes (SPA fallback) and proxy `/api/` to the backend container.
- [ ] Extend `docker-compose.yml` with `api` and `web` services; pass config via environment variables (`SPRING_DATASOURCE_URL`, `APP_JWT_SECRET`, `STRIPE_SECRET_KEY`...).
- [ ] Create `.env.example` (committed, fake values) and `.env` (git-ignored, real values).
- [ ] `docker compose up --build` → open `http://localhost` and test the whole flow.

### Production config
- [ ] `application-prod.properties`: `ddl-auto=validate`, `show-sql=false`, H2 console off.
- [ ] Add `spring-boot-starter-actuator`; expose only `health`.
- [ ] Configure CORS properly if frontend and backend are on different domains.

### CI
- [ ] `.github/workflows/ci.yml`: on push/PR, set up JDK and Node, run `./mvnw verify` and `npm ci && npm run build && npm test -- --watch=false` in `frontend/`.
- [ ] Add a CI badge to the root `README.md`.

### Deploy
- [ ] Pick a platform (Render / Railway / Fly.io). Create a managed Postgres.
- [ ] Deploy the backend (from Dockerfile), set environment variables.
- [ ] Deploy the frontend (static site or nginx container).
- [ ] Update the Stripe webhook URL to the deployed backend.
- [ ] Write a proper root `README.md`: what it is, screenshots, tech stack, how to run locally.

- [ ] Commit and tag: `git tag v1.0.0`

## Done when

- A friend can open your URL, register, and place a test order.
- A push with a failing test shows a red CI check.

## Check yourself

- Why a multi-stage Dockerfile? Compare the image sizes.
- Why does the SPA need a fallback to `index.html` in nginx?
- Where do your secrets live in dev, CI, and production?

## Ideas after v1

- Product reviews and ratings
- Wishlist
- Email on order confirmation (Spring Mail)
- Full-text search (Postgres `tsvector` or Elasticsearch)
- Caching with Redis (`@Cacheable`)
- Refresh tokens
- Observability: structured logs, metrics, tracing

## Notes

-
