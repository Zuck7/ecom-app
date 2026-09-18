# Phase 7 — Payments

## Goal

Checkout redirects to Stripe (test mode). After a successful payment, Stripe notifies your backend via webhook and the order becomes `PAID`.

## You'll learn

- Integrating a third-party API with an SDK
- Webhooks and verifying their signatures
- Idempotency: handling the same event arriving twice
- Keeping secrets out of the codebase

## Steps

- [ ] Create a free Stripe account; stay in **test mode**. Get the test secret key and set it as an environment variable (`STRIPE_SECRET_KEY`).
- [ ] Add the `com.stripe:stripe-java` dependency.
- [ ] Change checkout: create the order as `PENDING` (stock reserved), then create a Stripe Checkout Session with the order's line items and `orderId` in metadata. Return the session URL.
- [ ] Angular: after checkout, redirect the browser to the session URL. Add `/checkout/success` and `/checkout/cancel` pages.
- [ ] Install the Stripe CLI; run `stripe listen --forward-to localhost:8080/api/payments/webhook` and note the webhook signing secret (`STRIPE_WEBHOOK_SECRET`).
- [ ] `POST /api/payments/webhook` (permit in security config, no JWT):
  - Read the raw body as a `String`; verify with `Webhook.constructEvent(payload, sigHeader, secret)`.
  - On `checkout.session.completed`: find the order by metadata `orderId`, set `PAID` **only if it's still `PENDING`** (idempotent).
- [ ] Handle abandoned payments: a `@Scheduled` job that cancels `PENDING` orders older than, e.g., 30 minutes and restores stock.
- [ ] Pay with test card `4242 4242 4242 4242`, any future date, any CVC.

- [ ] Commit: `Phase 7: Stripe payments`

## Done when

- Paying with the test card marks the order `PAID`.
- Resending the same webhook event (`stripe events resend <id>`) doesn't break anything.
- The success page alone never marks an order paid; only the webhook does.

## Check yourself

- Why can't you trust the redirect to `/checkout/success` as proof of payment?
- What would happen without signature verification on the webhook?
- What does "idempotent" mean, and where did you make your code idempotent?

## Notes

-
