# 🧭 Routing & Navigation
## Master Angular's Client-Side Routing System

*Interview Frequency: **80%** | Experience Level: **All Levels** | Company Tiers: **All***

> **"Routing is the nervous system of Single Page Applications. Master it, and you master the user experience flow."** - Angular Router Team

---

## 🎯 **Interview Success Framework**

### **What Interviewers Really Test**
- **Junior (0-2 years)**: Basic routing setup, route parameters, simple navigation, understanding router-outlet
- **Mid-Level (2-5 years)**: Route guards, lazy loading, child routes, route resolvers, navigation strategies
- **Senior (5+ years)**: Advanced guard composition, preloading strategies, router performance optimization, custom route matchers

### **Company-Tier Expectations**
- **Tier 1 (FAANG)**: Router performance at scale, advanced preloading, custom route strategies, micro-frontend routing
- **Tier 2 (Professional)**: Practical routing architecture, business logic integration, user experience optimization
- **Tier 3 (Community)**: Solid routing fundamentals, basic guards, simple lazy loading implementation

### **🚨 Red Flags for Interviewers** ❌
- Not understanding the difference between routing and navigation
- Using router.navigate() when routerLink would be better
- Creating guards without understanding their execution order
- Not implementing proper loading states during route transitions
- Forgetting to unsubscribe from router observables
- Not understanding the difference between route params and query params

---

## 📚 **THEORETICAL FOUNDATION** (Understanding Router Philosophy)

### **What is Client-Side Routing?**
Client-side routing is the mechanism that **maps URLs to application states** without requiring server requests. Angular's Router creates the illusion of multiple pages while maintaining the SPA architecture.

#### **Router Design Philosophy**
```typescript
// Traditional Multi-Page Application
┌─────────────────────────────────────────────────────────────┐
│                    TRADITIONAL WEB APP                      │
│                                                             │
│  URL Change → Server Request → New HTML Page → Full Reload  │
│  /home      → GET /home      → home.html     → White Flash  │
│  /about     → GET /about     → about.html    → White Flash  │
│  /contact   → GET /contact   → contact.html  → White Flash  │
│                                                             │
│  Problems: Slow, White flashes, Lost state, Server load    │
└─────────────────────────────────────────────────────────────┘

// Angular Single Page Application
┌─────────────────────────────────────────────────────────────┐
│                     ANGULAR SPA ROUTING                     │
│                                                             │
│  URL Change → Router Match → Component Load → View Update   │
│  /home      → HomeComponent → Instant render → Smooth      │
│  /about     → AboutComponent → Instant render → Smooth     │
│  /contact   → ContactComponent → Instant render → Smooth   │
│                                                             │
│  Benefits: Fast, Smooth, State preserved, Offline capable  │
└─────────────────────────────────────────────────────────────┘
```

#### **Why Angular Router Matters**
```typescript
// Without Router (Problems):
class AppComponent {
  currentView = 'home';
  
  showHome() { this.currentView = 'home'; }      // ❌ No URL sync
  showAbout() { this.currentView = 'about'; }    // ❌ No browser history
  showContact() { this.currentView = 'contact'; } // ❌ No bookmarking
}

// With Angular Router (Solutions):
const routes: Routes = [
  { path: 'home', component: HomeComponent },      // ✅ URL mapping
  { path: 'about', component: AboutComponent },    // ✅ Browser history
  { path: 'contact', component: ContactComponent } // ✅ Bookmarkable URLs
];
// ✅ Back/forward buttons work
// ✅ Deep linking supported
// ✅ SEO-friendly URLs
```

### **Router Architecture Deep Dive**

#### **Router Mental Model**
```typescript
┌─────────────────────────────────────────────────────────────┐
│                    ANGULAR ROUTER SYSTEM                    │
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │    URL      │    │   ROUTER    │    │ COMPONENT   │     │
│  │ (/products) │───→│   ENGINE    │───→│   TREE      │     │
│  │             │    │             │    │             │     │
│  │ • Path      │    │ • Route     │    │ • Primary   │     │
│  │ • Params    │    │   Matching  │    │   Outlet    │     │
│  │ • Query     │    │ • Guards    │    │ • Auxiliary│     │
│  │ • Fragment  │    │ • Resolvers │    │   Outlets   │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│          ▲                   ▲                   ▲          │
│          │                   │                   │          │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │  BROWSER    │    │   ROUTE     │    │ NAVIGATION  │     │
│  │  LOCATION   │    │   CONFIG    │    │  EVENTS     │     │
│  │   API       │    │             │    │             │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

#### **Route Lifecycle Understanding**
```typescript
// Router Navigation Lifecycle
1. Navigation Starts → NavigationStart event
2. Route Recognition → Routes matched against URL
3. Guards Execution → CanActivate, CanLoad guards run
4. Resolve Data → Route resolvers fetch data
5. Component Creation → Target component instantiated
6. View Rendering → Component template rendered
7. Navigation End → NavigationEnd event

// Timeline visualization:
┌──────┬──────────┬─────────┬──────────┬───────────┬─────────┐
│ Start│ Recognize│ Guards  │ Resolve  │ Component │   End   │
│      │ Routes   │ Check   │ Data     │ Create    │ Success │
│ 0ms  │ 5ms      │ 50ms    │ 200ms    │ 220ms     │ 250ms   │
└──────┴──────────┴─────────┴──────────┴───────────┴─────────┘
```

### **Modern Routing Patterns (Angular 16+)**

#### **Router Data as Input**
```typescript
// Traditional approach (manual route subscription)
export class ProductDetailComponent implements OnInit {
  productId: string;
  
  constructor(private route: ActivatedRoute) {}
  
  ngOnInit() {
    this.route.paramMap.subscribe(params => {
      this.productId = params.get('id')!;
      this.loadProduct();
    });
  }
}

// Modern approach (automatic injection)
export class ProductDetailComponent {
  @Input() id!: string;  // Automatically injected from route params!
  
  ngOnInit() {
    this.loadProduct();  // No manual subscription needed
  }
}

// Route configuration
{
  path: 'product/:id',
  component: ProductDetailComponent,
  data: { bindToComponentInputs: true }  // Enable auto-injection
}
```

---

## 🔥 **CORE CONCEPTS** (Must-Know for All Interviews)

### **1. Basic Routing Setup** ⭐⭐⭐

#### **Essential Route Configuration**
```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

const routes: Routes = [
  // Basic routes
  { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
  { path: 'dashboard', component: DashboardComponent },
  { path: 'products', component: ProductListComponent },
  
  // Parameterized routes
  { path: 'product/:id', component: ProductDetailComponent },
  { path: 'user/:userId/profile', component: UserProfileComponent },
  
  // Query parameters and fragments
  { path: 'search', component: SearchComponent }, // /search?q=angular&category=tech#results
  
  // Wildcard route (must be last)
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    // Router configuration
    enableTracing: false, // Set to true for debugging
    preloadingStrategy: PreloadAllModules,
    scrollPositionRestoration: 'top'
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

#### **Navigation Techniques**
```typescript
// Component-based navigation
export class NavigationComponent {
  constructor(private router: Router) {}
  
  // Programmatic navigation
  navigateToProduct(productId: string) {
    this.router.navigate(['/product', productId]);
  }
  
  // Navigation with query parameters
  searchProducts(query: string, category: string) {
    this.router.navigate(['/search'], {
      queryParams: { q: query, category },
      fragment: 'results'
    });
  }
  
  // Relative navigation
  navigateToEdit() {
    this.router.navigate(['../edit'], { relativeTo: this.route });
  }
  
  // Navigation with state
  navigateWithState(data: any) {
    this.router.navigate(['/target'], { state: { data } });
  }
}
```

```html
<!-- Template-based navigation -->
<nav>
  <!-- Basic routerLink -->
  <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
  
  <!-- Parameterized routerLink -->
  <a [routerLink]="['/product', product.id]" routerLinkActive="active">
    {{ product.name }}
  </a>
  
  <!-- Query parameters and fragments -->
  <a [routerLink]="['/search']" 
     [queryParams]="{q: searchTerm, category: selectedCategory}"
     fragment="results">
    Search Results
  </a>
  
  <!-- Conditional navigation -->
  <a [routerLink]="user.isAdmin ? '/admin' : '/dashboard'">
    Go to {{ user.isAdmin ? 'Admin' : 'Dashboard' }}
  </a>
</nav>

<!-- Router outlet -->
<main>
  <router-outlet></router-outlet>
</main>
```

**🎯 Interview Q**: *"What's the difference between router.navigate() and routerLink?"*
**✅ Answer**: 
- **routerLink**: Declarative, template-based, better for accessibility and SEO
- **router.navigate()**: Programmatic, component-based, better for conditional logic
- **Performance**: routerLink is optimized for static navigation, navigate() for dynamic logic

### **2. Route Parameters & Data Access** ⭐⭐⭐

#### **Comprehensive Parameter Handling**
```typescript
export class ProductDetailComponent implements OnInit, OnDestroy {
  // Route data observables
  product$ = this.route.data.pipe(map(data => data['product']));
  productId$ = this.route.paramMap.pipe(map(params => params.get('id')));
  queryParams$ = this.route.queryParamMap;
  fragment$ = this.route.fragment;
  
  // Current snapshot (for one-time access)
  get currentProductId(): string | null {
    return this.route.snapshot.paramMap.get('id');
  }
  
  private destroy$ = new Subject<void>();
  
  constructor(
    private route: ActivatedRoute,
    private productService: ProductService
  ) {}
  
  ngOnInit() {
    // Subscribe to parameter changes
    this.route.paramMap.pipe(
      map(params => params.get('id')),
      filter(id => !!id),
      switchMap(id => this.productService.getProduct(id!)),
      takeUntil(this.destroy$)
    ).subscribe(product => {
      this.product = product;
    });
    
    // Handle query parameters
    this.route.queryParamMap.pipe(
      takeUntil(this.destroy$)
    ).subscribe(queryParams => {
      const view = queryParams.get('view') || 'details';
      const tab = queryParams.get('tab') || 'info';
      this.updateView(view, tab);
    });
    
    // Handle fragment changes
    this.route.fragment.pipe(
      takeUntil(this.destroy$)
    ).subscribe(fragment => {
      if (fragment) {
        this.scrollToSection(fragment);
      }
    });
  }
  
  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
  
  // URL manipulation
  updateQueryParams(params: {[key: string]: string}) {
    this.router.navigate([], {
      relativeTo: this.route,
      queryParams: params,
      queryParamsHandling: 'merge' // Preserve existing params
    });
  }
  
  private scrollToSection(fragment: string) {
    const element = document.getElementById(fragment);
    if (element) {
      element.scrollIntoView({ behavior: 'smooth' });
    }
  }
}
```

#### **Modern Parameter Injection (Angular 16+)**
```typescript
// Modern approach with automatic injection
@Component({
  selector: 'app-product-detail',
  template: `
    <div *ngIf="product">
      <h1>{{ product.name }}</h1>
      <p>View: {{ view }}</p>
      <p>Tab: {{ tab }}</p>
    </div>
  `
})
export class ProductDetailComponent implements OnInit {
  // Automatically injected from route
  @Input() id!: string;           // From route params
  @Input() view: string = 'details'; // From query params
  @Input() tab: string = 'info';     // From query params
  
  product: Product | null = null;
  
  constructor(private productService: ProductService) {}
  
  ngOnInit() {
    // Much cleaner - no manual subscription needed
    this.loadProduct();
  }
  
  async loadProduct() {
    if (this.id) {
      this.product = await this.productService.getProduct(this.id);
    }
  }
}

// Route configuration
{
  path: 'product/:id',
  component: ProductDetailComponent,
  data: { bindToComponentInputs: true }
}
```

### **3. Route Guards** ⭐⭐⭐

#### **Comprehensive Guard Implementation**
```typescript
// Modern Functional Guards (Angular 14+)
export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);
  
  if (authService.isAuthenticated()) {
    return true;
  }
  
  // Redirect to login with return URL
  return router.createUrlTree(['/login'], {
    queryParams: { returnUrl: state.url }
  });
};

export const adminGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const notificationService = inject(NotificationService);
  
  if (authService.hasRole('admin')) {
    return true;
  }
  
  notificationService.showError('Access denied. Admin privileges required.');
  return false;
};

// Data validation guard
export const productExistsGuard: CanActivateFn = (route, state) => {
  const productService = inject(ProductService);
  const router = inject(Router);
  const productId = route.paramMap.get('id');
  
  if (!productId) {
    return router.createUrlTree(['/products']);
  }
  
  return productService.productExists(productId).pipe(
    map(exists => exists || router.createUrlTree(['/products', 'not-found']))
  );
};

// Unsaved changes guard
export const canDeactivateGuard: CanDeactivateFn<any> = (component) => {
  if (component.hasUnsavedChanges?.()) {
    return confirm('You have unsaved changes. Are you sure you want to leave?');
  }
  return true;
};

// Route configuration
const routes: Routes = [
  {
    path: 'admin',
    canActivate: [authGuard, adminGuard],
    children: [
      { path: 'users', component: UserManagementComponent },
      { path: 'settings', component: AdminSettingsComponent }
    ]
  },
  {
    path: 'product/:id',
    component: ProductDetailComponent,
    canActivate: [productExistsGuard]
  },
  {
    path: 'edit-product/:id',
    component: ProductEditComponent,
    canActivate: [authGuard],
    canDeactivate: [canDeactivateGuard]
  }
];
```

#### **Guard Execution Order & Composition**
```typescript
// Guard execution order understanding
export class GuardExecutionComponent {
  /*
  GUARD EXECUTION ORDER:
  1. CanDeactivate (current route)
  2. CanLoad (lazy modules)
  3. CanActivateChild (parent routes)
  4. CanActivate (target route)
  5. Resolve (data resolvers)
  
  If ANY guard returns false, navigation is cancelled!
  */
}

// Complex guard composition
export const complexGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const permissionService = inject(PermissionService);
  const loadingService = inject(LoadingService);
  
  loadingService.setLoading(true);
  
  return authService.currentUser$.pipe(
    switchMap(user => {
      if (!user) {
        return of(false);
      }
      
      const requiredPermission = route.data?.['permission'];
      if (!requiredPermission) {
        return of(true);
      }
      
      return permissionService.hasPermission(user.id, requiredPermission);
    }),
    finalize(() => loadingService.setLoading(false))
  );
};
```

### **4. Lazy Loading Strategies** ⭐⭐

#### **Module-Based Lazy Loading**
```typescript
// app-routing.module.ts
const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
    canLoad: [authGuard]
  },
  {
    path: 'shop',
    loadChildren: () => import('./shop/shop.module').then(m => m.ShopModule)
  }
];

// admin-routing.module.ts
const adminRoutes: Routes = [
  { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
  { path: 'dashboard', component: AdminDashboardComponent },
  { path: 'users', component: UserManagementComponent },
  {
    path: 'products',
    loadChildren: () => import('./products/products.module').then(m => m.ProductsModule)
  }
];

@NgModule({
  imports: [RouterModule.forChild(adminRoutes)],
  exports: [RouterModule]
})
export class AdminRoutingModule { }
```

#### **Component-Based Lazy Loading (Angular 14+)**
```typescript
// Modern standalone component lazy loading
const routes: Routes = [
  {
    path: 'profile',
    loadComponent: () => import('./profile/profile.component').then(c => c.ProfileComponent)
  },
  {
    path: 'settings',
    loadChildren: () => import('./settings/settings.routes').then(r => r.SETTINGS_ROUTES)
  }
];

// settings.routes.ts
import { Routes } from '@angular/router';

export const SETTINGS_ROUTES: Routes = [
  {
    path: '',
    loadComponent: () => import('./settings-layout.component').then(c => c.SettingsLayoutComponent),
    children: [
      {
        path: 'account',
        loadComponent: () => import('./account-settings.component').then(c => c.AccountSettingsComponent)
      },
      {
        path: 'privacy',
        loadComponent: () => import('./privacy-settings.component').then(c => c.PrivacySettingsComponent)
      }
    ]
  }
];
```

#### **Preloading Strategies**
```typescript
// Custom preloading strategy
@Injectable()
export class CustomPreloadingStrategy implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    // Only preload routes marked for preloading
    if (route.data && route.data['preload']) {
      console.log('Preloading:', route.path);
      return load();
    }
    return of(null);
  }
}

// Route configuration with preloading
const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
    data: { preload: false } // Don't preload admin module
  },
  {
    path: 'shop',
    loadChildren: () => import('./shop/shop.module').then(m => m.ShopModule),
    data: { preload: true } // Preload shop module
  }
];

// Router configuration
RouterModule.forRoot(routes, {
  preloadingStrategy: CustomPreloadingStrategy
})
```

---

## 🏗️ **ADVANCED ROUTING PATTERNS**

### **5. Route Resolvers** ⭐⭐

#### **Data Resolution Before Navigation**
```typescript
// Modern functional resolver
export const productResolver: ResolveFn<Product> = (route) => {
  const productService = inject(ProductService);
  const router = inject(Router);
  const productId = route.paramMap.get('id');
  
  if (!productId) {
    router.navigate(['/products']);
    return EMPTY;
  }
  
  return productService.getProduct(productId).pipe(
    catchError(() => {
      router.navigate(['/products', 'not-found']);
      return EMPTY;
    })
  );
};

// Complex resolver with multiple data sources
export const dashboardResolver: ResolveFn<DashboardData> = () => {
  const userService = inject(UserService);
  const statsService = inject(StatsService);
  const notificationService = inject(NotificationService);
  
  return forkJoin({
    user: userService.getCurrentUser(),
    stats: statsService.getDashboardStats(),
    notifications: notificationService.getUnreadNotifications()
  }).pipe(
    map(data => ({
      user: data.user,
      stats: data.stats,
      notifications: data.notifications,
      lastUpdated: new Date()
    }))
  );
};

// Route configuration
{
  path: 'product/:id',
  component: ProductDetailComponent,
  resolve: {
    product: productResolver
  }
},
{
  path: 'dashboard',
  component: DashboardComponent,
  resolve: {
    dashboardData: dashboardResolver
  }
}

// Component usage
export class ProductDetailComponent implements OnInit {
  product$ = this.route.data.pipe(map(data => data['product']));
  
  constructor(private route: ActivatedRoute) {}
}
```

### **6. Child Routes & Nested Layouts** ⭐⭐

#### **Hierarchical Route Structure**
```typescript
// Main layout with nested routes
const routes: Routes = [
  {
    path: 'app',
    component: AppLayoutComponent,
    children: [
      {
        path: 'dashboard',
        component: DashboardComponent
      },
      {
        path: 'users',
        component: UsersLayoutComponent,
        children: [
          { path: '', component: UserListComponent },
          { path: 'create', component: UserCreateComponent },
          { path: ':id', component: UserDetailComponent },
          { path: ':id/edit', component: UserEditComponent }
        ]
      }
    ]
  }
];
```

```html
<!-- app-layout.component.html -->
<div class="app-layout">
  <header>
    <nav>
      <a routerLink="/app/dashboard">Dashboard</a>
      <a routerLink="/app/users">Users</a>
    </nav>
  </header>
  
  <main>
    <!-- Child routes render here -->
    <router-outlet></router-outlet>
  </main>
</div>

<!-- users-layout.component.html -->
<div class="users-layout">
  <aside>
    <nav>
      <a routerLink="/app/users">All Users</a>
      <a routerLink="/app/users/create">Create User</a>
    </nav>
  </aside>
  
  <section>
    <!-- Nested child routes render here -->
    <router-outlet></router-outlet>
  </section>
</div>
```

---

## 🌟 **REAL-WORLD SCENARIOS** (Production Examples)

### **E-commerce Routing Architecture**
```typescript
// Complete e-commerce routing setup
const routes: Routes = [
  // Public routes
  { path: '', redirectTo: '/shop', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  
  // Shop routes with category filtering
  {
    path: 'shop',
    component: ShopLayoutComponent,
    children: [
      { path: '', component: ProductListComponent },
      { path: 'category/:category', component: ProductListComponent },
      { path: 'search', component: SearchResultsComponent },
      {
        path: 'product/:id',
        component: ProductDetailComponent,
        resolve: { product: productResolver },
        canActivate: [productExistsGuard]
      }
    ]
  },
  
  // User account routes (protected)
  {
    path: 'account',
    canActivate: [authGuard],
    children: [
      {
        path: '',
        component: AccountLayoutComponent,
        children: [
          { path: '', redirectTo: 'profile', pathMatch: 'full' },
          { path: 'profile', component: ProfileComponent },
          { path: 'orders', component: OrderHistoryComponent },
          { path: 'order/:id', component: OrderDetailComponent },
          { path: 'wishlist', component: WishlistComponent },
          { path: 'addresses', component: AddressManagementComponent }
        ]
      }
    ]
  },
  
  // Checkout flow (protected)
  {
    path: 'checkout',
    canActivate: [authGuard, cartNotEmptyGuard],
    children: [
      { path: '', redirectTo: 'shipping', pathMatch: 'full' },
      { path: 'shipping', component: ShippingComponent },
      { path: 'payment', component: PaymentComponent },
      { path: 'review', component: OrderReviewComponent },
      { path: 'confirmation/:orderId', component: OrderConfirmationComponent }
    ]
  },
  
  // Admin routes (role-based protection)
  {
    path: 'admin',
    canActivate: [authGuard, adminGuard],
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule)
  },
  
  // Error handling
  { path: '404', component: NotFoundComponent },
  { path: '403', component: ForbiddenComponent },
  { path: '**', redirectTo: '/404' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    // Production optimizations
    preloadingStrategy: PreloadAllModules,
    scrollPositionRestoration: 'top',
    anchorScrolling: 'enabled',
    scrollOffset: [0, 64], // Account for fixed header
    enableTracing: false // Set to true for debugging
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### **Navigation Service for Complex Apps**
```typescript
@Injectable({ providedIn: 'root' })
export class NavigationService {
  private router = inject(Router);
  private route = inject(ActivatedRoute);
  private location = inject(Location);
  
  // Navigation history
  private navigationHistory: string[] = [];
  private maxHistoryLength = 10;
  
  // Current route information
  public currentRoute$ = this.router.events.pipe(
    filter(event => event instanceof NavigationEnd),
    map(event => (event as NavigationEnd).url)
  );
  
  constructor() {
    // Track navigation history
    this.currentRoute$.subscribe(url => {
      this.navigationHistory.push(url);
      if (this.navigationHistory.length > this.maxHistoryLength) {
        this.navigationHistory.shift();
      }
    });
  }
  
  /**
   * Navigate to product with category context
   */
  navigateToProduct(productId: string, categoryId?: string) {
    const extras: NavigationExtras = {};
    
    if (categoryId) {
      extras.queryParams = { category: categoryId };
    }
    
    return this.router.navigate(['/shop/product', productId], extras);
  }
  
  /**
   * Navigate to search with filters
   */
  navigateToSearch(query: string, filters?: SearchFilters) {
    const queryParams: any = { q: query };
    
    if (filters) {
      if (filters.category) queryParams.category = filters.category;
      if (filters.minPrice) queryParams.minPrice = filters.minPrice;
      if (filters.maxPrice) queryParams.maxPrice = filters.maxPrice;
      if (filters.brand) queryParams.brand = filters.brand;
    }
    
    return this.router.navigate(['/shop/search'], { queryParams });
  }
  
  /**
   * Smart back navigation
   */
  goBack() {
    if (this.navigationHistory.length > 1) {
      this.location.back();
    } else {
      this.router.navigate(['/']);
    }
  }
  
  /**
   * Navigate to login with return URL
   */
  navigateToLogin(returnUrl?: string) {
    const url = returnUrl || this.router.url;
    return this.router.navigate(['/login'], {
      queryParams: { returnUrl: url }
    });
  }
  
  /**
   * Navigate after successful login
   */
  navigateAfterLogin() {
    const returnUrl = this.route.snapshot.queryParamMap.get('returnUrl');
    return this.router.navigate([returnUrl || '/dashboard']);
  }
  
  /**
   * Navigate with loading state
   */
  async navigateWithLoading(commands: any[], extras?: NavigationExtras) {
    const loadingService = inject(LoadingService);
    
    loadingService.setLoading(true);
    try {
      return await this.router.navigate(commands, extras);
    } finally {
      setTimeout(() => loadingService.setLoading(false), 500);
    }
  }
  
  /**
   * Check if route is active
   */
  isRouteActive(route: string): boolean {
    return this.router.isActive(route, {
      paths: 'subset',
      queryParams: 'subset',
      fragment: 'ignored',
      matrixParams: 'ignored'
    });
  }
  
  /**
   * Get current route parameters
   */
  getCurrentParams(): Observable<{[key: string]: any}> {
    return combineLatest([
      this.route.paramMap,
      this.route.queryParamMap
    ]).pipe(
      map(([params, queryParams]) => ({
        params: this.paramsToObject(params),
        queryParams: this.paramsToObject(queryParams)
      }))
    );
  }
  
  private paramsToObject(paramMap: ParamMap): {[key: string]: string} {
    const obj: {[key: string]: string} = {};
    paramMap.keys.forEach(key => {
      obj[key] = paramMap.get(key)!;
    });
    return obj;
  }
}

interface SearchFilters {
  category?: string;
  minPrice?: number;
  maxPrice?: number;
  brand?: string;
}
```

---

## 🎯 **INTERVIEW SCENARIOS** (Company-Tier Specific)

### **Tier 1 (FAANG) Questions**
```typescript
// Q: "Design a router for a micro-frontend architecture"
@Injectable()
export class MicrofrontendRoutingStrategy {
  
  loadMicrofrontend(name: string): Promise<any> {
    return import(/* webpackChunkName: "[request]" */ `./microfrontends/${name}`)
      .then(module => module.default);
  }
  
  // Dynamic route registration
  registerMicrofrontendRoutes(name: string, routes: Routes) {
    const router = inject(Router);
    const currentConfig = router.config;
    
    // Insert microfrontend routes
    const newRoutes = [
      ...currentConfig,
      {
        path: name,
        loadChildren: () => this.loadMicrofrontend(name)
      }
    ];
    
    router.resetConfig(newRoutes);
  }
}
```

### **Tier 2 (Professional) Questions**
```typescript
// Q: "Implement breadcrumb navigation"
@Injectable({ providedIn: 'root' })
export class BreadcrumbService {
  private breadcrumbs$ = new BehaviorSubject<Breadcrumb[]>([]);
  
  getBreadcrumbs(): Observable<Breadcrumb[]> {
    return this.breadcrumbs$.asObservable();
  }
  
  setBreadcrumbs(route: ActivatedRoute) {
    const breadcrumbs = this.buildBreadcrumbs(route.root);
    this.breadcrumbs$.next(breadcrumbs);
  }
  
  private buildBreadcrumbs(route: ActivatedRoute): Breadcrumb[] {
    // Implementation for building breadcrumbs from route hierarchy
    const breadcrumbs: Breadcrumb[] = [];
    // ... breadcrumb building logic
    return breadcrumbs;
  }
}
```

---

## 💡 **Interview Success Tips**

### **How to Approach Routing Questions**
1. **Start with basics**: Demonstrate route configuration understanding
2. **Show navigation patterns**: Both programmatic and template-based
3. **Discuss guards**: Security and user experience considerations
4. **Mention performance**: Lazy loading and preloading strategies
5. **Consider user experience**: Loading states, error handling, accessibility

### **What Interviewers Evaluate**
- **Route Architecture**: Logical organization and scalability
- **User Experience**: Smooth navigation and loading states
- **Security**: Proper guard implementation and access control
- **Performance**: Efficient code splitting and preloading
- **Modern Features**: Angular 16+ router enhancements

---

*Next: [01-06 Forms & Validation](./01-06-forms-validation.md) - Master Angular's powerful form system*
