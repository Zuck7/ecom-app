# Phase 3 — Angular Frontend

## Goal

An Angular app in `frontend/` that lists products (with pagination and category filter) and shows a product detail page, using data from your Spring API.

> Note: this is modern **Angular** (TypeScript, standalone components, signals), not the old **AngularJS 1.x**, which is end of life.

## You'll learn

- Angular CLI and project structure
- Standalone components, templates, `@for` / `@if` control flow
- Services and dependency injection in Angular
- `HttpClient`, Observables, and signals
- Routing and route params
- CORS and the dev proxy

## Steps

### Create the project
- [ ] `npm install -g @angular/cli`
- [ ] From the repo root: `ng new frontend --routing --style=scss`
- [ ] `cd frontend && ng serve` → open `http://localhost:4200`
- [ ] Skim the generated files: `main.ts`, `app.config.ts`, `app.routes.ts`, `app.ts`/`app.component.ts`.

### Connect to the backend
- [ ] Create `frontend/proxy.conf.json`:
  ```json
  { "/api": { "target": "http://localhost:8080", "secure": false } }
  ```
- [ ] In `angular.json`, under `serve.options`, add `"proxyConfig": "proxy.conf.json"` (or run `ng serve --proxy-config proxy.conf.json`).
- [ ] Add `provideHttpClient()` to `providers` in `app.config.ts`.

### Models and services
- [ ] `src/app/models/product.ts`:
  ```ts
  export interface Product {
    id: number;
    name: string;
    description: string;
    price: number;
    stock: number;
    imageUrl?: string;
    categoryName?: string;
  }

  export interface Page<T> {
    content: T[];
    totalElements: number;
    totalPages: number;
    number: number;
    size: number;
  }
  ```
- [ ] `ng g service services/product-api` (creates `product-api.ts` with class `ProductApi`). Don't name it `product`: since Angular 20 the CLI drops the `Service` suffix, so you'd get a class `Product` that clashes with your `Product` model.
  ```ts
  @Injectable({ providedIn: 'root' })
  export class ProductApi {
    private http = inject(HttpClient);

    getProducts(page = 0, size = 12) {
      return this.http.get<Page<Product>>('/api/products', { params: { page, size } });
    }

    getProduct(id: number) {
      return this.http.get<Product>(`/api/products/${id}`);
    }
  }
  ```

### Pages and components
- [ ] `ng g component components/navbar` — shop name + links.
- [ ] `ng g component components/product-card` — takes a product via `input()`.
- [ ] `ng g component pages/product-list` — loads products into a `signal`, renders cards with `@for`.
- [ ] `ng g component pages/product-detail` — reads `:id` from the route, loads one product.
- [ ] `ng g component pages/not-found`.
  > Angular 20+ names files and classes without suffixes: `pages/product-list/product-list.ts` exports `ProductList` (not `ProductListComponent`).
- [ ] Routes in `app.routes.ts`:
  ```ts
  export const routes: Routes = [
    { path: '', component: ProductList },
    { path: 'products/:id', component: ProductDetail },
    { path: '**', component: NotFound },
  ];
  ```
- [ ] Add pagination buttons (Prev / Next) and a category dropdown.
- [ ] Show loading and error states.
- [ ] Style it: plain SCSS, or try Angular Material / Tailwind.

- [ ] Add `frontend/node_modules` to `.gitignore` (Angular CLI usually handles this) and commit: `Phase 3: Angular product pages`

## Done when

- With both apps running, `localhost:4200` shows your seeded products.
- Clicking a product opens its detail page; the browser back button works.
- Pagination and category filter work.

## Check yourself

- What would happen without the proxy? What exact error would the browser show, and why?
- What's the difference between an Observable and a signal? When does an HTTP Observable actually send the request?
- Why is `HttpClient` used in a service instead of directly in the component?
- How does the detail component get the `id` from the URL?

## Notes

-
