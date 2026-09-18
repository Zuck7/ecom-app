# Phase 6 — Admin

## Goal

An admin area where ADMIN users manage products, categories, and orders, including uploading product images.

## You'll learn

- Method security (`@PreAuthorize`)
- Multipart file upload and serving static files
- Reactive Forms for create/edit screens
- Lazy-loaded Angular routes

## Steps

### Backend
- [ ] Enable method security: `@EnableMethodSecurity` on your security config.
- [ ] Protect product/category create, update, delete with `@PreAuthorize("hasRole('ADMIN')")`.
- [ ] `POST /api/products/{id}/image` accepting `MultipartFile`:
  - Validate content type (`image/jpeg`, `image/png`, `image/webp`) and size.
  - Save with a generated file name (UUID), never the user's file name.
  - Store to a local `uploads/` folder for now (add to `.gitignore`), and set `imageUrl`.
  - Configure max upload size: `spring.servlet.multipart.max-file-size=5MB`.
- [ ] Serve uploaded files (a resource handler for `/uploads/**`), or later move to S3 / Cloudinary.
- [ ] Admin order endpoints: list all orders (paged, filter by status), update status.
- [ ] Soft delete for products (`active` flag) so old orders don't break.

### Frontend
- [ ] Lazy-load admin routes: `{ path: 'admin', canActivate: [adminGuard], loadChildren: () => import('./admin/admin.routes') }`.
- [ ] Admin product table with edit/delete buttons.
- [ ] Product form (create + edit, same component) using Reactive Forms and validation.
- [ ] Image upload with preview (`FormData`).
- [ ] Admin orders page with status dropdown.
- [ ] Show the Admin link in the navbar only for admins.

- [ ] Commit: `Phase 6: admin area`

## Done when

- Logged in as admin, you can create a product with an image and see it on the shop page.
- A normal user gets 403 on admin endpoints and can't reach `/admin` in the UI.

## Check yourself

- Why is hiding the Admin link in Angular not real security?
- Why not use the uploaded file's original name when saving?
- URL-based rules (`requestMatchers`) vs `@PreAuthorize`: when would you use each?
- Why soft delete instead of real delete for products?

## Notes

-
