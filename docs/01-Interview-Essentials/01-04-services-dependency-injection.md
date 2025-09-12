# 🔧 Services & Dependency Injection
## Master Angular's Service Layer & DI System

*Interview Frequency: **90%** | Experience Level: **All Levels** | Company Tiers: **All***

> **"Services are the backbone of Angular applications. Master dependency injection, and you master Angular's architectural philosophy."** - Angular Core Team

---

## 🎯 **Interview Success Framework**

### **What Interviewers Really Test**
- **Junior (0-2 years)**: Basic service creation, @Injectable decorator, simple HTTP calls, understanding DI basics
- **Mid-Level (2-5 years)**: Provider strategies, service lifecycle, HTTP interceptors, error handling, service communication patterns
- **Senior (5+ years)**: Advanced DI patterns, custom providers, hierarchical injection, performance optimization, service architecture

### **Company-Tier Expectations**
- **Tier 1 (FAANG)**: Deep DI understanding, custom injection tokens, performance implications, service testing strategies
- **Tier 2 (Professional)**: Practical service implementation, API integration, error handling, service organization patterns
- **Tier 3 (Community)**: Basic service usage, HTTP client fundamentals, simple data sharing between components

### **🚨 Red Flags for Interviewers** ❌
- Not understanding the difference between singleton and instance services
- Creating services without @Injectable decorator
- Not handling HTTP errors properly
- Using services for DOM manipulation instead of components
- Not understanding provider hierarchy and injection scope
- Memory leaks from unmanaged HTTP subscriptions

---

## 📚 **THEORETICAL FOUNDATION** (Understanding the Why)

### **What are Angular Services?**
Services are **singleton objects** that encapsulate business logic, data access, and shared functionality. They promote **separation of concerns** by moving non-UI logic out of components into reusable, testable units.

#### **Service Design Philosophy**
```typescript
// The Angular Way: Separation of Concerns
┌─────────────────────────────────────────────────────────────┐
│                 ANGULAR ARCHITECTURE                        │
│                                                             │
│  ┌─────────────────┐    ┌─────────────────┐               │
│  │   COMPONENTS    │    │    SERVICES     │               │
│  │  (Presentation) │◄──►│ (Business Logic)│               │
│  │                 │    │                 │               │
│  │ • Templates     │    │ • Data Access   │               │
│  │ • User Events   │    │ • API Calls     │               │
│  │ • UI Logic      │    │ • Business Rules│               │
│  │ • Styling       │    │ • Shared State  │               │
│  └─────────────────┘    └─────────────────┘               │
│           ▲                       ▲                        │
│           │                       │                        │
│  ┌─────────────────┐    ┌─────────────────┐               │
│  │   DIRECTIVES    │    │  HTTP CLIENT    │               │
│  │  (DOM Logic)    │    │ (Communication) │               │
│  └─────────────────┘    └─────────────────┘               │
└─────────────────────────────────────────────────────────────┘
```

#### **Why Dependency Injection Matters**
```typescript
// Without DI (Tight Coupling - Bad)
class UserComponent {
  private userService: UserService;
  
  constructor() {
    // ❌ Component creates its own dependencies
    this.userService = new UserService(new HttpClient());
    // Problems: Hard to test, tight coupling, no flexibility
  }
}

// With DI (Loose Coupling - Good)
class UserComponent {
  constructor(private userService: UserService) {
    // ✅ Angular provides the dependency
    // Benefits: Easy testing, loose coupling, flexible configuration
  }
}
```

### **Dependency Injection Deep Dive**

#### **How Angular's DI Works**
```typescript
// DI Container Mental Model
┌─────────────────────────────────────────────────────────────┐
│                   ANGULAR DI CONTAINER                      │
│                                                             │
│  Token: UserService     │  Provider: { useClass: UserService }  │
│  Token: HttpClient      │  Provider: { useClass: HttpClient }   │
│  Token: 'API_URL'       │  Provider: { useValue: 'api.com' }    │
│  Token: DataService     │  Provider: { useFactory: factory }    │
│                                                             │
│  When component asks for UserService:                      │
│  1. Check if instance exists in current injector           │
│  2. If not, check parent injector (hierarchical)           │
│  3. Create instance using provider configuration           │
│  4. Cache instance for future use (singleton by default)   │
│  5. Inject into requesting component                       │
└─────────────────────────────────────────────────────────────┘
```

#### **Provider Strategies Explained**
```typescript
// Different ways to provide services
providers: [
  // 1. Class Provider (Default)
  UserService,  // Shorthand for { provide: UserService, useClass: UserService }
  
  // 2. Value Provider
  { provide: 'API_URL', useValue: 'https://api.example.com' },
  
  // 3. Factory Provider
  {
    provide: DataService,
    useFactory: (http: HttpClient, apiUrl: string) => {
      return new DataService(http, apiUrl);
    },
    deps: [HttpClient, 'API_URL']
  },
  
  // 4. Existing Provider (Alias)
  { provide: Logger, useExisting: ConsoleLogger },
  
  // 5. Class Provider with different implementation
  { provide: UserService, useClass: MockUserService }
]
```

### **Service Lifecycle & Scope**

#### **Singleton vs Instance Services**
```typescript
// Root Singleton (Application-wide single instance)
@Injectable({ providedIn: 'root' })
export class GlobalService {
  // One instance shared across entire application
}

// Module Singleton (One instance per module)
@NgModule({
  providers: [FeatureService]  // One instance per module
})
export class FeatureModule { }

// Component Instance (New instance per component)
@Component({
  providers: [LocalService]  // New instance for each component
})
export class MyComponent { }
```

---

## 🔥 **CORE CONCEPTS** (Must-Know for All Interviews)

### **1. Basic Service Creation** ⭐⭐⭐

#### **Essential Service Structure**
```typescript
// Modern Angular Service (17+)
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, BehaviorSubject, throwError } from 'rxjs';
import { catchError, retry, shareReplay } from 'rxjs/operators';

@Injectable({ providedIn: 'root' })
export class UserService {
  // Modern inject() function (Angular 14+)
  private http = inject(HttpClient);
  
  // State management
  private usersSubject = new BehaviorSubject<User[]>([]);
  public users$ = this.usersSubject.asObservable();
  
  // Cache for performance
  private userCache = new Map<string, User>();
  
  // API endpoints
  private readonly API_URL = 'https://api.example.com/users';
  
  /**
   * Get all users with caching
   */
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.API_URL).pipe(
      retry(3),  // Retry failed requests
      shareReplay(1),  // Cache the result
      catchError(this.handleError)
    );
  }
  
  /**
   * Get user by ID with caching
   */
  getUserById(id: string): Observable<User> {
    // Check cache first
    const cachedUser = this.userCache.get(id);
    if (cachedUser) {
      return new Observable(observer => {
        observer.next(cachedUser);
        observer.complete();
      });
    }
    
    return this.http.get<User>(`${this.API_URL}/${id}`).pipe(
      tap(user => this.userCache.set(id, user)),  // Cache the result
      catchError(this.handleError)
    );
  }
  
  /**
   * Create new user
   */
  createUser(userData: Partial<User>): Observable<User> {
    return this.http.post<User>(this.API_URL, userData).pipe(
      tap(newUser => {
        // Update local state
        const currentUsers = this.usersSubject.value;
        this.usersSubject.next([...currentUsers, newUser]);
        
        // Update cache
        this.userCache.set(newUser.id, newUser);
      }),
      catchError(this.handleError)
    );
  }
  
  /**
   * Update existing user
   */
  updateUser(id: string, updates: Partial<User>): Observable<User> {
    return this.http.put<User>(`${this.API_URL}/${id}`, updates).pipe(
      tap(updatedUser => {
        // Update local state
        const currentUsers = this.usersSubject.value;
        const index = currentUsers.findIndex(u => u.id === id);
        if (index !== -1) {
          const newUsers = [...currentUsers];
          newUsers[index] = updatedUser;
          this.usersSubject.next(newUsers);
        }
        
        // Update cache
        this.userCache.set(id, updatedUser);
      }),
      catchError(this.handleError)
    );
  }
  
  /**
   * Delete user
   */
  deleteUser(id: string): Observable<void> {
    return this.http.delete<void>(`${this.API_URL}/${id}`).pipe(
      tap(() => {
        // Update local state
        const currentUsers = this.usersSubject.value;
        this.usersSubject.next(currentUsers.filter(u => u.id !== id));
        
        // Remove from cache
        this.userCache.delete(id);
      }),
      catchError(this.handleError)
    );
  }
  
  /**
   * Search users
   */
  searchUsers(query: string): Observable<User[]> {
    const params = new HttpParams().set('q', query);
    return this.http.get<User[]>(`${this.API_URL}/search`, { params }).pipe(
      debounceTime(300),  // Debounce search requests
      distinctUntilChanged(),
      switchMap(() => this.http.get<User[]>(`${this.API_URL}/search`, { params })),
      catchError(this.handleError)
    );
  }
  
  /**
   * Clear cache (useful for testing)
   */
  clearCache(): void {
    this.userCache.clear();
    this.usersSubject.next([]);
  }
  
  /**
   * Centralized error handling
   */
  private handleError = (error: any): Observable<never> => {
    console.error('UserService Error:', error);
    
    // Different error handling based on error type
    if (error.status === 404) {
      return throwError(() => new Error('User not found'));
    } else if (error.status === 401) {
      return throwError(() => new Error('Unauthorized access'));
    } else if (error.status === 500) {
      return throwError(() => new Error('Server error. Please try again later.'));
    }
    
    return throwError(() => new Error('An unexpected error occurred'));
  };
}

// User interface
interface User {
  id: string;
  name: string;
  email: string;
  avatar?: string;
  role: 'admin' | 'user' | 'guest';
  createdAt: Date;
  lastLogin?: Date;
}
```

**🎯 Interview Q**: *"What's the difference between constructor injection and inject() function?"*
**✅ Answer**: 
- **Constructor injection**: Traditional approach, required for backward compatibility
- **inject() function**: Modern approach (Angular 14+), can be used anywhere in injection context
- **Benefits of inject()**: More flexible, can be used in factory functions, easier testing

### **2. HTTP Client Mastery** ⭐⭐⭐

#### **Advanced HTTP Patterns**
```typescript
@Injectable({ providedIn: 'root' })
export class ApiService {
  private http = inject(HttpClient);
  private baseUrl = inject('API_BASE_URL');
  
  /**
   * Generic HTTP GET with type safety
   */
  get<T>(endpoint: string, options?: {
    params?: HttpParams;
    headers?: HttpHeaders;
  }): Observable<T> {
    return this.http.get<T>(`${this.baseUrl}/${endpoint}`, options).pipe(
      retry(2),
      timeout(30000),  // 30 second timeout
      catchError(this.handleError)
    );
  }
  
  /**
   * POST with optimistic updates
   */
  post<T>(endpoint: string, data: any): Observable<T> {
    const headers = new HttpHeaders({
      'Content-Type': 'application/json',
      'X-Requested-With': 'XMLHttpRequest'
    });
    
    return this.http.post<T>(`${this.baseUrl}/${endpoint}`, data, { headers }).pipe(
      catchError(this.handleError)
    );
  }
  
  /**
   * File upload with progress tracking
   */
  uploadFile(file: File, endpoint: string): Observable<HttpEvent<any>> {
    const formData = new FormData();
    formData.append('file', file, file.name);
    
    const req = new HttpRequest('POST', `${this.baseUrl}/${endpoint}`, formData, {
      reportProgress: true,
      responseType: 'json'
    });
    
    return this.http.request(req);
  }
  
  /**
   * Download file with proper headers
   */
  downloadFile(endpoint: string, filename: string): Observable<Blob> {
    return this.http.get(`${this.baseUrl}/${endpoint}`, {
      responseType: 'blob',
      headers: new HttpHeaders({
        'Accept': 'application/octet-stream'
      })
    }).pipe(
      tap(blob => {
        // Create download link
        const url = window.URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = filename;
        a.click();
        window.URL.revokeObjectURL(url);
      })
    );
  }
  
  private handleError = (error: HttpErrorResponse): Observable<never> => {
    // Log error for debugging
    console.error('HTTP Error:', error);
    
    // User-friendly error messages
    let errorMessage = 'An error occurred';
    
    if (error.error instanceof ErrorEvent) {
      // Client-side error
      errorMessage = `Error: ${error.error.message}`;
    } else {
      // Server-side error
      switch (error.status) {
        case 400:
          errorMessage = 'Bad request. Please check your input.';
          break;
        case 401:
          errorMessage = 'Unauthorized. Please log in again.';
          break;
        case 403:
          errorMessage = 'Forbidden. You do not have permission.';
          break;
        case 404:
          errorMessage = 'Resource not found.';
          break;
        case 500:
          errorMessage = 'Server error. Please try again later.';
          break;
        default:
          errorMessage = `Error ${error.status}: ${error.message}`;
      }
    }
    
    return throwError(() => new Error(errorMessage));
  };
}
```

### **3. HTTP Interceptors** ⭐⭐

#### **Authentication Interceptor**
```typescript
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  private authService = inject(AuthService);
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Get token from auth service
    const token = this.authService.getToken();
    
    // Clone request and add authorization header
    let authReq = req;
    if (token) {
      authReq = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${token}`)
      });
    }
    
    // Handle response
    return next.handle(authReq).pipe(
      catchError((error: HttpErrorResponse) => {
        if (error.status === 401) {
          // Token expired, redirect to login
          this.authService.logout();
          return throwError(() => error);
        }
        return throwError(() => error);
      })
    );
  }
}

// Register interceptor
providers: [
  {
    provide: HTTP_INTERCEPTORS,
    useClass: AuthInterceptor,
    multi: true
  }
]
```

#### **Loading Interceptor**
```typescript
@Injectable()
export class LoadingInterceptor implements HttpInterceptor {
  private loadingService = inject(LoadingService);
  private activeRequests = 0;
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Start loading
    this.activeRequests++;
    this.loadingService.setLoading(true);
    
    return next.handle(req).pipe(
      finalize(() => {
        // Stop loading when request completes
        this.activeRequests--;
        if (this.activeRequests === 0) {
          this.loadingService.setLoading(false);
        }
      })
    );
  }
}

@Injectable({ providedIn: 'root' })
export class LoadingService {
  private loadingSubject = new BehaviorSubject<boolean>(false);
  public loading$ = this.loadingSubject.asObservable();
  
  setLoading(loading: boolean): void {
    this.loadingSubject.next(loading);
  }
}
```

---

## 🏗️ **ADVANCED DEPENDENCY INJECTION PATTERNS**

### **4. Custom Injection Tokens** ⭐⭐

#### **Configuration Injection**
```typescript
// Create injection tokens for configuration
export const API_CONFIG = new InjectionToken<ApiConfig>('api.config');
export const FEATURE_FLAGS = new InjectionToken<FeatureFlags>('feature.flags');

// Interfaces
interface ApiConfig {
  baseUrl: string;
  timeout: number;
  retryAttempts: number;
}

interface FeatureFlags {
  enableNewFeature: boolean;
  enableBetaFeatures: boolean;
}

// Provide configuration
providers: [
  {
    provide: API_CONFIG,
    useValue: {
      baseUrl: 'https://api.production.com',
      timeout: 30000,
      retryAttempts: 3
    }
  },
  {
    provide: FEATURE_FLAGS,
    useFactory: () => {
      return {
        enableNewFeature: environment.production,
        enableBetaFeatures: !environment.production
      };
    }
  }
]

// Use in service
@Injectable({ providedIn: 'root' })
export class ConfigurableService {
  constructor(
    @Inject(API_CONFIG) private config: ApiConfig,
    @Inject(FEATURE_FLAGS) private features: FeatureFlags
  ) {}
  
  makeRequest() {
    if (this.features.enableNewFeature) {
      // Use new API endpoint
      return this.http.get(`${this.config.baseUrl}/v2/data`);
    } else {
      // Use legacy endpoint
      return this.http.get(`${this.config.baseUrl}/v1/data`);
    }
  }
}
```

### **5. Factory Providers** ⭐⭐

#### **Dynamic Service Creation**
```typescript
// Factory function for creating configured services
export function createDataService(
  http: HttpClient,
  config: ApiConfig,
  logger: Logger
): DataService {
  const service = new DataService(http, config.baseUrl);
  service.setLogger(logger);
  service.setTimeout(config.timeout);
  return service;
}

// Provider configuration
providers: [
  {
    provide: DataService,
    useFactory: createDataService,
    deps: [HttpClient, API_CONFIG, Logger]
  }
]
```

### **6. Hierarchical Injection** ⭐⭐

#### **Module-Scoped Services**
```typescript
// Feature module with scoped services
@NgModule({
  providers: [
    // These services are singletons within this module
    ShoppingCartService,
    PaymentService,
    
    // Override global service with feature-specific implementation
    { provide: NotificationService, useClass: EcommerceNotificationService }
  ]
})
export class EcommerceModule { }

// Component-scoped service
@Component({
  selector: 'app-product-detail',
  providers: [
    // New instance for each component instance
    ProductAnalyticsService
  ]
})
export class ProductDetailComponent {
  constructor(
    private analytics: ProductAnalyticsService,  // Component-scoped
    private cart: ShoppingCartService,           // Module-scoped
    private user: UserService                    // Root-scoped
  ) {}
}
```

---

## 🌟 **REAL-WORLD SCENARIOS** (Production Examples)

### **E-commerce Shopping Cart Service**
```typescript
@Injectable({ providedIn: 'root' })
export class ShoppingCartService {
  private http = inject(HttpClient);
  private storageService = inject(StorageService);
  
  // Cart state
  private cartItemsSubject = new BehaviorSubject<CartItem[]>([]);
  public cartItems$ = this.cartItemsSubject.asObservable();
  
  // Computed properties
  public totalItems$ = this.cartItems$.pipe(
    map(items => items.reduce((total, item) => total + item.quantity, 0))
  );
  
  public totalPrice$ = this.cartItems$.pipe(
    map(items => items.reduce((total, item) => total + (item.price * item.quantity), 0))
  );
  
  constructor() {
    // Load cart from local storage on service initialization
    this.loadCartFromStorage();
  }
  
  /**
   * Add item to cart with optimistic updates
   */
  addToCart(product: Product, quantity: number = 1): Observable<void> {
    const currentItems = this.cartItemsSubject.value;
    const existingItem = currentItems.find(item => item.productId === product.id);
    
    let newItems: CartItem[];
    if (existingItem) {
      // Update quantity
      newItems = currentItems.map(item =>
        item.productId === product.id
          ? { ...item, quantity: item.quantity + quantity }
          : item
      );
    } else {
      // Add new item
      const newItem: CartItem = {
        productId: product.id,
        name: product.name,
        price: product.price,
        quantity,
        imageUrl: product.imageUrl
      };
      newItems = [...currentItems, newItem];
    }
    
    // Optimistic update
    this.cartItemsSubject.next(newItems);
    this.saveCartToStorage();
    
    // Sync with server
    return this.syncCartWithServer(newItems).pipe(
      catchError(error => {
        // Rollback on error
        this.cartItemsSubject.next(currentItems);
        this.saveCartToStorage();
        return throwError(() => error);
      })
    );
  }
  
  /**
   * Remove item from cart
   */
  removeFromCart(productId: string): void {
    const currentItems = this.cartItemsSubject.value;
    const newItems = currentItems.filter(item => item.productId !== productId);
    
    this.cartItemsSubject.next(newItems);
    this.saveCartToStorage();
    
    // Sync with server in background
    this.syncCartWithServer(newItems).subscribe();
  }
  
  /**
   * Update item quantity
   */
  updateQuantity(productId: string, quantity: number): void {
    if (quantity <= 0) {
      this.removeFromCart(productId);
      return;
    }
    
    const currentItems = this.cartItemsSubject.value;
    const newItems = currentItems.map(item =>
      item.productId === productId
        ? { ...item, quantity }
        : item
    );
    
    this.cartItemsSubject.next(newItems);
    this.saveCartToStorage();
    
    // Debounced server sync
    this.debouncedServerSync(newItems);
  }
  
  /**
   * Clear entire cart
   */
  clearCart(): Observable<void> {
    this.cartItemsSubject.next([]);
    this.saveCartToStorage();
    
    return this.http.delete('/api/cart').pipe(
      catchError(this.handleError)
    );
  }
  
  /**
   * Proceed to checkout
   */
  checkout(): Observable<CheckoutSession> {
    const items = this.cartItemsSubject.value;
    
    return this.http.post<CheckoutSession>('/api/checkout', {
      items,
      timestamp: new Date().toISOString()
    }).pipe(
      tap(() => {
        // Clear cart after successful checkout initiation
        this.clearCart().subscribe();
      }),
      catchError(this.handleError)
    );
  }
  
  /**
   * Load cart from local storage
   */
  private loadCartFromStorage(): void {
    try {
      const savedCart = this.storageService.getItem('shopping_cart');
      if (savedCart) {
        const cartItems = JSON.parse(savedCart) as CartItem[];
        this.cartItemsSubject.next(cartItems);
      }
    } catch (error) {
      console.error('Error loading cart from storage:', error);
    }
  }
  
  /**
   * Save cart to local storage
   */
  private saveCartToStorage(): void {
    try {
      const cartItems = this.cartItemsSubject.value;
      this.storageService.setItem('shopping_cart', JSON.stringify(cartItems));
    } catch (error) {
      console.error('Error saving cart to storage:', error);
    }
  }
  
  /**
   * Sync cart with server
   */
  private syncCartWithServer(items: CartItem[]): Observable<void> {
    return this.http.put<void>('/api/cart', { items });
  }
  
  /**
   * Debounced server sync for quantity updates
   */
  private debouncedServerSync = debounce((items: CartItem[]) => {
    this.syncCartWithServer(items).subscribe();
  }, 1000);
  
  private handleError = (error: any): Observable<never> => {
    console.error('ShoppingCartService Error:', error);
    return throwError(() => new Error('Cart operation failed'));
  };
}

// Interfaces
interface CartItem {
  productId: string;
  name: string;
  price: number;
  quantity: number;
  imageUrl: string;
}

interface Product {
  id: string;
  name: string;
  price: number;
  imageUrl: string;
}

interface CheckoutSession {
  sessionId: string;
  checkoutUrl: string;
  expiresAt: Date;
}
```

---

## 🎯 **INTERVIEW SCENARIOS** (Company-Tier Specific)

### **Tier 1 (FAANG) Questions**
```typescript
// Q: "Design a caching service with LRU eviction policy"
@Injectable({ providedIn: 'root' })
export class LRUCacheService<T> {
  private cache = new Map<string, T>();
  private accessOrder = new Map<string, number>();
  private accessCounter = 0;
  
  constructor(@Inject('CACHE_SIZE') private maxSize: number = 100) {}
  
  get(key: string): T | null {
    if (this.cache.has(key)) {
      // Update access order
      this.accessOrder.set(key, ++this.accessCounter);
      return this.cache.get(key)!;
    }
    return null;
  }
  
  set(key: string, value: T): void {
    if (this.cache.size >= this.maxSize && !this.cache.has(key)) {
      // Evict least recently used item
      const lruKey = this.findLRUKey();
      this.cache.delete(lruKey);
      this.accessOrder.delete(lruKey);
    }
    
    this.cache.set(key, value);
    this.accessOrder.set(key, ++this.accessCounter);
  }
  
  private findLRUKey(): string {
    let lruKey = '';
    let lruAccess = Infinity;
    
    for (const [key, access] of this.accessOrder) {
      if (access < lruAccess) {
        lruAccess = access;
        lruKey = key;
      }
    }
    
    return lruKey;
  }
}
```

### **Tier 2 (Professional) Questions**
```typescript
// Q: "Create a service for managing user preferences with persistence"
@Injectable({ providedIn: 'root' })
export class UserPreferencesService {
  private http = inject(HttpClient);
  private preferencesSubject = new BehaviorSubject<UserPreferences>(defaultPreferences);
  public preferences$ = this.preferencesSubject.asObservable();
  
  async loadPreferences(userId: string): Promise<void> {
    try {
      // Try to load from server first
      const serverPrefs = await this.http.get<UserPreferences>(`/api/users/${userId}/preferences`).toPromise();
      this.preferencesSubject.next(serverPrefs);
    } catch (error) {
      // Fallback to local storage
      const localPrefs = this.loadFromLocalStorage(userId);
      this.preferencesSubject.next(localPrefs);
    }
  }
  
  updatePreference<K extends keyof UserPreferences>(
    key: K, 
    value: UserPreferences[K]
  ): Observable<void> {
    const currentPrefs = this.preferencesSubject.value;
    const newPrefs = { ...currentPrefs, [key]: value };
    
    // Optimistic update
    this.preferencesSubject.next(newPrefs);
    this.saveToLocalStorage(newPrefs);
    
    // Sync with server
    return this.http.put<void>('/api/preferences', { [key]: value }).pipe(
      catchError(error => {
        // Rollback on error
        this.preferencesSubject.next(currentPrefs);
        return throwError(() => error);
      })
    );
  }
}
```

---

## 💡 **Interview Success Tips**

### **How to Approach Service Questions**
1. **Start with the interface**: Define what the service should do
2. **Consider state management**: How will the service maintain state?
3. **Think about error handling**: How will failures be managed?
4. **Plan for testing**: How will the service be unit tested?
5. **Consider performance**: Caching, lazy loading, memory usage

### **What Interviewers Evaluate**
- **Architecture Understanding**: Proper separation of concerns
- **Error Handling**: Robust error management strategies
- **Performance**: Efficient data fetching and caching
- **Testing**: Services designed for testability
- **Best Practices**: Following Angular conventions and patterns

---

*Next: [01-05 Routing & Navigation](./01-05-routing-navigation.md) - Master Angular's client-side routing*
