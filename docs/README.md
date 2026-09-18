# E-commerce App — Learning Docs

Spring Boot (backend) + Angular (frontend) e-commerce app, built phase by phase.

## Start here every session

1. Open [PROGRESS.md](PROGRESS.md) and check **Current position**.
2. Open that phase file and continue from the first unchecked box.
3. When you stop, update **Current position** and add a line to the **Session log**.

## Files

| File | What it's for |
|---|---|
| [PROGRESS.md](PROGRESS.md) | Where you left off, phase checklist, session log |
| [architecture.md](architecture.md) | Big picture: diagram, data model, API endpoints, folder layout |
| [phases/00-setup.md](phases/00-setup.md) | Fix JDK, dependencies, H2 database |
| [phases/01-product-api.md](phases/01-product-api.md) | First REST API: Product CRUD |
| [phases/02-clean-backend.md](phases/02-clean-backend.md) | DTOs, validation, error handling, pagination |
| [phases/03-angular-frontend.md](phases/03-angular-frontend.md) | Angular app showing products |
| [phases/04-authentication.md](phases/04-authentication.md) | Users, Spring Security, JWT, login |
| [phases/05-cart-and-orders.md](phases/05-cart-and-orders.md) | Cart, checkout, orders, Postgres, Flyway |
| [phases/06-admin.md](phases/06-admin.md) | Admin role, product management, image upload |
| [phases/07-payments.md](phases/07-payments.md) | Stripe test mode, webhooks |
| [phases/08-testing.md](phases/08-testing.md) | Unit, slice, integration, frontend tests |
| [phases/09-ship-it.md](phases/09-ship-it.md) | Docker, CI, deployment |

## How each phase file is organized

- **Goal**: what works at the end
- **You'll learn**: concepts to understand, not just type
- **Steps**: checkboxes; tick them (`- [x]`) as you go
- **Done when**: how to verify the phase is finished
- **Check yourself**: questions you should be able to answer before moving on
- **Notes**: your own notes, errors you hit, and how you fixed them

## Habits

- Commit at the end of every phase (e.g. `git commit -m "Phase 1: product CRUD API"`).
- Don't skip **Check yourself**. If you can't answer a question, re-read that part before moving on.
- Write down every error and its fix in the phase's **Notes** section. That becomes your personal troubleshooting guide.
