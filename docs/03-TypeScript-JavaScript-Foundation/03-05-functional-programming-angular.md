# 📈 **03-05: Functional Programming in Angular**
*Master functional programming paradigms for scalable, maintainable Angular applications*

---

## 🎯 **CHAPTER OVERVIEW**

### **Why Functional Programming Matters for Angular Developers**
Functional programming isn't just an academic exercise—it's the foundation of modern Angular development. With RxJS at its core, Angular applications naturally embrace functional paradigms. Understanding these patterns transforms how you approach state management, component design, and application architecture.

### **What You'll Master**
- **Pure Functions & Immutability**: Build predictable, testable Angular services
- **RxJS Functional Patterns**: Master operator composition and reactive programming
- **Functional Reactive Programming**: OnPush optimization and immutable state management
- **Functional State Management**: Redux patterns and NGRX functional approach
- **Error Handling**: Railway-oriented programming and functional error boundaries
- **Performance Optimization**: Functional patterns for Angular performance

### **When to Apply These Concepts**
- **Interview Scenarios**: Demonstrating advanced architectural understanding
- **Complex State Management**: Managing application state predictably
- **Performance Optimization**: Leveraging OnPush and immutable patterns
- **Team Collaboration**: Writing maintainable, testable functional code
- **Scalable Architecture**: Building applications that grow gracefully

---

## 🧠 **LEARNING OBJECTIVES**

### **🎯 Primary Goals**
By completing this chapter, you will:

1. **Master Functional Fundamentals**: Understand pure functions, immutability, and composition
2. **Apply RxJS Functionally**: Use operators as composable functions for data transformation
3. **Implement Reactive Patterns**: Build Angular applications with functional reactive programming
4. **Design Functional Architecture**: Create scalable applications using functional principles
5. **Optimize Performance**: Leverage functional patterns for Angular performance benefits
6. **Handle Errors Functionally**: Implement robust error handling with functional patterns

### **💼 Career Impact**
- **Technical Interviews**: Demonstrate advanced functional programming knowledge
- **Code Quality**: Write more maintainable and testable Angular applications
- **Performance**: Build faster applications through functional optimization
- **Architecture**: Design scalable systems using functional principles
- **Team Leadership**: Mentor others in functional programming best practices

---

## 📚 **TABLE OF CONTENTS**

### **🚀 PART I: FUNCTIONAL PROGRAMMING FOUNDATIONS**
1. **[Functional Programming Fundamentals](#fundamentals)**
2. **[Pure Functions in Angular](#pure-functions)**
3. **[Immutability Patterns](#immutability)**
4. **[Function Composition](#composition)**

### **⚡ PART II: RxJS FUNCTIONAL PATTERNS**
5. **[Operators as Functions](#operators)**
6. **[Pipe Composition](#pipe-composition)**
7. **[Reactive Functional Programming](#reactive-fp)**

### **🎨 PART III: ANGULAR FUNCTIONAL PATTERNS**
8. **[Functional Components](#functional-components)**
9. **[OnPush Change Detection](#onpush)**
10. **[Immutable State Management](#state-management)**

### **🔧 PART IV: ADVANCED FUNCTIONAL CONCEPTS**
11. **[Functional Error Handling](#error-handling)**
12. **[Railway-Oriented Programming](#railway)**
13. **[Performance Optimization](#performance)**

### **🏆 PART V: COMPANY-TIER CHALLENGES**
14. **[Tier 1: FAANG Functional Architecture](#tier1)**
15. **[Tier 2: Practical Functional Patterns](#tier2)**
16. **[Tier 3: Functional Basics](#tier3)**

---

<a name="fundamentals"></a>
## 🚀 **PART I: FUNCTIONAL PROGRAMMING FOUNDATIONS**

### **1. Functional Programming Fundamentals**

Functional programming is a paradigm that treats computation as the evaluation of mathematical functions. In Angular development, this translates to writing more predictable, testable, and maintainable code.

#### **Core Principles**

```typescript
// 🎯 PRINCIPLE 1: Functions are First-Class Citizens
// Functions can be assigned to variables, passed as arguments, and returned from other functions

interface UserProcessor<T> {
  (user: User): T;
}

const getUserName: UserProcessor<string> = (user: User) => user.name;
const getUserAge: UserProcessor<number> = (user: User) => user.age;
const isAdult: UserProcessor<boolean> = (user: User) => user.age >= 18;

// 🎯 PRINCIPLE 2: Higher-Order Functions
// Functions that operate on other functions

class UserService {
  private users$ = new BehaviorSubject<User[]>([]);

  // Higher-order function that takes a processor function
  processUsers<T>(processor: UserProcessor<T>): Observable<T[]> {
    return this.users$.pipe(
      map(users => users.map(processor))
    );
  }

  // Usage examples
  getUserNames(): Observable<string[]> {
    return this.processUsers(getUserName);
  }

  getAdultStatus(): Observable<boolean[]> {
    return this.processUsers(isAdult);
  }
}

// 🎯 PRINCIPLE 3: Function Composition
// Building complex operations by combining simple functions

const compose = <A, B, C>(f: (b: B) => C, g: (a: A) => B) => (a: A) => f(g(a));

const getUpperCaseName = compose(
  (name: string) => name.toUpperCase(),
  (user: User) => user.name
);

const formatUserDisplay = compose(
  (name: string) => `User: ${name}`,
  getUpperCaseName
);
```

#### **Angular Integration Example**

```typescript
// 🎯 Functional Component Design
@Component({
  selector: 'app-user-list',
  template: `
    <div *ngFor="let user of users$ | async">
      {{ formatUser(user) }}
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserListComponent {
  users$ = this.userService.getUsers();

  // Pure function for formatting - no side effects
  formatUser = (user: User): string => `${user.name} (${user.age})`;

  // Pure function for filtering - returns new array
  filterAdults = (users: User[]): User[] => 
    users.filter(user => user.age >= 18);

  // Composed functionality using pure functions
  adultUsers$ = this.users$.pipe(
    map(this.filterAdults)
  );

  constructor(private userService: UserService) {}
}
```

<a name="pure-functions"></a>
### **2. Pure Functions in Angular**

Pure functions are the cornerstone of functional programming. They always return the same output for the same input and have no side effects.

#### **Pure Function Characteristics**

```typescript
// ✅ PURE FUNCTION - Same input, same output, no side effects
const calculateTax = (amount: number, rate: number): number => {
  return amount * rate;
};

// ❌ IMPURE FUNCTION - Accesses external state
let taxRate = 0.1;
const calculateTaxImpure = (amount: number): number => {
  return amount * taxRate; // Depends on external variable
};

// ❌ IMPURE FUNCTION - Has side effects
const calculateTaxWithLogging = (amount: number, rate: number): number => {
  console.log(`Calculating tax for ${amount}`); // Side effect
  return amount * rate;
};
```

#### **Angular Service with Pure Functions**

```typescript
// 🎯 Pure Function-Based Service
@Injectable({
  providedIn: 'root'
})
export class CalculatorService {
  
  // Pure functions for calculations
  private add = (a: number, b: number): number => a + b;
  private multiply = (a: number, b: number): number => a * b;
  private divide = (a: number, b: number): number => b !== 0 ? a / b : 0;

  // Pure function for complex calculations
  calculateOrderTotal = (items: OrderItem[]): OrderTotal => {
    const subtotal = items.reduce((sum, item) => 
      this.add(sum, this.multiply(item.price, item.quantity)), 0
    );
    
    const tax = this.multiply(subtotal, 0.1);
    const total = this.add(subtotal, tax);
    
    return { subtotal, tax, total };
  };

  // Pure function for discount calculation
  applyDiscount = (total: number, discountPercent: number): number => {
    const discount = this.multiply(total, discountPercent / 100);
    return total - discount;
  };

  // Compose pure functions for complex operations
  calculateFinalPrice = (
    items: OrderItem[], 
    discountPercent: number
  ): number => {
    const orderTotal = this.calculateOrderTotal(items);
    return this.applyDiscount(orderTotal.total, discountPercent);
  };
}

// Usage in component
@Component({
  selector: 'app-order-summary',
  template: `
    <div class="order-summary">
      <p>Subtotal: {{ orderTotal.subtotal | currency }}</p>
      <p>Tax: {{ orderTotal.tax | currency }}</p>
      <p>Total: {{ orderTotal.total | currency }}</p>
      <p *ngIf="discount > 0">
        Final Price: {{ finalPrice | currency }}
      </p>
    </div>
  `
})
export class OrderSummaryComponent {
  @Input() items: OrderItem[] = [];
  @Input() discount = 0;

  get orderTotal(): OrderTotal {
    return this.calculator.calculateOrderTotal(this.items);
  }

  get finalPrice(): number {
    return this.calculator.calculateFinalPrice(this.items, this.discount);
  }

  constructor(private calculator: CalculatorService) {}
}
```

#### **Testing Pure Functions**

```typescript
// 🎯 Pure functions are extremely easy to test
describe('CalculatorService Pure Functions', () => {
  let service: CalculatorService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(CalculatorService);
  });

  it('should calculate order total correctly', () => {
    const items: OrderItem[] = [
      { price: 10, quantity: 2 },
      { price: 15, quantity: 1 }
    ];

    const result = service.calculateOrderTotal(items);

    expect(result.subtotal).toBe(35);
    expect(result.tax).toBe(3.5);
    expect(result.total).toBe(38.5);
  });

  it('should apply discount correctly', () => {
    const total = 100;
    const discount = 10; // 10%

    const result = service.applyDiscount(total, discount);

    expect(result).toBe(90);
  });

  it('should be deterministic - same input, same output', () => {
    const items: OrderItem[] = [{ price: 10, quantity: 1 }];
    
    const result1 = service.calculateOrderTotal(items);
    const result2 = service.calculateOrderTotal(items);
    
    expect(result1).toEqual(result2);
  });
});
```

<a name="immutability"></a>
### **3. Immutability Patterns**

Immutability is crucial for predictable state management and performance optimization in Angular applications.

#### **Immutable Data Structures**

```typescript
// 🎯 Immutable User Management
interface User {
  readonly id: number;
  readonly name: string;
  readonly email: string;
  readonly preferences: readonly UserPreference[];
}

interface UserPreference {
  readonly key: string;
  readonly value: string;
}

class ImmutableUserService {
  private users$ = new BehaviorSubject<readonly User[]>([]);

  // Pure function to add user - returns new array
  addUser = (users: readonly User[], newUser: User): readonly User[] => {
    return [...users, newUser];
  };

  // Pure function to update user - returns new array with updated user
  updateUser = (
    users: readonly User[], 
    userId: number, 
    updates: Partial<User>
  ): readonly User[] => {
    return users.map(user => 
      user.id === userId 
        ? { ...user, ...updates }
        : user
    );
  };

  // Pure function to remove user - returns new array
  removeUser = (users: readonly User[], userId: number): readonly User[] => {
    return users.filter(user => user.id !== userId);
  };

  // Pure function to update user preferences
  updateUserPreference = (
    users: readonly User[],
    userId: number,
    preference: UserPreference
  ): readonly User[] => {
    return users.map(user => {
      if (user.id !== userId) return user;
      
      const updatedPreferences = user.preferences.some(p => p.key === preference.key)
        ? user.preferences.map(p => p.key === preference.key ? preference : p)
        : [...user.preferences, preference];
        
      return { ...user, preferences: updatedPreferences };
    });
  };

  // Observable methods
  getUsers(): Observable<readonly User[]> {
    return this.users$.asObservable();
  }

  addUserAndUpdate(newUser: User): void {
    const currentUsers = this.users$.value;
    const updatedUsers = this.addUser(currentUsers, newUser);
    this.users$.next(updatedUsers);
  }

  updateUserAndNotify(userId: number, updates: Partial<User>): void {
    const currentUsers = this.users$.value;
    const updatedUsers = this.updateUser(currentUsers, userId, updates);
    this.users$.next(updatedUsers);
  }
}
```

#### **Immutable State Management Pattern**

```typescript
// 🎯 Application State with Immutability
interface AppState {
  readonly users: readonly User[];
  readonly loading: boolean;
  readonly error: string | null;
  readonly filters: readonly FilterOption[];
}

interface FilterOption {
  readonly key: string;
  readonly value: string;
  readonly active: boolean;
}

// Pure state update functions
const StateUpdaters = {
  setLoading: (state: AppState, loading: boolean): AppState => ({
    ...state,
    loading
  }),

  setError: (state: AppState, error: string | null): AppState => ({
    ...state,
    error,
    loading: false
  }),

  setUsers: (state: AppState, users: readonly User[]): AppState => ({
    ...state,
    users,
    loading: false,
    error: null
  }),

  toggleFilter: (state: AppState, filterKey: string): AppState => ({
    ...state,
    filters: state.filters.map(filter =>
      filter.key === filterKey
        ? { ...filter, active: !filter.active }
        : filter
    )
  }),

  addUser: (state: AppState, user: User): AppState => ({
    ...state,
    users: [...state.users, user]
  }),

  updateUser: (state: AppState, userId: number, updates: Partial<User>): AppState => ({
    ...state,
    users: state.users.map(user =>
      user.id === userId ? { ...user, ...updates } : user
    )
  })
};

// State management service
@Injectable({
  providedIn: 'root'
})
export class AppStateService {
  private readonly initialState: AppState = {
    users: [],
    loading: false,
    error: null,
    filters: [
      { key: 'active', value: 'Active Users', active: false },
      { key: 'premium', value: 'Premium Users', active: false }
    ]
  };

  private state$ = new BehaviorSubject<AppState>(this.initialState);

  // Public observable
  getState(): Observable<AppState> {
    return this.state$.asObservable();
  }

  // Selector methods
  getUsers(): Observable<readonly User[]> {
    return this.getState().pipe(map(state => state.users));
  }

  getFilteredUsers(): Observable<readonly User[]> {
    return this.getState().pipe(
      map(state => {
        const activeFilters = state.filters.filter(f => f.active);
        if (activeFilters.length === 0) return state.users;
        
        return state.users.filter(user => {
          // Apply filters based on active filter keys
          return activeFilters.every(filter => {
            switch (filter.key) {
              case 'active': return user.isActive;
              case 'premium': return user.isPremium;
              default: return true;
            }
          });
        });
      })
    );
  }

  getLoading(): Observable<boolean> {
    return this.getState().pipe(map(state => state.loading));
  }

  getError(): Observable<string | null> {
    return this.getState().pipe(map(state => state.error));
  }

  // State update methods
  setLoading(loading: boolean): void {
    this.updateState(state => StateUpdaters.setLoading(state, loading));
  }

  setUsers(users: readonly User[]): void {
    this.updateState(state => StateUpdaters.setUsers(state, users));
  }

  addUser(user: User): void {
    this.updateState(state => StateUpdaters.addUser(state, user));
  }

  updateUser(userId: number, updates: Partial<User>): void {
    this.updateState(state => StateUpdaters.updateUser(state, userId, updates));
  }

  toggleFilter(filterKey: string): void {
    this.updateState(state => StateUpdaters.toggleFilter(state, filterKey));
  }

  setError(error: string | null): void {
    this.updateState(state => StateUpdaters.setError(state, error));
  }

  private updateState(updater: (state: AppState) => AppState): void {
    const currentState = this.state$.value;
    const newState = updater(currentState);
    this.state$.next(newState);
  }
}
```

<a name="composition"></a>
### **4. Function Composition**

Function composition allows you to build complex operations by combining simple, reusable functions.

#### **Composition Utilities**

```typescript
// 🎯 Composition Helper Functions
type UnaryFunction<T, R> = (arg: T) => R;

// Basic composition - right to left
const compose = <A, B, C>(
  f: UnaryFunction<B, C>,
  g: UnaryFunction<A, B>
): UnaryFunction<A, C> => (a: A) => f(g(a));

// Pipe composition - left to right (more intuitive)
const pipe = <T>(...fns: Array<UnaryFunction<any, any>>) =>
  (value: T) => fns.reduce((acc, fn) => fn(acc), value);

// Async composition
const composeAsync = <A, B, C>(
  f: UnaryFunction<B, Promise<C>>,
  g: UnaryFunction<A, Promise<B>>
): UnaryFunction<A, Promise<C>> => async (a: A) => f(await g(a));

// Example usage in Angular context
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface User {
  id: number;
  name: string;
  email: string;
  isActive: boolean;
}

// Building blocks - small, focused functions
const normalizeEmail = (email: string): string => email.toLowerCase().trim();
const validateEmail = (email: string): boolean => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
const capitalizeWords = (str: string): string => 
  str.replace(/\w\S*/g, txt => 
    txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase()
  );

// User transformation functions
const extractUserData = (response: ApiResponse<User>): User => response.data;
const normalizeUserEmail = (user: User): User => ({
  ...user,
  email: normalizeEmail(user.email)
});
const formatUserName = (user: User): User => ({
  ...user,
  name: capitalizeWords(user.name)
});
const validateUser = (user: User): User => {
  if (!validateEmail(user.email)) {
    throw new Error(`Invalid email: ${user.email}`);
  }
  return user;
};

// Composed user processing pipeline
const processUser = pipe(
  extractUserData,
  normalizeUserEmail,
  formatUserName,
  validateUser
);

// More complex composition with filtering and transformation
const processUsers = (responses: ApiResponse<User>[]): User[] => {
  const processResponse = pipe(
    extractUserData,
    normalizeUserEmail,
    formatUserName
  );

  return responses
    .map(processResponse)
    .filter(user => user.isActive)
    .filter(user => validateEmail(user.email));
};
```

#### **Angular Service with Composition**

```typescript
// 🎯 Data Processing Service using Function Composition
@Injectable({
  providedIn: 'root'
})
export class DataProcessingService {
  
  // Basic transformation functions
  private readonly transformations = {
    trim: (str: string): string => str.trim(),
    lowercase: (str: string): string => str.toLowerCase(),
    removeSpaces: (str: string): string => str.replace(/\s/g, ''),
    capitalize: (str: string): string => 
      str.charAt(0).toUpperCase() + str.slice(1),
    
    // Number transformations
    round: (num: number): number => Math.round(num),
    toFixed: (decimals: number) => (num: number): number => 
      Number(num.toFixed(decimals)),
    
    // Array transformations
    unique: <T>(arr: T[]): T[] => [...new Set(arr)],
    sort: <T>(arr: T[]): T[] => [...arr].sort(),
    reverse: <T>(arr: T[]): T[] => [...arr].reverse()
  };

  // Validation functions
  private readonly validators = {
    isEmail: (email: string): boolean => 
      /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email),
    isPhone: (phone: string): boolean => 
      /^\+?[\d\s-()]{10,}$/.test(phone),
    isNotEmpty: (str: string): boolean => str.length > 0,
    isPositive: (num: number): boolean => num > 0
  };

  // Composed data cleaning pipeline
  cleanEmailData = pipe(
    this.transformations.trim,
    this.transformations.lowercase,
    (email: string) => {
      if (!this.validators.isEmail(email)) {
        throw new Error(`Invalid email format: ${email}`);
      }
      return email;
    }
  );

  // Composed user name processing
  processUserName = pipe(
    this.transformations.trim,
    this.transformations.capitalize
  );

  // Composed phone number processing
  processPhoneNumber = pipe(
    this.transformations.trim,
    this.transformations.removeSpaces,
    (phone: string) => {
      if (!this.validators.isPhone(phone)) {
        throw new Error(`Invalid phone format: ${phone}`);
      }
      return phone;
    }
  );

  // Complex user processing pipeline
  processUserData = (rawUser: any): User => {
    try {
      return {
        id: rawUser.id,
        name: this.processUserName(rawUser.name || ''),
        email: this.cleanEmailData(rawUser.email || ''),
        phone: this.processPhoneNumber(rawUser.phone || ''),
        isActive: Boolean(rawUser.isActive)
      };
    } catch (error) {
      console.error('User processing error:', error);
      throw error;
    }
  };

  // Batch processing with composition
  processBatchData = <T, R>(
    data: T[],
    processor: (item: T) => R,
    filters: Array<(item: R) => boolean> = []
  ): R[] => {
    const applyFilters = (item: R): boolean => 
      filters.every(filter => filter(item));

    return data
      .map(processor)
      .filter(applyFilters);
  };

  // Usage example
  processUserBatch(rawUsers: any[]): Observable<User[]> {
    return of(rawUsers).pipe(
      map(users => this.processBatchData(
        users,
        this.processUserData,
        [
          user => this.validators.isNotEmpty(user.name),
          user => this.validators.isEmail(user.email)
        ]
      )),
      catchError(error => {
        console.error('Batch processing error:', error);
        return of([]);
      })
    );
  }
}
```

#### **Reactive Composition with RxJS**

```typescript
// 🎯 RxJS Operator Composition
@Injectable({
  providedIn: 'root'
})
export class ReactiveDataService {
  
  // Composed observable transformations
  private readonly dataTransformers = {
    
    // Generic retry with exponential backoff
    retryWithBackoff: <T>(maxRetries: number = 3, baseDelay: number = 1000) =>
      (source: Observable<T>) => source.pipe(
        retryWhen(errors =>
          errors.pipe(
            scan((retryCount, error) => {
              if (retryCount >= maxRetries) {
                throw error;
              }
              return retryCount + 1;
            }, 0),
            delay(baseDelay),
            tap(retryCount => console.log(`Retry attempt ${retryCount}`))
          )
        )
      ),

    // Loading state wrapper
    withLoadingState: <T>() => 
      (source: Observable<T>) => merge(
        of({ loading: true, data: null, error: null }),
        source.pipe(
          map(data => ({ loading: false, data, error: null })),
          catchError(error => of({ loading: false, data: null, error }))
        )
      ),

    // Cache with expiration
    cacheWithExpiry: <T>(expiryMs: number) => {
      let cachedData: T | null = null;
      let cacheTime = 0;
      
      return (source: Observable<T>) => defer(() => {
        const now = Date.now();
        if (cachedData && (now - cacheTime) < expiryMs) {
          return of(cachedData);
        }
        
        return source.pipe(
          tap(data => {
            cachedData = data;
            cacheTime = now;
          })
        );
      });
    }
  };

  // Composed API call pipeline
  createApiPipeline = <T>(
    apiCall: Observable<T>,
    options: {
      useCache?: boolean;
      cacheExpiryMs?: number;
      retryAttempts?: number;
      includeLoadingState?: boolean;
    } = {}
  ) => {
    const {
      useCache = false,
      cacheExpiryMs = 300000, // 5 minutes
      retryAttempts = 3,
      includeLoadingState = false
    } = options;

    let pipeline = apiCall;

    // Apply caching if requested
    if (useCache) {
      pipeline = pipeline.pipe(
        this.dataTransformers.cacheWithExpiry(cacheExpiryMs)
      );
    }

    // Apply retry logic
    pipeline = pipeline.pipe(
      this.dataTransformers.retryWithBackoff(retryAttempts)
    );

    // Apply loading state if requested
    if (includeLoadingState) {
      pipeline = pipeline.pipe(
        this.dataTransformers.withLoadingState()
      );
    }

    return pipeline;
  };

  // Example usage
  getUserData(userId: number): Observable<User> {
    const apiCall = this.http.get<User>(`/api/users/${userId}`);
    
    return this.createApiPipeline(apiCall, {
      useCache: true,
      cacheExpiryMs: 300000,
      retryAttempts: 3
    });
  }

  getUsersWithLoadingState(): Observable<{loading: boolean, data: User[] | null, error: any}> {
    const apiCall = this.http.get<User[]>('/api/users');
    
    return this.createApiPipeline(apiCall, {
      includeLoadingState: true,
      retryAttempts: 2
    });
  }

  constructor(private http: HttpClient) {}
}
```

This comprehensive functional programming foundation sets the stage for building sophisticated Angular applications. In the next sections, we'll explore how these patterns integrate with RxJS and Angular's reactive architecture.

---

<a name="operators"></a>
## ⚡ **PART II: RxJS FUNCTIONAL PATTERNS**

### **5. Operators as Functions**

RxJS operators are pure functions that transform observables. Understanding operators as functions is crucial for mastering reactive programming in Angular.

#### **Understanding Operator Composition**

```typescript
// 🎯 Operators are Higher-Order Functions
// They take observables and return observables

// Custom operator creation
const multiplyBy = (multiplier: number) => 
  <T extends number>(source: Observable<T>): Observable<T> =>
    source.pipe(map(value => (value * multiplier) as T));

const addValue = (addition: number) =>
  <T extends number>(source: Observable<T>): Observable<T> =>
    source.pipe(map(value => (value + addition) as T));

// Usage
const numbers$ = of(1, 2, 3, 4, 5);

const transformed$ = numbers$.pipe(
  multiplyBy(2),    // [2, 4, 6, 8, 10]
  addValue(1)       // [3, 5, 7, 9, 11]
);

// 🎯 Advanced Custom Operators for Angular
const withLoadingSpinner = <T>() =>
  (source: Observable<T>): Observable<T> =>
    source.pipe(
      tap(() => console.log('Loading started...')),
      finalize(() => console.log('Loading completed'))
    );

const retryWithExponentialBackoff = (maxRetries: number = 3) =>
  <T>(source: Observable<T>): Observable<T> =>
    source.pipe(
      retryWhen(errors =>
        errors.pipe(
          scan((retryCount, error) => {
            if (retryCount >= maxRetries) {
              throw error;
            }
            return retryCount + 1;
          }, 0),
          delayWhen(retryCount => timer(Math.pow(2, retryCount) * 1000))
        )
      )
    );

const cacheWithInvalidation = <T>(cacheTime: number = 300000) => {
  let cache: T | null = null;
  let cacheTimestamp = 0;

  return (source: Observable<T>): Observable<T> =>
    defer(() => {
      const now = Date.now();
      if (cache && (now - cacheTimestamp) < cacheTime) {
        return of(cache);
      }

      return source.pipe(
        tap(data => {
          cache = data;
          cacheTimestamp = now;
        })
      );
    });
};
```

#### **Functional Data Transformation Patterns**

```typescript
// 🎯 Advanced Functional Transformation Service
@Injectable({
  providedIn: 'root'
})
export class FunctionalDataService {

  // Higher-order functions for data transformation
  private readonly transformers = {
    
    // Functional filtering with type safety
    filterBy: <T, K extends keyof T>(key: K, predicate: (value: T[K]) => boolean) =>
      (source: Observable<T[]>): Observable<T[]> =>
        source.pipe(
          map(items => items.filter(item => predicate(item[key])))
        ),

    // Functional sorting
    sortBy: <T, K extends keyof T>(key: K, direction: 'asc' | 'desc' = 'asc') =>
      (source: Observable<T[]>): Observable<T[]> =>
        source.pipe(
          map(items => {
            const sorted = [...items].sort((a, b) => {
              const aVal = a[key];
              const bVal = b[key];
              if (aVal < bVal) return direction === 'asc' ? -1 : 1;
              if (aVal > bVal) return direction === 'asc' ? 1 : -1;
              return 0;
            });
            return sorted;
          })
        ),

    // Functional grouping
    groupBy: <T, K extends keyof T>(key: K) =>
      (source: Observable<T[]>): Observable<Record<string, T[]>> =>
        source.pipe(
          map(items => 
            items.reduce((groups, item) => {
              const groupKey = String(item[key]);
              if (!groups[groupKey]) {
                groups[groupKey] = [];
              }
              groups[groupKey].push(item);
              return groups;
            }, {} as Record<string, T[]>)
          )
        ),

    // Functional pagination
    paginate: <T>(page: number, pageSize: number) =>
      (source: Observable<T[]>): Observable<{items: T[], total: number, page: number}> =>
        source.pipe(
          map(items => {
            const start = (page - 1) * pageSize;
            const end = start + pageSize;
            return {
              items: items.slice(start, end),
              total: items.length,
              page
            };
          })
        ),

    // Functional search
    search: <T>(searchTerm: string, searchFields: Array<keyof T>) =>
      (source: Observable<T[]>): Observable<T[]> =>
        source.pipe(
          map(items => {
            if (!searchTerm.trim()) return items;
            
            const term = searchTerm.toLowerCase();
            return items.filter(item =>
              searchFields.some(field => {
                const value = item[field];
                return String(value).toLowerCase().includes(term);
              })
            );
          })
        )
  };

  // Composed data processing pipeline
  createDataPipeline = <T>(
    source: Observable<T[]>,
    operations: {
      filter?: { key: keyof T; predicate: (value: any) => boolean };
      sort?: { key: keyof T; direction?: 'asc' | 'desc' };
      search?: { term: string; fields: Array<keyof T> };
      pagination?: { page: number; pageSize: number };
      groupBy?: keyof T;
    }
  ): Observable<any> => {
    let pipeline = source;

    // Apply filter
    if (operations.filter) {
      pipeline = pipeline.pipe(
        this.transformers.filterBy(operations.filter.key, operations.filter.predicate)
      );
    }

    // Apply search
    if (operations.search) {
      pipeline = pipeline.pipe(
        this.transformers.search(operations.search.term, operations.search.fields)
      );
    }

    // Apply sorting
    if (operations.sort) {
      pipeline = pipeline.pipe(
        this.transformers.sortBy(operations.sort.key, operations.sort.direction)
      );
    }

    // Apply grouping
    if (operations.groupBy) {
      pipeline = pipeline.pipe(
        this.transformers.groupBy(operations.groupBy)
      );
    }

    // Apply pagination
    if (operations.pagination) {
      pipeline = pipeline.pipe(
        this.transformers.paginate(operations.pagination.page, operations.pagination.pageSize)
      );
    }

    return pipeline;
  };

  // Real-world usage example
  processUserData(
    users$: Observable<User[]>,
    filters: {
      activeOnly?: boolean;
      searchTerm?: string;
      sortBy?: keyof User;
      page?: number;
      pageSize?: number;
    }
  ): Observable<{items: User[], total: number, page: number}> {
    
    return this.createDataPipeline(users$, {
      filter: filters.activeOnly ? {
        key: 'isActive',
        predicate: (isActive: boolean) => isActive
      } : undefined,
      
      search: filters.searchTerm ? {
        term: filters.searchTerm,
        fields: ['name', 'email']
      } : undefined,
      
      sort: filters.sortBy ? {
        key: filters.sortBy,
        direction: 'asc'
      } : undefined,
      
      pagination: filters.page && filters.pageSize ? {
        page: filters.page,
        pageSize: filters.pageSize
      } : undefined
    });
  }
}
```

<a name="pipe-composition"></a>
### **6. Pipe Composition**

RxJS pipe operators enable functional composition patterns that create readable and maintainable data transformation pipelines.

#### **Advanced Pipe Composition Patterns**

```typescript
// 🎯 Complex API Integration with Functional Composition
@Injectable({
  providedIn: 'root'
})
export class ApiCompositionService {

  // Reusable pipe compositions
  private readonly apiPipes = {
    
    // Standard API response handling
    handleApiResponse: <T>() => pipe(
      tap(() => console.log('API call initiated')),
      map((response: any) => response.data),
      shareReplay(1),
      catchError((error: any) => {
        console.error('API Error:', error);
        return throwError(() => new Error(`API call failed: ${error.message}`));
      })
    ),

    // Optimistic update pattern
    optimisticUpdate: <T>(optimisticValue: T, rollbackValue: T) => pipe(
      startWith(optimisticValue),
      catchError(() => of(rollbackValue))
    ),

    // Debounced search with cancellation
    debouncedSearch: (debounceMs: number = 300) => pipe(
      debounceTime(debounceMs),
      distinctUntilChanged(),
      switchMap((searchTerm: string) => 
        searchTerm.length > 2 
          ? this.searchApi(searchTerm)
          : of([])
      )
    ),

    // Polling with exponential backoff
    pollWithBackoff: <T>(intervalMs: number = 5000, maxInterval: number = 30000) => {
      let currentInterval = intervalMs;
      
      return pipe(
        repeatWhen((completed: Observable<any>) =>
          completed.pipe(
            delay(currentInterval),
            tap(() => {
              currentInterval = Math.min(currentInterval * 1.5, maxInterval);
            })
          )
        ),
        tap(() => {
          currentInterval = intervalMs; // Reset on success
        })
      );
    },

    // Rate limiting
    rateLimit: <T>(requestsPerSecond: number) => {
      const interval = 1000 / requestsPerSecond;
      return pipe(
        concatMap((value: T) => 
          of(value).pipe(delay(interval))
        )
      );
    }
  };

  // Complex user data management pipeline
  createUserManagementPipeline(): {
    users$: Observable<User[]>,
    searchUsers: (term: string) => Observable<User[]>,
    updateUser: (user: User) => Observable<User>,
    deleteUser: (id: number) => Observable<boolean>
  } {
    
    // Base users observable with caching and error handling
    const users$ = this.http.get<User[]>('/api/users').pipe(
      this.apiPipes.handleApiResponse<User[]>(),
      cacheWithInvalidation(300000), // 5-minute cache
      withLoadingSpinner(),
      retryWithExponentialBackoff(3)
    );

    // Search users with debouncing and cancellation
    const searchUsers = (term: string): Observable<User[]> =>
      of(term).pipe(
        this.apiPipes.debouncedSearch(300),
        shareReplay(1)
      );

    // Optimistic user updates
    const updateUser = (user: User): Observable<User> => {
      const optimisticUpdate$ = of(user);
      const apiUpdate$ = this.http.put<User>(`/api/users/${user.id}`, user).pipe(
        this.apiPipes.handleApiResponse<User>()
      );

      return apiUpdate$.pipe(
        this.apiPipes.optimisticUpdate(user, user), // Use same user as rollback
        catchError(error => {
          console.error('Update failed, rolling back:', error);
          return throwError(() => error);
        })
      );
    };

    // User deletion with confirmation
    const deleteUser = (id: number): Observable<boolean> =>
      this.http.delete(`/api/users/${id}`).pipe(
        map(() => true),
        this.apiPipes.handleApiResponse<boolean>(),
        catchError(() => of(false))
      );

    return { users$, searchUsers, updateUser, deleteUser };
  }

  // Real-time data synchronization pipeline
  createRealtimePipeline<T>(
    apiEndpoint: string,
    websocketEndpoint: string
  ): Observable<T[]> {
    
    // Initial data load
    const initialData$ = this.http.get<T[]>(apiEndpoint).pipe(
      this.apiPipes.handleApiResponse<T[]>()
    );

    // WebSocket updates
    const realtimeUpdates$ = this.websocketService.connect(websocketEndpoint).pipe(
      filter((message: any) => message.type === 'update'),
      map((message: any) => message.data),
      scan((acc: T[], update: any) => {
        // Apply update to existing data
        const index = acc.findIndex((item: any) => item.id === update.id);
        if (index >= 0) {
          return [...acc.slice(0, index), update, ...acc.slice(index + 1)];
        } else {
          return [...acc, update];
        }
      }, [] as T[])
    );

    // Combine initial load with real-time updates
    return merge(initialData$, realtimeUpdates$).pipe(
      scan((acc: T[], newData: T[]) => newData, [] as T[]),
      distinctUntilChanged((a, b) => JSON.stringify(a) === JSON.stringify(b)),
      shareReplay(1)
    );
  }

  private searchApi(term: string): Observable<User[]> {
    return this.http.get<User[]>(`/api/search?q=${encodeURIComponent(term)}`).pipe(
      this.apiPipes.handleApiResponse<User[]>()
    );
  }

  constructor(
    private http: HttpClient,
    private websocketService: WebSocketService
  ) {}
}
```

#### **Functional Form Handling with RxJS**

```typescript
// 🎯 Reactive Form Composition
@Injectable({
  providedIn: 'root'
})
export class FunctionalFormService {

  // Form validation pipe compositions
  private readonly validationPipes = {
    
    // Email validation
    validateEmail: pipe(
      map((email: string) => ({
        value: email,
        isValid: /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email),
        errors: /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email) ? [] : ['Invalid email format']
      })),
      distinctUntilChanged((a, b) => a.isValid === b.isValid && a.value === b.value)
    ),

    // Required field validation
    validateRequired: (fieldName: string) => pipe(
      map((value: string) => ({
        value,
        isValid: value.trim().length > 0,
        errors: value.trim().length > 0 ? [] : [`${fieldName} is required`]
      }))
    ),

    // Min length validation
    validateMinLength: (minLength: number, fieldName: string) => pipe(
      map((value: string) => ({
        value,
        isValid: value.length >= minLength,
        errors: value.length >= minLength ? [] : [`${fieldName} must be at least ${minLength} characters`]
      }))
    ),

    // Async unique validation
    validateUnique: <T>(
      checkUniqueService: (value: T) => Observable<boolean>,
      fieldName: string
    ) => pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap((value: T) =>
        checkUniqueService(value).pipe(
          map(isUnique => ({
            value,
            isValid: isUnique,
            errors: isUnique ? [] : [`${fieldName} already exists`]
          })),
          catchError(() => of({
            value,
            isValid: false,
            errors: [`Unable to verify ${fieldName} uniqueness`]
          }))
        )
      )
    )
  };

  // Complex form state management
  createFormStatePipeline<T extends Record<string, any>>(
    initialValues: T,
    validators: Partial<Record<keyof T, Array<(value: any) => Observable<ValidationResult>>>>
  ): {
    formState$: Observable<FormState<T>>,
    updateField: (field: keyof T, value: any) => void,
    submitForm: () => Observable<T>,
    resetForm: () => void
  } {
    
    const formUpdates$ = new Subject<{ field: keyof T; value: any }>();
    const formSubmission$ = new Subject<void>();
    const formReset$ = new Subject<void>();

    // Form state pipeline
    const formState$ = merge(
      of({ type: 'init', payload: initialValues }),
      formUpdates$.pipe(map(update => ({ type: 'update', payload: update }))),
      formReset$.pipe(map(() => ({ type: 'reset', payload: initialValues })))
    ).pipe(
      scan((state: FormState<T>, action: any) => {
        switch (action.type) {
          case 'init':
          case 'reset':
            return {
              values: action.payload,
              errors: {} as Record<keyof T, string[]>,
              isValid: false,
              isSubmitting: false,
              isDirty: false
            };
          
          case 'update':
            const { field, value } = action.payload;
            const newValues = { ...state.values, [field]: value };
            
            return {
              ...state,
              values: newValues,
              isDirty: true
            };
          
          default:
            return state;
        }
      }, {
        values: initialValues,
        errors: {} as Record<keyof T, string[]>,
        isValid: false,
        isSubmitting: false,
        isDirty: false
      } as FormState<T>),
      
      // Apply validations
      switchMap(state => {
        const validationStreams = Object.keys(validators).map(field => {
          const fieldValidators = validators[field as keyof T] || [];
          const fieldValue = state.values[field as keyof T];
          
          return combineLatest(
            fieldValidators.map(validator => validator(fieldValue))
          ).pipe(
            map(results => ({
              field: field as keyof T,
              errors: results.flatMap(result => result.errors)
            }))
          );
        });
        
        if (validationStreams.length === 0) {
          return of({ ...state, isValid: true });
        }
        
        return combineLatest(validationStreams).pipe(
          map(fieldValidations => {
            const errors = fieldValidations.reduce(
              (acc, { field, errors }) => ({ ...acc, [field]: errors }),
              {} as Record<keyof T, string[]>
            );
            
            const isValid = Object.values(errors).every(
              (fieldErrors: string[]) => fieldErrors.length === 0
            );
            
            return { ...state, errors, isValid };
          })
        );
      }),
      
      shareReplay(1)
    );

    // Form submission pipeline
    const submitForm = (): Observable<T> =>
      formState$.pipe(
        take(1),
        filter(state => state.isValid && !state.isSubmitting),
        map(state => state.values),
        tap(() => formUpdates$.next({ field: 'isSubmitting' as keyof T, value: true })),
        // Here you would typically make an API call
        delay(1000), // Simulate API call
        tap(() => formUpdates$.next({ field: 'isSubmitting' as keyof T, value: false }))
      );

    return {
      formState$,
      updateField: (field: keyof T, value: any) => formUpdates$.next({ field, value }),
      submitForm,
      resetForm: () => formReset$.next()
    };
  }

  // Usage example for user registration form
  createUserRegistrationForm(): Observable<FormState<UserRegistrationData>> {
    const initialValues: UserRegistrationData = {
      email: '',
      username: '',
      password: '',
      confirmPassword: ''
    };

    const validators = {
      email: [
        (value: string) => of(value).pipe(this.validationPipes.validateEmail),
        (value: string) => of(value).pipe(
          this.validationPipes.validateUnique(
            this.checkEmailUnique.bind(this),
            'Email'
          )
        )
      ],
      username: [
        (value: string) => of(value).pipe(this.validationPipes.validateRequired('Username')),
        (value: string) => of(value).pipe(this.validationPipes.validateMinLength(3, 'Username'))
      ],
      password: [
        (value: string) => of(value).pipe(this.validationPipes.validateRequired('Password')),
        (value: string) => of(value).pipe(this.validationPipes.validateMinLength(8, 'Password'))
      ]
    };

    const { formState$ } = this.createFormStatePipeline(initialValues, validators);
    return formState$;
  }

  private checkEmailUnique(email: string): Observable<boolean> {
    return this.http.get<{exists: boolean}>(`/api/check-email/${email}`).pipe(
      map(response => !response.exists),
      catchError(() => of(false))
    );
  }

  constructor(private http: HttpClient) {}
}

interface ValidationResult {
  value: any;
  isValid: boolean;
  errors: string[];
}

interface FormState<T> {
  values: T;
  errors: Record<keyof T, string[]>;
  isValid: boolean;
  isSubmitting: boolean;
  isDirty: boolean;
}

interface UserRegistrationData {
  email: string;
  username: string;
  password: string;
  confirmPassword: string;
}
```

<a name="reactive-fp"></a>
### **7. Reactive Functional Programming**

Reactive functional programming combines the predictability of functional programming with the power of reactive streams.

#### **Advanced Reactive Patterns**

```typescript
// 🎯 Reactive State Management with Functional Patterns
@Injectable({
  providedIn: 'root'
})
export class ReactiveStateService<T> {
  
  private state$ = new BehaviorSubject<T>(this.initialState);
  private actionStream$ = new Subject<StateAction<T>>();

  constructor(private initialState: T) {
    // Functional state reducer pipeline
    this.actionStream$.pipe(
      scan((currentState: T, action: StateAction<T>) => 
        this.reduceState(currentState, action)
      , this.initialState),
      distinctUntilChanged((a, b) => JSON.stringify(a) === JSON.stringify(b)),
      tap(newState => this.state$.next(newState))
    ).subscribe();
  }

  // Pure state reducer function
  private reduceState(state: T, action: StateAction<T>): T {
    return action.reducer(state, action.payload);
  }

  // Public methods for state access and updates
  getState(): Observable<T> {
    return this.state$.asObservable();
  }

  dispatch(action: StateAction<T>): void {
    this.actionStream$.next(action);
  }

  // Functional selectors
  select<K>(selector: (state: T) => K): Observable<K> {
    return this.getState().pipe(
      map(selector),
      distinctUntilChanged()
    );
  }

  // Async action handling
  dispatchAsync<P>(
    asyncAction: (payload: P) => Observable<StateAction<T>>
  ): (payload: P) => Observable<T> {
    return (payload: P) => 
      asyncAction(payload).pipe(
        tap(action => this.dispatch(action)),
        switchMap(() => this.getState().pipe(take(1)))
      );
  }
}

// State action interface
interface StateAction<T> {
  type: string;
  payload?: any;
  reducer: (state: T, payload?: any) => T;
}

// Example: Todo List State Management
interface TodoState {
  todos: Todo[];
  filter: 'all' | 'active' | 'completed';
  loading: boolean;
}

interface Todo {
  id: number;
  text: string;
  completed: boolean;
  createdAt: Date;
}

@Injectable({
  providedIn: 'root'
})
export class TodoStateService {
  private stateService = new ReactiveStateService<TodoState>({
    todos: [],
    filter: 'all',
    loading: false
  });

  // Action creators with pure reducers
  private actions = {
    addTodo: (text: string): StateAction<TodoState> => ({
      type: 'ADD_TODO',
      payload: { text },
      reducer: (state, { text }) => ({
        ...state,
        todos: [
          ...state.todos,
          {
            id: Date.now(),
            text,
            completed: false,
            createdAt: new Date()
          }
        ]
      })
    }),

    toggleTodo: (id: number): StateAction<TodoState> => ({
      type: 'TOGGLE_TODO',
      payload: { id },
      reducer: (state, { id }) => ({
        ...state,
        todos: state.todos.map(todo =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
      })
    }),

    removeTodo: (id: number): StateAction<TodoState> => ({
      type: 'REMOVE_TODO',
      payload: { id },
      reducer: (state, { id }) => ({
        ...state,
        todos: state.todos.filter(todo => todo.id !== id)
      })
    }),

    setFilter: (filter: 'all' | 'active' | 'completed'): StateAction<TodoState> => ({
      type: 'SET_FILTER',
      payload: { filter },
      reducer: (state, { filter }) => ({
        ...state,
        filter
      })
    }),

    setLoading: (loading: boolean): StateAction<TodoState> => ({
      type: 'SET_LOADING',
      payload: { loading },
      reducer: (state, { loading }) => ({
        ...state,
        loading
      })
    })
  };

  // Functional selectors
  getFilteredTodos(): Observable<Todo[]> {
    return combineLatest([
      this.stateService.select(state => state.todos),
      this.stateService.select(state => state.filter)
    ]).pipe(
      map(([todos, filter]) => {
        switch (filter) {
          case 'active':
            return todos.filter(todo => !todo.completed);
          case 'completed':
            return todos.filter(todo => todo.completed);
          default:
            return todos;
        }
      })
    );
  }

  getTodoStats(): Observable<{total: number, completed: number, active: number}> {
    return this.stateService.select(state => state.todos).pipe(
      map(todos => ({
        total: todos.length,
        completed: todos.filter(t => t.completed).length,
        active: todos.filter(t => !t.completed).length
      }))
    );
  }

  // Async actions with side effects
  loadTodosFromApi = this.stateService.dispatchAsync((userId: number) =>
    this.http.get<Todo[]>(`/api/users/${userId}/todos`).pipe(
      map(todos => ({
        type: 'LOAD_TODOS_SUCCESS',
        payload: { todos },
        reducer: (state: TodoState, { todos }) => ({
          ...state,
          todos,
          loading: false
        })
      })),
      startWith(this.actions.setLoading(true)),
      catchError(error => of({
        type: 'LOAD_TODOS_ERROR',
        payload: { error },
        reducer: (state: TodoState) => ({
          ...state,
          loading: false
        })
      }))
    )
  );

  // Public action methods
  addTodo(text: string): void {
    this.stateService.dispatch(this.actions.addTodo(text));
  }

  toggleTodo(id: number): void {
    this.stateService.dispatch(this.actions.toggleTodo(id));
  }

  removeTodo(id: number): void {
    this.stateService.dispatch(this.actions.removeTodo(id));
  }

  setFilter(filter: 'all' | 'active' | 'completed'): void {
    this.stateService.dispatch(this.actions.setFilter(filter));
  }

  constructor(private http: HttpClient) {}
}
```

---

*💡 **Key Takeaway**: RxJS operators are the building blocks of functional reactive programming. By composing operators as pure functions, we create powerful, maintainable data transformation pipelines that make Angular applications more predictable and performant.*

---

<a name="functional-components"></a>
## 🎨 **PART III: ANGULAR FUNCTIONAL PATTERNS**

### **8. Functional Components**

Functional component design leverages pure functions and immutable data patterns to create predictable, testable Angular components.

#### **Pure Component Functions**

```typescript
// 🎯 Functional Component Design Patterns
@Component({
  selector: 'app-user-card',
  template: `
    <div class="user-card" [class.active]="isActive(user)">
      <img [src]="getUserAvatar(user)" [alt]="getUserDisplayName(user)">
      <h3>{{ getUserDisplayName(user) }}</h3>
      <p>{{ getUserTitle(user) }}</p>
      <div class="stats">
        <span>Posts: {{ getUserPostCount(user) }}</span>
        <span>Followers: {{ getFormattedFollowerCount(user) }}</span>
      </div>
      <div class="actions">
        <button 
          *ngFor="let action of getUserActions(user)" 
          [disabled]="!action.enabled"
          (click)="action.handler(user)">
          {{ action.label }}
        </button>
      </div>
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserCardComponent {
  @Input() user!: User;
  @Input() currentUserId?: number;
  @Output() userAction = new EventEmitter<{action: string, user: User}>();

  // Pure functions for data transformation
  getUserDisplayName = (user: User): string => 
    user.displayName || `${user.firstName} ${user.lastName}`.trim() || user.email;

  getUserAvatar = (user: User): string => 
    user.avatar || '/assets/default-avatar.png';

  getUserTitle = (user: User): string => 
    user.title || 'Member';

  getUserPostCount = (user: User): number => 
    user.stats?.posts || 0;

  getFormattedFollowerCount = (user: User): string => {
    const count = user.stats?.followers || 0;
    if (count >= 1000000) return `${(count / 1000000).toFixed(1)}M`;
    if (count >= 1000) return `${(count / 1000).toFixed(1)}K`;
    return count.toString();
  };

  isActive = (user: User): boolean => 
    user.lastSeen ? (Date.now() - user.lastSeen.getTime()) < 300000 : false; // 5 minutes

  // Pure function for determining available actions
  getUserActions = (user: User): UserAction[] => {
    const baseActions: UserAction[] = [
      {
        label: 'View Profile',
        action: 'view',
        enabled: true,
        handler: (user: User) => this.emitAction('view', user)
      }
    ];

    if (this.currentUserId && this.currentUserId !== user.id) {
      baseActions.push({
        label: user.isFollowing ? 'Unfollow' : 'Follow',
        action: user.isFollowing ? 'unfollow' : 'follow',
        enabled: true,
        handler: (user: User) => this.emitAction(
          user.isFollowing ? 'unfollow' : 'follow', 
          user
        )
      });

      if (user.canMessage) {
        baseActions.push({
          label: 'Message',
          action: 'message',
          enabled: this.isActive(user),
          handler: (user: User) => this.emitAction('message', user)
        });
      }
    }

    return baseActions;
  };

  private emitAction(action: string, user: User): void {
    this.userAction.emit({ action, user });
  }
}

interface UserAction {
  label: string;
  action: string;
  enabled: boolean;
  handler: (user: User) => void;
}
```

#### **Functional Component Composition**

```typescript
// 🎯 Higher-Order Component Functions
@Injectable({
  providedIn: 'root'
})
export class ComponentCompositionService {

  // HOC for loading states
  withLoadingState<T>(
    dataStream: Observable<T>,
    loadingTemplate: TemplateRef<any>,
    errorTemplate: TemplateRef<any>
  ): Observable<ComponentState<T>> {
    return dataStream.pipe(
      map(data => ({ loading: false, data, error: null })),
      startWith({ loading: true, data: null, error: null }),
      catchError(error => of({ loading: false, data: null, error }))
    );
  }

  // HOC for pagination
  withPagination<T>(
    dataStream: Observable<T[]>,
    pageSize: number
  ): {
    paginatedData$: Observable<T[]>,
    currentPage$: Observable<number>,
    totalPages$: Observable<number>,
    goToPage: (page: number) => void,
    nextPage: () => void,
    prevPage: () => void
  } {
    const currentPageSubject = new BehaviorSubject(1);
    
    const paginatedData$ = combineLatest([
      dataStream,
      currentPageSubject.asObservable()
    ]).pipe(
      map(([data, page]) => {
        const start = (page - 1) * pageSize;
        const end = start + pageSize;
        return data.slice(start, end);
      })
    );

    const totalPages$ = dataStream.pipe(
      map(data => Math.ceil(data.length / pageSize))
    );

    return {
      paginatedData$,
      currentPage$: currentPageSubject.asObservable(),
      totalPages$,
      goToPage: (page: number) => currentPageSubject.next(page),
      nextPage: () => {
        const current = currentPageSubject.value;
        currentPageSubject.next(current + 1);
      },
      prevPage: () => {
        const current = currentPageSubject.value;
        if (current > 1) currentPageSubject.next(current - 1);
      }
    };
  }

  // HOC for filtering and searching
  withFiltering<T>(
    dataStream: Observable<T[]>,
    searchFields: Array<keyof T>
  ): {
    filteredData$: Observable<T[]>,
    searchTerm$: Observable<string>,
    activeFilters$: Observable<Filter[]>,
    updateSearch: (term: string) => void,
    addFilter: (filter: Filter) => void,
    removeFilter: (filterId: string) => void,
    clearFilters: () => void
  } {
    const searchSubject = new BehaviorSubject('');
    const filtersSubject = new BehaviorSubject<Filter[]>([]);

    const filteredData$ = combineLatest([
      dataStream,
      searchSubject.asObservable(),
      filtersSubject.asObservable()
    ]).pipe(
      map(([data, searchTerm, filters]) => {
        let filtered = data;

        // Apply search
        if (searchTerm.trim()) {
          const term = searchTerm.toLowerCase();
          filtered = filtered.filter(item =>
            searchFields.some(field => {
              const value = item[field];
              return String(value).toLowerCase().includes(term);
            })
          );
        }

        // Apply filters
        filtered = filters.reduce((acc, filter) => {
          return acc.filter(item => filter.predicate(item));
        }, filtered);

        return filtered;
      })
    );

    return {
      filteredData$,
      searchTerm$: searchSubject.asObservable(),
      activeFilters$: filtersSubject.asObservable(),
      updateSearch: (term: string) => searchSubject.next(term),
      addFilter: (filter: Filter) => {
        const current = filtersSubject.value;
        filtersSubject.next([...current, filter]);
      },
      removeFilter: (filterId: string) => {
        const current = filtersSubject.value;
        filtersSubject.next(current.filter(f => f.id !== filterId));
      },
      clearFilters: () => filtersSubject.next([])
    };
  }
}

interface ComponentState<T> {
  loading: boolean;
  data: T | null;
  error: any;
}

interface Filter {
  id: string;
  label: string;
  predicate: (item: any) => boolean;
}

// Usage Example: Smart List Component
@Component({
  selector: 'app-smart-user-list',
  template: `
    <div class="search-controls">
      <input 
        [value]="searchTerm$ | async" 
        (input)="updateSearch($event.target.value)"
        placeholder="Search users...">
      
      <div class="filters">
        <button 
          *ngFor="let filter of availableFilters" 
          [class.active]="isFilterActive(filter.id)"
          (click)="toggleFilter(filter)">
          {{ filter.label }}
        </button>
      </div>
    </div>

    <div class="pagination-controls" *ngIf="(totalPages$ | async) > 1">
      <button (click)="prevPage()" [disabled]="(currentPage$ | async) === 1">
        Previous
      </button>
      <span>Page {{ currentPage$ | async }} of {{ totalPages$ | async }}</span>
      <button (click)="nextPage()" [disabled]="(currentPage$ | async) === (totalPages$ | async)">
        Next
      </button>
    </div>

    <div class="user-list">
      <app-user-card 
        *ngFor="let user of paginatedData$ | async; trackBy: trackByUserId"
        [user]="user"
        [currentUserId]="currentUserId"
        (userAction)="handleUserAction($event)">
      </app-user-card>
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class SmartUserListComponent implements OnInit {
  @Input() currentUserId?: number;

  // Composed functionality
  filteredData$!: Observable<User[]>;
  searchTerm$!: Observable<string>;
  activeFilters$!: Observable<Filter[]>;
  paginatedData$!: Observable<User[]>;
  currentPage$!: Observable<number>;
  totalPages$!: Observable<number>;

  // Component methods
  updateSearch!: (term: string) => void;
  addFilter!: (filter: Filter) => void;
  removeFilter!: (filterId: string) => void;
  goToPage!: (page: number) => void;
  nextPage!: () => void;
  prevPage!: () => void;

  availableFilters: Filter[] = [
    {
      id: 'active',
      label: 'Active Users',
      predicate: (user: User) => user.isActive
    },
    {
      id: 'premium',
      label: 'Premium Users',
      predicate: (user: User) => user.isPremium
    },
    {
      id: 'verified',
      label: 'Verified Users',
      predicate: (user: User) => user.isVerified
    }
  ];

  ngOnInit(): void {
    // Get base data
    const users$ = this.userService.getUsers();

    // Compose filtering functionality
    const filteringComposition = this.compositionService.withFiltering(
      users$,
      ['firstName', 'lastName', 'email', 'title']
    );

    // Compose pagination functionality
    const paginationComposition = this.compositionService.withPagination(
      filteringComposition.filteredData$,
      10
    );

    // Wire up composed functionality
    this.filteredData$ = filteringComposition.filteredData$;
    this.searchTerm$ = filteringComposition.searchTerm$;
    this.activeFilters$ = filteringComposition.activeFilters$;
    this.updateSearch = filteringComposition.updateSearch;
    this.addFilter = filteringComposition.addFilter;
    this.removeFilter = filteringComposition.removeFilter;

    this.paginatedData$ = paginationComposition.paginatedData$;
    this.currentPage$ = paginationComposition.currentPage$;
    this.totalPages$ = paginationComposition.totalPages$;
    this.goToPage = paginationComposition.goToPage;
    this.nextPage = paginationComposition.nextPage;
    this.prevPage = paginationComposition.prevPage;
  }

  isFilterActive(filterId: string): Observable<boolean> {
    return this.activeFilters$.pipe(
      map(filters => filters.some(f => f.id === filterId))
    );
  }

  toggleFilter(filter: Filter): void {
    this.activeFilters$.pipe(take(1)).subscribe(activeFilters => {
      const isActive = activeFilters.some(f => f.id === filter.id);
      if (isActive) {
        this.removeFilter(filter.id);
      } else {
        this.addFilter(filter);
      }
    });
  }

  trackByUserId(index: number, user: User): number {
    return user.id;
  }

  handleUserAction(event: {action: string, user: User}): void {
    // Handle user actions functionally
    console.log('User action:', event);
  }

  constructor(
    private userService: UserService,
    private compositionService: ComponentCompositionService
  ) {}
}
```

<a name="onpush"></a>
### **9. OnPush Change Detection**

OnPush change detection strategy works perfectly with functional programming patterns, providing significant performance benefits.

#### **Immutable Data with OnPush**

```typescript
// 🎯 OnPush Optimization with Immutable Patterns
@Component({
  selector: 'app-optimized-todo-list',
  template: `
    <div class="todo-controls">
      <input 
        #todoInput
        (keyup.enter)="addTodo(todoInput.value); todoInput.value = ''"
        placeholder="Add new todo...">
      
      <div class="filters">
        <button 
          *ngFor="let filter of filters"
          [class.active]="(currentFilter$ | async) === filter"
          (click)="setFilter(filter)">
          {{ filter | titlecase }}
        </button>
      </div>
    </div>

    <div class="todo-stats">
      <ng-container *ngIf="stats$ | async as stats">
        <span>Total: {{ stats.total }}</span>
        <span>Active: {{ stats.active }}</span>
        <span>Completed: {{ stats.completed }}</span>
      </ng-container>
    </div>

    <div class="todo-list">
      <app-todo-item
        *ngFor="let todo of filteredTodos$ | async; trackBy: trackByTodoId"
        [todo]="todo"
        (toggle)="toggleTodo($event)"
        (remove)="removeTodo($event)"
        (update)="updateTodo($event)">
      </app-todo-item>
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class OptimizedTodoListComponent implements OnInit {
  
  // Immutable observables
  filteredTodos$!: Observable<readonly Todo[]>;
  currentFilter$!: Observable<TodoFilter>;
  stats$!: Observable<TodoStats>;

  filters: readonly TodoFilter[] = ['all', 'active', 'completed'] as const;

  ngOnInit(): void {
    // Subscribe to immutable state
    this.filteredTodos$ = this.todoService.getFilteredTodos();
    this.currentFilter$ = this.todoService.getCurrentFilter();
    this.stats$ = this.todoService.getTodoStats();
  }

  // Pure event handlers
  addTodo(text: string): void {
    if (text.trim()) {
      this.todoService.addTodo(text.trim());
    }
  }

  toggleTodo(id: number): void {
    this.todoService.toggleTodo(id);
  }

  removeTodo(id: number): void {
    this.todoService.removeTodo(id);
  }

  updateTodo(update: {id: number, text: string}): void {
    this.todoService.updateTodo(update.id, { text: update.text });
  }

  setFilter(filter: TodoFilter): void {
    this.todoService.setFilter(filter);
  }

  trackByTodoId(index: number, todo: Todo): number {
    return todo.id;
  }

  constructor(private todoService: TodoStateService) {}
}

@Component({
  selector: 'app-todo-item',
  template: `
    <div class="todo-item" [class.completed]="todo.completed">
      <input 
        type="checkbox" 
        [checked]="todo.completed"
        (change)="toggle.emit(todo.id)">
      
      <span 
        *ngIf="!editing"
        class="todo-text"
        (dblclick)="startEditing()">
        {{ todo.text }}
      </span>
      
      <input 
        *ngIf="editing"
        #editInput
        [value]="todo.text"
        (blur)="finishEditing(editInput.value)"
        (keyup.enter)="finishEditing(editInput.value)"
        (keyup.escape)="cancelEditing()">
      
      <button class="remove-btn" (click)="remove.emit(todo.id)">
        ×
      </button>
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class TodoItemComponent {
  @Input() todo!: Todo;
  @Output() toggle = new EventEmitter<number>();
  @Output() remove = new EventEmitter<number>();
  @Output() update = new EventEmitter<{id: number, text: string}>();

  editing = false;

  startEditing(): void {
    this.editing = true;
  }

  finishEditing(newText: string): void {
    if (newText.trim() && newText !== this.todo.text) {
      this.update.emit({ id: this.todo.id, text: newText.trim() });
    }
    this.editing = false;
  }

  cancelEditing(): void {
    this.editing = false;
  }
}

type TodoFilter = 'all' | 'active' | 'completed';

interface TodoStats {
  total: number;
  active: number;
  completed: number;
}
```

#### **Performance Monitoring with OnPush**

```typescript
// 🎯 Performance-Optimized Service with Immutable Updates
@Injectable({
  providedIn: 'root'
})
export class PerformanceOptimizedDataService {
  
  private changeDetectionSubject = new Subject<string>();
  private performanceMetrics$ = new BehaviorSubject<PerformanceMetrics>({
    renderCount: 0,
    lastRenderTime: 0,
    averageRenderTime: 0,
    changeDetectionTriggers: []
  });

  // Immutable data streams
  private dataSubject = new BehaviorSubject<readonly DataItem[]>([]);
  private readonly data$ = this.dataSubject.asObservable();

  constructor() {
    // Monitor change detection performance
    this.changeDetectionSubject.pipe(
      scan((metrics: PerformanceMetrics, trigger: string) => {
        const now = performance.now();
        const renderTime = now - metrics.lastRenderTime;
        
        return {
          renderCount: metrics.renderCount + 1,
          lastRenderTime: now,
          averageRenderTime: metrics.renderCount === 0 
            ? renderTime 
            : (metrics.averageRenderTime * metrics.renderCount + renderTime) / (metrics.renderCount + 1),
          changeDetectionTriggers: [
            ...metrics.changeDetectionTriggers.slice(-9), // Keep last 10
            { trigger, timestamp: now, renderTime }
          ]
        };
      }, this.performanceMetrics$.value)
    ).subscribe(metrics => this.performanceMetrics$.next(metrics));
  }

  // Immutable data operations
  getData(): Observable<readonly DataItem[]> {
    return this.data$;
  }

  addItem(item: Omit<DataItem, 'id'>): void {
    this.triggerChangeDetection('ADD_ITEM');
    
    const currentData = this.dataSubject.value;
    const newItem: DataItem = {
      ...item,
      id: Date.now() + Math.random()
    };
    
    // Immutable update
    const newData = [...currentData, newItem];
    this.dataSubject.next(newData);
  }

  updateItem(id: number, updates: Partial<DataItem>): void {
    this.triggerChangeDetection('UPDATE_ITEM');
    
    const currentData = this.dataSubject.value;
    const newData = currentData.map(item =>
      item.id === id ? { ...item, ...updates } : item
    );
    
    this.dataSubject.next(newData);
  }

  removeItem(id: number): void {
    this.triggerChangeDetection('REMOVE_ITEM');
    
    const currentData = this.dataSubject.value;
    const newData = currentData.filter(item => item.id !== id);
    this.dataSubject.next(newData);
  }

  // Batch operations for better performance
  updateMultipleItems(updates: Array<{id: number, updates: Partial<DataItem>}>): void {
    this.triggerChangeDetection('BATCH_UPDATE');
    
    const currentData = this.dataSubject.value;
    const updateMap = new Map(updates.map(u => [u.id, u.updates]));
    
    const newData = currentData.map(item => {
      const itemUpdates = updateMap.get(item.id);
      return itemUpdates ? { ...item, ...itemUpdates } : item;
    });
    
    this.dataSubject.next(newData);
  }

  // Performance metrics
  getPerformanceMetrics(): Observable<PerformanceMetrics> {
    return this.performanceMetrics$.asObservable();
  }

  private triggerChangeDetection(trigger: string): void {
    this.changeDetectionSubject.next(trigger);
  }
}

interface DataItem {
  id: number;
  name: string;
  value: number;
  isActive: boolean;
  tags: readonly string[];
  metadata: readonly KeyValuePair[];
}

interface KeyValuePair {
  readonly key: string;
  readonly value: string;
}

interface PerformanceMetrics {
  renderCount: number;
  lastRenderTime: number;
  averageRenderTime: number;
  changeDetectionTriggers: ChangeDetectionTrigger[];
}

interface ChangeDetectionTrigger {
  trigger: string;
  timestamp: number;
  renderTime: number;
}
```

<a name="state-management"></a>
### **10. Immutable State Management**

Immutable state management ensures predictable application behavior and optimal performance with OnPush change detection.

#### **Advanced Immutable State Patterns**

```typescript
// 🎯 Enterprise-Level Immutable State Management
@Injectable({
  providedIn: 'root'
})
export class ImmutableStateManager<T> {
  
  private stateSubject: BehaviorSubject<T>;
  private actionLog: StateAction<T>[] = [];
  private undoStack: T[] = [];
  private redoStack: T[] = [];

  constructor(private initialState: T) {
    this.stateSubject = new BehaviorSubject<T>(this.deepFreeze(initialState));
  }

  // Public state access
  getState(): Observable<T> {
    return this.stateSubject.asObservable();
  }

  getCurrentState(): T {
    return this.stateSubject.value;
  }

  // Immutable state updates with history
  updateState(updater: (state: T) => T, actionName?: string): void {
    const currentState = this.getCurrentState();
    const newState = this.deepFreeze(updater(currentState));
    
    // Add to undo stack
    this.undoStack.push(currentState);
    if (this.undoStack.length > 50) { // Limit undo history
      this.undoStack.shift();
    }
    
    // Clear redo stack on new action
    this.redoStack = [];
    
    // Log action
    if (actionName) {
      this.actionLog.push({
        type: actionName,
        timestamp: new Date(),
        previousState: currentState,
        newState
      });
    }
    
    this.stateSubject.next(newState);
  }

  // Undo/Redo functionality
  undo(): boolean {
    if (this.undoStack.length === 0) return false;
    
    const currentState = this.getCurrentState();
    const previousState = this.undoStack.pop()!;
    
    this.redoStack.push(currentState);
    this.stateSubject.next(previousState);
    
    return true;
  }

  redo(): boolean {
    if (this.redoStack.length === 0) return false;
    
    const currentState = this.getCurrentState();
    const nextState = this.redoStack.pop()!;
    
    this.undoStack.push(currentState);
    this.stateSubject.next(nextState);
    
    return true;
  }

  // State selectors
  select<K>(selector: (state: T) => K): Observable<K> {
    return this.getState().pipe(
      map(selector),
      distinctUntilChanged()
    );
  }

  selectMany<K extends keyof T>(...keys: K[]): Observable<Pick<T, K>> {
    return this.getState().pipe(
      map(state => {
        const selected = {} as Pick<T, K>;
        keys.forEach(key => {
          selected[key] = state[key];
        });
        return selected;
      }),
      distinctUntilChanged((a, b) => this.shallowEqual(a, b))
    );
  }

  // Async state updates
  updateStateAsync<P>(
    asyncUpdater: (state: T, payload: P) => Observable<T>,
    payload: P,
    actionName?: string
  ): Observable<T> {
    const currentState = this.getCurrentState();
    
    return asyncUpdater(currentState, payload).pipe(
      tap(newState => {
        this.updateState(() => newState, actionName);
      }),
      take(1)
    );
  }

  // Batch updates
  batchUpdate(updates: Array<(state: T) => T>, actionName?: string): void {
    const finalState = updates.reduce(
      (state, updater) => updater(state),
      this.getCurrentState()
    );
    
    this.updateState(() => finalState, actionName);
  }

  // Development helpers
  getActionLog(): readonly StateAction<T>[] {
    return [...this.actionLog];
  }

  canUndo(): Observable<boolean> {
    return of(this.undoStack.length > 0);
  }

  canRedo(): Observable<boolean> {
    return of(this.redoStack.length > 0);
  }

  // Deep freeze for immutability enforcement
  private deepFreeze<U>(obj: U): U {
    if (obj === null || typeof obj !== 'object') return obj;
    
    Object.freeze(obj);
    
    Object.getOwnPropertyNames(obj).forEach(prop => {
      const value = (obj as any)[prop];
      if (value !== null && typeof value === 'object') {
        this.deepFreeze(value);
      }
    });
    
    return obj;
  }

  private shallowEqual(a: any, b: any): boolean {
    const keysA = Object.keys(a);
    const keysB = Object.keys(b);
    
    if (keysA.length !== keysB.length) return false;
    
    return keysA.every(key => a[key] === b[key]);
  }
}

interface StateAction<T> {
  type: string;
  timestamp: Date;
  previousState: T;
  newState: T;
}

// Application State Example
interface AppState {
  readonly user: User | null;
  readonly settings: AppSettings;
  readonly notifications: readonly Notification[];
  readonly ui: UIState;
}

interface AppSettings {
  readonly theme: 'light' | 'dark';
  readonly language: string;
  readonly autoSave: boolean;
  readonly preferences: readonly UserPreference[];
}

interface UIState {
  readonly loading: boolean;
  readonly sidebarOpen: boolean;
  readonly activeTab: string;
  readonly modals: readonly ModalState[];
}

interface ModalState {
  readonly id: string;
  readonly type: string;
  readonly isOpen: boolean;
  readonly data?: any;
}

@Injectable({
  providedIn: 'root'
})
export class AppStateService {
  
  private stateManager = new ImmutableStateManager<AppState>({
    user: null,
    settings: {
      theme: 'light',
      language: 'en',
      autoSave: true,
      preferences: []
    },
    notifications: [],
    ui: {
      loading: false,
      sidebarOpen: true,
      activeTab: 'dashboard',
      modals: []
    }
  });

  // State selectors
  getUser(): Observable<User | null> {
    return this.stateManager.select(state => state.user);
  }

  getSettings(): Observable<AppSettings> {
    return this.stateManager.select(state => state.settings);
  }

  getNotifications(): Observable<readonly Notification[]> {
    return this.stateManager.select(state => state.notifications);
  }

  getUIState(): Observable<UIState> {
    return this.stateManager.select(state => state.ui);
  }

  // State updaters
  setUser(user: User | null): void {
    this.stateManager.updateState(
      state => ({ ...state, user }),
      'SET_USER'
    );
  }

  updateSettings(settings: Partial<AppSettings>): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        settings: { ...state.settings, ...settings }
      }),
      'UPDATE_SETTINGS'
    );
  }

  addNotification(notification: Omit<Notification, 'id' | 'timestamp'>): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        notifications: [
          ...state.notifications,
          {
            ...notification,
            id: Date.now().toString(),
            timestamp: new Date()
          }
        ]
      }),
      'ADD_NOTIFICATION'
    );
  }

  removeNotification(id: string): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        notifications: state.notifications.filter(n => n.id !== id)
      }),
      'REMOVE_NOTIFICATION'
    );
  }

  toggleSidebar(): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        ui: {
          ...state.ui,
          sidebarOpen: !state.ui.sidebarOpen
        }
      }),
      'TOGGLE_SIDEBAR'
    );
  }

  setActiveTab(tab: string): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        ui: {
          ...state.ui,
          activeTab: tab
        }
      }),
      'SET_ACTIVE_TAB'
    );
  }

  openModal(modal: Omit<ModalState, 'isOpen'>): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        ui: {
          ...state.ui,
          modals: [
            ...state.ui.modals.filter(m => m.id !== modal.id),
            { ...modal, isOpen: true }
          ]
        }
      }),
      'OPEN_MODAL'
    );
  }

  closeModal(id: string): void {
    this.stateManager.updateState(
      state => ({
        ...state,
        ui: {
          ...state.ui,
          modals: state.ui.modals.map(m =>
            m.id === id ? { ...m, isOpen: false } : m
          )
        }
      }),
      'CLOSE_MODAL'
    );
  }

  // Development helpers
  undo(): boolean {
    return this.stateManager.undo();
  }

  redo(): boolean {
    return this.stateManager.redo();
  }

  getActionHistory(): readonly StateAction<AppState>[] {
    return this.stateManager.getActionLog();
  }
}
```

---

*💡 **Key Takeaway**: Angular functional patterns emphasize immutability, pure functions, and reactive programming. OnPush change detection works seamlessly with these patterns, providing both performance benefits and predictable application behavior.*

---

<a name="error-handling"></a>
## 🔧 **PART IV: ADVANCED FUNCTIONAL CONCEPTS**

### **11. Functional Error Handling**

Functional error handling patterns provide robust, composable ways to manage errors without breaking the functional programming paradigm.

#### **Either/Maybe Patterns in Angular**

```typescript
// 🎯 Functional Error Types
abstract class Result<T, E = Error> {
  abstract isSuccess(): boolean;
  abstract isFailure(): boolean;
  
  static success<T, E = Error>(value: T): Result<T, E> {
    return new Success(value);
  }
  
  static failure<T, E = Error>(error: E): Result<T, E> {
    return new Failure(error);
  }

  // Functor: map over the success value
  abstract map<U>(f: (value: T) => U): Result<U, E>;
  
  // Monad: chain operations that might fail
  abstract flatMap<U>(f: (value: T) => Result<U, E>): Result<U, E>;
  
  // Error mapping
  abstract mapError<F>(f: (error: E) => F): Result<T, F>;
  
  // Pattern matching
  abstract match<U>(
    onSuccess: (value: T) => U,
    onFailure: (error: E) => U
  ): U;
  
  // Tap operations (for side effects)
  abstract tapSuccess(f: (value: T) => void): Result<T, E>;
  abstract tapFailure(f: (error: E) => void): Result<T, E>;
  
  // Convert to observable
  abstract toObservable(): Observable<T>;
}

class Success<T, E = Error> extends Result<T, E> {
  constructor(private value: T) {
    super();
  }

  isSuccess(): boolean {
    return true;
  }

  isFailure(): boolean {
    return false;
  }

  map<U>(f: (value: T) => U): Result<U, E> {
    try {
      return Result.success(f(this.value));
    } catch (error) {
      return Result.failure(error as E);
    }
  }

  flatMap<U>(f: (value: T) => Result<U, E>): Result<U, E> {
    try {
      return f(this.value);
    } catch (error) {
      return Result.failure(error as E);
    }
  }

  mapError<F>(_f: (error: E) => F): Result<T, F> {
    return Result.success(this.value);
  }

  match<U>(onSuccess: (value: T) => U, _onFailure: (error: E) => U): U {
    return onSuccess(this.value);
  }

  tapSuccess(f: (value: T) => void): Result<T, E> {
    f(this.value);
    return this;
  }

  tapFailure(_f: (error: E) => void): Result<T, E> {
    return this;
  }

  toObservable(): Observable<T> {
    return of(this.value);
  }

  getValue(): T {
    return this.value;
  }
}

class Failure<T, E = Error> extends Result<T, E> {
  constructor(private error: E) {
    super();
  }

  isSuccess(): boolean {
    return false;
  }

  isFailure(): boolean {
    return true;
  }

  map<U>(_f: (value: T) => U): Result<U, E> {
    return Result.failure(this.error);
  }

  flatMap<U>(_f: (value: T) => Result<U, E>): Result<U, E> {
    return Result.failure(this.error);
  }

  mapError<F>(f: (error: E) => F): Result<T, F> {
    return Result.failure(f(this.error));
  }

  match<U>(_onSuccess: (value: T) => U, onFailure: (error: E) => U): U {
    return onFailure(this.error);
  }

  tapSuccess(_f: (value: T) => void): Result<T, E> {
    return this;
  }

  tapFailure(f: (error: E) => void): Result<T, E> {
    f(this.error);
    return this;
  }

  toObservable(): Observable<T> {
    return throwError(() => this.error);
  }

  getError(): E {
    return this.error;
  }
}

// Maybe type for null safety
abstract class Maybe<T> {
  abstract isSome(): boolean;
  abstract isNone(): boolean;
  
  static some<T>(value: T): Maybe<T> {
    return new Some(value);
  }
  
  static none<T>(): Maybe<T> {
    return new None<T>();
  }
  
  static fromNullable<T>(value: T | null | undefined): Maybe<T> {
    return value != null ? Maybe.some(value) : Maybe.none();
  }

  abstract map<U>(f: (value: T) => U): Maybe<U>;
  abstract flatMap<U>(f: (value: T) => Maybe<U>): Maybe<U>;
  abstract filter(predicate: (value: T) => boolean): Maybe<T>;
  abstract getOrElse(defaultValue: T): T;
  abstract getOrElseGet(defaultValueSupplier: () => T): T;
  abstract toObservable(): Observable<T>;
}

class Some<T> extends Maybe<T> {
  constructor(private value: T) {
    super();
  }

  isSome(): boolean {
    return true;
  }

  isNone(): boolean {
    return false;
  }

  map<U>(f: (value: T) => U): Maybe<U> {
    try {
      const result = f(this.value);
      return result != null ? Maybe.some(result) : Maybe.none();
    } catch {
      return Maybe.none();
    }
  }

  flatMap<U>(f: (value: T) => Maybe<U>): Maybe<U> {
    try {
      return f(this.value);
    } catch {
      return Maybe.none();
    }
  }

  filter(predicate: (value: T) => boolean): Maybe<T> {
    try {
      return predicate(this.value) ? this : Maybe.none();
    } catch {
      return Maybe.none();
    }
  }

  getOrElse(_defaultValue: T): T {
    return this.value;
  }

  getOrElseGet(_defaultValueSupplier: () => T): T {
    return this.value;
  }

  toObservable(): Observable<T> {
    return of(this.value);
  }

  getValue(): T {
    return this.value;
  }
}

class None<T> extends Maybe<T> {
  isSome(): boolean {
    return false;
  }

  isNone(): boolean {
    return true;
  }

  map<U>(_f: (value: T) => U): Maybe<U> {
    return Maybe.none();
  }

  flatMap<U>(_f: (value: T) => Maybe<U>): Maybe<U> {
    return Maybe.none();
  }

  filter(_predicate: (value: T) => boolean): Maybe<T> {
    return this;
  }

  getOrElse(defaultValue: T): T {
    return defaultValue;
  }

  getOrElseGet(defaultValueSupplier: () => T): T {
    return defaultValueSupplier();
  }

  toObservable(): Observable<T> {
    return EMPTY;
  }
}
```

#### **Angular Service with Functional Error Handling**

```typescript
// 🎯 Functional Error Handling in Angular Services
@Injectable({
  providedIn: 'root'
})
export class FunctionalUserService {

  // Functional API call wrapper
  private apiCall<T>(
    request: Observable<T>,
    context: string
  ): Observable<Result<T, ApiError>> {
    return request.pipe(
      map(data => Result.success(data)),
      catchError((error: any) => {
        const apiError: ApiError = {
          message: error.message || 'Unknown error',
          code: error.status || 500,
          context,
          timestamp: new Date()
        };
        return of(Result.failure(apiError));
      })
    );
  }

  // Get user with functional error handling
  getUser(id: number): Observable<Result<User, ApiError>> {
    return this.apiCall(
      this.http.get<User>(`/api/users/${id}`),
      `getUser(${id})`
    );
  }

  // Create user with validation
  createUser(userData: CreateUserRequest): Observable<Result<User, ValidationError | ApiError>> {
    return this.validateUserData(userData)
      .flatMap(validData => 
        this.apiCall(
          this.http.post<User>('/api/users', validData),
          'createUser'
        )
      );
  }

  // Update user with optimistic updates
  updateUser(id: number, updates: Partial<User>): Observable<Result<User, ApiError>> {
    return this.getUser(id).pipe(
      switchMap(userResult => 
        userResult.match(
          // Success: proceed with update
          user => this.apiCall(
            this.http.put<User>(`/api/users/${id}`, { ...user, ...updates }),
            `updateUser(${id})`
          ),
          // Failure: propagate error
          error => of(Result.failure(error))
        )
      )
    );
  }

  // Search users with Maybe for nullable results
  searchUsers(query: string): Observable<Maybe<User[]>> {
    if (!query.trim()) {
      return of(Maybe.none());
    }

    return this.http.get<User[]>(`/api/users/search?q=${encodeURIComponent(query)}`).pipe(
      map(users => users.length > 0 ? Maybe.some(users) : Maybe.none()),
      catchError(() => of(Maybe.none()))
    );
  }

  // Compose multiple operations with error handling
  getUserWithProjects(userId: number): Observable<Result<UserWithProjects, ApiError>> {
    return this.getUser(userId).pipe(
      switchMap(userResult =>
        userResult.match(
          user => this.getUserProjects(userId).pipe(
            map(projectsResult =>
              projectsResult.map(projects => ({
                user,
                projects
              }))
            )
          ),
          error => of(Result.failure(error))
        )
      )
    );
  }

  private getUserProjects(userId: number): Observable<Result<Project[], ApiError>> {
    return this.apiCall(
      this.http.get<Project[]>(`/api/users/${userId}/projects`),
      `getUserProjects(${userId})`
    );
  }

  private validateUserData(userData: CreateUserRequest): Result<CreateUserRequest, ValidationError> {
    const errors: string[] = [];

    if (!userData.email?.trim()) {
      errors.push('Email is required');
    } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(userData.email)) {
      errors.push('Invalid email format');
    }

    if (!userData.name?.trim()) {
      errors.push('Name is required');
    }

    if (userData.age !== undefined && userData.age < 0) {
      errors.push('Age must be a positive number');
    }

    if (errors.length > 0) {
      return Result.failure({
        message: 'Validation failed',
        errors,
        field: 'userData'
      });
    }

    return Result.success(userData);
  }

  constructor(private http: HttpClient) {}
}

interface ApiError {
  message: string;
  code: number;
  context: string;
  timestamp: Date;
}

interface ValidationError {
  message: string;
  errors: string[];
  field: string;
}

interface CreateUserRequest {
  email: string;
  name: string;
  age?: number;
}

interface UserWithProjects {
  user: User;
  projects: Project[];
}

interface Project {
  id: number;
  name: string;
  description: string;
}
```

<a name="railway"></a>
### **12. Railway-Oriented Programming**

Railway-oriented programming provides a visual metaphor for handling success and failure paths in a functional way.

#### **Railway Pattern Implementation**

```typescript
// 🎯 Railway-Oriented Programming for Angular
class Railway<T, E = Error> {
  constructor(
    private value: T | E,
    private isSuccess: boolean
  ) {}

  static success<T, E = Error>(value: T): Railway<T, E> {
    return new Railway<T, E>(value, true);
  }

  static failure<T, E = Error>(error: E): Railway<T, E> {
    return new Railway<T, E>(error, false);
  }

  // Bind (>>=) - railway switching function
  bind<U>(f: (value: T) => Railway<U, E>): Railway<U, E> {
    if (!this.isSuccess) {
      return Railway.failure(this.value as E);
    }
    
    try {
      return f(this.value as T);
    } catch (error) {
      return Railway.failure(error as E);
    }
  }

  // Map - transform success values
  map<U>(f: (value: T) => U): Railway<U, E> {
    if (!this.isSuccess) {
      return Railway.failure(this.value as E);
    }
    
    try {
      return Railway.success(f(this.value as T));
    } catch (error) {
      return Railway.failure(error as E);
    }
  }

  // Tee - side effects on success
  tee(f: (value: T) => void): Railway<T, E> {
    if (this.isSuccess) {
      try {
        f(this.value as T);
      } catch (error) {
        return Railway.failure(error as E);
      }
    }
    return this;
  }

  // Try/Catch wrapper
  static tryCatch<T, E = Error>(f: () => T): Railway<T, E> {
    try {
      return Railway.success(f());
    } catch (error) {
      return Railway.failure(error as E);
    }
  }

  // Async operations
  static async asyncTryCatch<T, E = Error>(f: () => Promise<T>): Promise<Railway<T, E>> {
    try {
      const result = await f();
      return Railway.success(result);
    } catch (error) {
      return Railway.failure(error as E);
    }
  }

  // Extract values
  match<U>(onSuccess: (value: T) => U, onFailure: (error: E) => U): U {
    return this.isSuccess 
      ? onSuccess(this.value as T)
      : onFailure(this.value as E);
  }

  getValueOrThrow(): T {
    if (this.isSuccess) {
      return this.value as T;
    }
    throw this.value;
  }

  getValueOrDefault(defaultValue: T): T {
    return this.isSuccess ? this.value as T : defaultValue;
  }

  toObservable(): Observable<T> {
    return this.isSuccess 
      ? of(this.value as T)
      : throwError(() => this.value);
  }
}

// Railway operators for complex flows
const railwayOperators = {
  // Compose multiple railway functions
  compose: <A, B, C, E>(
    f: (a: A) => Railway<B, E>,
    g: (b: B) => Railway<C, E>
  ) => (a: A): Railway<C, E> => f(a).bind(g),

  // Apply function to railway value
  apply: <T, U, E>(
    railwayFunc: Railway<(value: T) => U, E>
  ) => (railwayValue: Railway<T, E>): Railway<U, E> =>
    railwayFunc.bind(f => railwayValue.map(f)),

  // Lift regular function to railway
  lift: <T, U, E>(f: (value: T) => U) =>
    (railway: Railway<T, E>): Railway<U, E> => railway.map(f),

  // Combine multiple railways
  combine: <T extends readonly unknown[], E>(
    railways: { [K in keyof T]: Railway<T[K], E> }
  ): Railway<T, E> => {
    const results: unknown[] = [];
    
    for (const railway of railways) {
      const result = railway.match(
        value => ({ success: true, value }),
        error => ({ success: false, error })
      );
      
      if (!result.success) {
        return Railway.failure(result.error as E);
      }
      
      results.push(result.value);
    }
    
    return Railway.success(results as T);
  }
};
```

#### **Railway Pattern in Angular Form Processing**

```typescript
// 🎯 Form Processing with Railway Pattern
@Injectable({
  providedIn: 'root'
})
export class RailwayFormService {

  // User registration pipeline
  processUserRegistration(
    formData: RegistrationFormData
  ): Observable<Railway<RegistrationResult, RegistrationError>> {
    
    return from(this.runRegistrationPipeline(formData));
  }

  private async runRegistrationPipeline(
    formData: RegistrationFormData
  ): Promise<Railway<RegistrationResult, RegistrationError>> {
    
    return Railway.success(formData)
      .bind(this.validateBasicInfo)
      .bind(this.validatePassword)
      .bind(this.checkEmailUniqueness.bind(this))
      .bind(this.checkUsernameUniqueness.bind(this))
      .bind(this.createUserAccount.bind(this))
      .bind(this.sendWelcomeEmail.bind(this))
      .tee(this.logSuccessfulRegistration)
      .map(this.createRegistrationResult);
  }

  // Validation steps
  private validateBasicInfo = (
    data: RegistrationFormData
  ): Railway<RegistrationFormData, RegistrationError> => {
    const errors: string[] = [];

    if (!data.email?.trim()) {
      errors.push('Email is required');
    } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(data.email)) {
      errors.push('Invalid email format');
    }

    if (!data.username?.trim()) {
      errors.push('Username is required');
    } else if (data.username.length < 3) {
      errors.push('Username must be at least 3 characters');
    }

    if (!data.firstName?.trim()) {
      errors.push('First name is required');
    }

    if (!data.lastName?.trim()) {
      errors.push('Last name is required');
    }

    if (errors.length > 0) {
      return Railway.failure({
        type: 'VALIDATION_ERROR',
        message: 'Basic validation failed',
        errors,
        step: 'basicInfo'
      });
    }

    return Railway.success(data);
  };

  private validatePassword = (
    data: RegistrationFormData
  ): Railway<RegistrationFormData, RegistrationError> => {
    const errors: string[] = [];

    if (!data.password) {
      errors.push('Password is required');
    } else {
      if (data.password.length < 8) {
        errors.push('Password must be at least 8 characters');
      }
      if (!/[A-Z]/.test(data.password)) {
        errors.push('Password must contain at least one uppercase letter');
      }
      if (!/[a-z]/.test(data.password)) {
        errors.push('Password must contain at least one lowercase letter');
      }
      if (!/\d/.test(data.password)) {
        errors.push('Password must contain at least one number');
      }
    }

    if (data.password !== data.confirmPassword) {
      errors.push('Passwords do not match');
    }

    if (errors.length > 0) {
      return Railway.failure({
        type: 'VALIDATION_ERROR',
        message: 'Password validation failed',
        errors,
        step: 'password'
      });
    }

    return Railway.success(data);
  };

  private async checkEmailUniqueness(
    data: RegistrationFormData
  ): Promise<Railway<RegistrationFormData, RegistrationError>> {
    try {
      const exists = await this.http.get<{exists: boolean}>(
        `/api/check-email/${encodeURIComponent(data.email)}`
      ).toPromise();

      if (exists?.exists) {
        return Railway.failure({
          type: 'UNIQUENESS_ERROR',
          message: 'Email already exists',
          errors: ['This email is already registered'],
          step: 'emailUniqueness'
        });
      }

      return Railway.success(data);
    } catch (error) {
      return Railway.failure({
        type: 'API_ERROR',
        message: 'Failed to check email uniqueness',
        errors: ['Unable to verify email uniqueness'],
        step: 'emailUniqueness'
      });
    }
  }

  private async checkUsernameUniqueness(
    data: RegistrationFormData
  ): Promise<Railway<RegistrationFormData, RegistrationError>> {
    try {
      const exists = await this.http.get<{exists: boolean}>(
        `/api/check-username/${encodeURIComponent(data.username)}`
      ).toPromise();

      if (exists?.exists) {
        return Railway.failure({
          type: 'UNIQUENESS_ERROR',
          message: 'Username already exists',
          errors: ['This username is already taken'],
          step: 'usernameUniqueness'
        });
      }

      return Railway.success(data);
    } catch (error) {
      return Railway.failure({
        type: 'API_ERROR',
        message: 'Failed to check username uniqueness',
        errors: ['Unable to verify username uniqueness'],
        step: 'usernameUniqueness'
      });
    }
  }

  private async createUserAccount(
    data: RegistrationFormData
  ): Promise<Railway<User, RegistrationError>> {
    try {
      const user = await this.http.post<User>('/api/users', {
        email: data.email,
        username: data.username,
        firstName: data.firstName,
        lastName: data.lastName,
        password: data.password
      }).toPromise();

      return Railway.success(user!);
    } catch (error: any) {
      return Railway.failure({
        type: 'API_ERROR',
        message: 'Failed to create user account',
        errors: [error.message || 'Unknown error occurred'],
        step: 'createAccount'
      });
    }
  }

  private async sendWelcomeEmail(
    user: User
  ): Promise<Railway<User, RegistrationError>> {
    try {
      await this.http.post('/api/emails/welcome', {
        userId: user.id,
        email: user.email,
        name: `${user.firstName} ${user.lastName}`
      }).toPromise();

      return Railway.success(user);
    } catch (error) {
      // Non-critical error - log but don't fail registration
      console.warn('Failed to send welcome email:', error);
      return Railway.success(user);
    }
  }

  private logSuccessfulRegistration = (user: User): void => {
    console.log('Successful registration:', {
      userId: user.id,
      email: user.email,
      timestamp: new Date()
    });
  };

  private createRegistrationResult = (user: User): RegistrationResult => ({
    user,
    success: true,
    message: 'Registration completed successfully',
    timestamp: new Date()
  });

  constructor(private http: HttpClient) {}
}

interface RegistrationFormData {
  email: string;
  username: string;
  firstName: string;
  lastName: string;
  password: string;
  confirmPassword: string;
}

interface RegistrationError {
  type: 'VALIDATION_ERROR' | 'UNIQUENESS_ERROR' | 'API_ERROR';
  message: string;
  errors: string[];
  step: string;
}

interface RegistrationResult {
  user: User;
  success: boolean;
  message: string;
  timestamp: Date;
}
```

<a name="performance"></a>
### **13. Performance Optimization**

Functional programming patterns can significantly improve Angular application performance when applied correctly.

#### **Memoization and Caching Patterns**

```typescript
// 🎯 Functional Memoization for Performance
class FunctionalCache<K, V> {
  private cache = new Map<string, CacheEntry<V>>();
  
  constructor(
    private maxSize: number = 100,
    private ttl: number = 300000 // 5 minutes
  ) {}

  memoize<Args extends readonly unknown[]>(
    fn: (...args: Args) => V,
    keyGenerator?: (...args: Args) => string
  ): (...args: Args) => V {
    return (...args: Args): V => {
      const key = keyGenerator ? keyGenerator(...args) : JSON.stringify(args);
      const cached = this.get(key);
      
      if (cached !== undefined) {
        return cached;
      }
      
      const result = fn(...args);
      this.set(key, result);
      return result;
    };
  }

  memoizeAsync<Args extends readonly unknown[]>(
    fn: (...args: Args) => Observable<V>,
    keyGenerator?: (...args: Args) => string
  ): (...args: Args) => Observable<V> {
    const pendingRequests = new Map<string, Observable<V>>();
    
    return (...args: Args): Observable<V> => {
      const key = keyGenerator ? keyGenerator(...args) : JSON.stringify(args);
      const cached = this.get(key);
      
      if (cached !== undefined) {
        return of(cached);
      }
      
      // Check for pending request
      const pending = pendingRequests.get(key);
      if (pending) {
        return pending;
      }
      
      // Create new request
      const request$ = fn(...args).pipe(
        tap(result => {
          this.set(key, result);
          pendingRequests.delete(key);
        }),
        catchError(error => {
          pendingRequests.delete(key);
          return throwError(() => error);
        }),
        share()
      );
      
      pendingRequests.set(key, request$);
      return request$;
    };
  }

  private get(key: string): V | undefined {
    const entry = this.cache.get(key);
    if (!entry) return undefined;
    
    if (Date.now() - entry.timestamp > this.ttl) {
      this.cache.delete(key);
      return undefined;
    }
    
    return entry.value;
  }

  private set(key: string, value: V): void {
    // Implement LRU eviction
    if (this.cache.size >= this.maxSize) {
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    
    this.cache.set(key, {
      value,
      timestamp: Date.now()
    });
  }

  clear(): void {
    this.cache.clear();
  }

  size(): number {
    return this.cache.size;
  }
}

interface CacheEntry<V> {
  value: V;
  timestamp: number;
}

// Optimized data processing service
@Injectable({
  providedIn: 'root'
})
export class OptimizedDataService {
  private cache = new FunctionalCache<string, any>(50, 300000);

  // Memoized computation functions
  private calculateUserScore = this.cache.memoize(
    (user: User): number => {
      // Expensive calculation
      let score = 0;
      score += user.posts?.length * 10 || 0;
      score += user.followers?.length * 5 || 0;
      score += user.comments?.length * 2 || 0;
      
      // Simulate complex calculation
      for (let i = 0; i < 1000; i++) {
        score += Math.random() * 0.1;
      }
      
      return Math.round(score);
    },
    (user: User) => `user-score-${user.id}-${user.updatedAt?.getTime()}`
  );

  private filterAndSortUsers = this.cache.memoize(
    (users: User[], filters: UserFilters, sortBy: string): User[] => {
      let filtered = users;
      
      // Apply filters
      if (filters.activeOnly) {
        filtered = filtered.filter(u => u.isActive);
      }
      
      if (filters.minScore !== undefined) {
        filtered = filtered.filter(u => this.calculateUserScore(u) >= filters.minScore!);
      }
      
      if (filters.searchTerm) {
        const term = filters.searchTerm.toLowerCase();
        filtered = filtered.filter(u => 
          u.name.toLowerCase().includes(term) ||
          u.email.toLowerCase().includes(term)
        );
      }
      
      // Apply sorting
      switch (sortBy) {
        case 'score':
          return filtered.sort((a, b) => this.calculateUserScore(b) - this.calculateUserScore(a));
        case 'name':
          return filtered.sort((a, b) => a.name.localeCompare(b.name));
        case 'created':
          return filtered.sort((a, b) => b.createdAt.getTime() - a.createdAt.getTime());
        default:
          return filtered;
      }
    },
    (users: User[], filters: UserFilters, sortBy: string) => 
      `filtered-users-${users.length}-${JSON.stringify(filters)}-${sortBy}`
  );

  // Memoized API calls
  private fetchUserData = this.cache.memoizeAsync(
    (userId: number): Observable<User> =>
      this.http.get<User>(`/api/users/${userId}`),
    (userId: number) => `user-${userId}`
  );

  private fetchUserStats = this.cache.memoizeAsync(
    (userId: number): Observable<UserStats> =>
      this.http.get<UserStats>(`/api/users/${userId}/stats`),
    (userId: number) => `user-stats-${userId}`
  );

  // Composed operations with caching
  getUserWithStats(userId: number): Observable<UserWithStats> {
    return combineLatest([
      this.fetchUserData(userId),
      this.fetchUserStats(userId)
    ]).pipe(
      map(([user, stats]) => ({
        ...user,
        stats,
        score: this.calculateUserScore(user)
      }))
    );
  }

  getFilteredUsers(filters: UserFilters, sortBy: string = 'name'): Observable<User[]> {
    return this.http.get<User[]>('/api/users').pipe(
      map(users => this.filterAndSortUsers(users, filters, sortBy))
    );
  }

  // Batch operations with optimized caching
  getUserScores(userIds: number[]): Observable<Record<number, number>> {
    const cachedScores: Record<number, number> = {};
    const uncachedIds: number[] = [];
    
    // Check cache first
    userIds.forEach(id => {
      const cached = this.cache.get(`user-score-batch-${id}`);
      if (cached !== undefined) {
        cachedScores[id] = cached;
      } else {
        uncachedIds.push(id);
      }
    });
    
    if (uncachedIds.length === 0) {
      return of(cachedScores);
    }
    
    // Fetch uncached data
    return this.http.post<User[]>('/api/users/batch', { ids: uncachedIds }).pipe(
      map(users => {
        const newScores: Record<number, number> = {};
        
        users.forEach(user => {
          const score = this.calculateUserScore(user);
          newScores[user.id] = score;
          
          // Cache individual score
          this.cache.set(`user-score-batch-${user.id}`, score);
        });
        
        return { ...cachedScores, ...newScores };
      })
    );
  }

  // Cache management
  clearCache(): void {
    this.cache.clear();
  }

  getCacheSize(): number {
    return this.cache.size();
  }

  constructor(private http: HttpClient) {}
}

interface UserFilters {
  activeOnly?: boolean;
  minScore?: number;
  searchTerm?: string;
}

interface UserStats {
  postsCount: number;
  followersCount: number;
  commentsCount: number;
  lastActiveDate: Date;
}

interface UserWithStats extends User {
  stats: UserStats;
  score: number;
}
```

---

*💡 **Key Takeaway**: Advanced functional concepts like Result/Railway patterns, memoization, and immutable caching create robust, performant Angular applications. These patterns prevent errors from spreading and optimize expensive computations through pure functional approaches.*

---

<a name="tier1"></a>
## 🏆 **PART V: COMPANY-TIER CHALLENGES**

### **14. Tier 1: FAANG Functional Architecture**

FAANG companies expect deep understanding of functional programming principles applied to large-scale Angular architectures.

#### **Challenge 1: Functional Reactive State Management**

```typescript
// 🎯 Google/Meta Level Challenge: Design a functional state management system
// Requirements: Type-safe, immutable, composable, performant for 10M+ users

interface Challenge1Requirements {
  // Design a functional state management system that:
  // 1. Handles deeply nested immutable updates
  // 2. Provides time-travel debugging
  // 3. Supports optimistic updates with rollback
  // 4. Implements efficient memoization
  // 5. Type-safe throughout with full IntelliSense
}

// SOLUTION: Advanced Functional State Architecture
abstract class FunctionalStore<T> {
  protected state$ = new BehaviorSubject<T>(this.initialState);
  protected actionHistory: StateAction<T>[] = [];
  protected snapshotHistory: T[] = [];
  protected memoCache = new Map<string, any>();

  constructor(protected initialState: T) {}

  // Lens-based immutable updates for nested state
  updateWithLens<K extends keyof T>(
    lens: Lens<T, T[K]>,
    updater: (value: T[K]) => T[K]
  ): void {
    const currentState = this.state$.getValue();
    const newState = lens.set(currentState, updater(lens.get(currentState)));
    this.commitStateChange(newState, `LENS_UPDATE_${String(lens.path)}`);
  }

  // Optimistic updates with automatic rollback
  optimisticUpdate<P>(
    optimisticUpdater: (state: T) => T,
    asyncOperation: (state: T) => Observable<T>,
    actionName: string
  ): Observable<T> {
    const originalState = this.state$.getValue();
    const optimisticState = optimisticUpdater(originalState);
    
    // Apply optimistic update immediately
    this.state$.next(optimisticState);
    
    return asyncOperation(optimisticState).pipe(
      tap(confirmedState => {
        this.commitStateChange(confirmedState, `${actionName}_CONFIRMED`);
      }),
      catchError(error => {
        // Rollback on failure
        this.state$.next(originalState);
        this.commitStateChange(originalState, `${actionName}_ROLLBACK`);
        return throwError(() => error);
      })
    );
  }

  // Time-travel debugging
  travelToSnapshot(index: number): void {
    if (index >= 0 && index < this.snapshotHistory.length) {
      const targetState = this.snapshotHistory[index];
      this.state$.next(targetState);
    }
  }

  // Memoized selectors for performance
  createMemoizedSelector<R>(
    selector: (state: T) => R,
    keyGenerator?: (state: T) => string
  ): Observable<R> {
    return this.state$.pipe(
      map(state => {
        const key = keyGenerator ? keyGenerator(state) : JSON.stringify(state);
        
        if (this.memoCache.has(key)) {
          return this.memoCache.get(key);
        }
        
        const result = selector(state);
        this.memoCache.set(key, result);
        
        // Limit cache size
        if (this.memoCache.size > 1000) {
          const firstKey = this.memoCache.keys().next().value;
          this.memoCache.delete(firstKey);
        }
        
        return result;
      }),
      distinctUntilChanged()
    );
  }

  protected commitStateChange(newState: T, actionName: string): void {
    this.actionHistory.push({
      type: actionName,
      timestamp: new Date(),
      previousState: this.state$.getValue(),
      newState
    });
    
    this.snapshotHistory.push(newState);
    if (this.snapshotHistory.length > 100) {
      this.snapshotHistory.shift();
    }
    
    this.state$.next(newState);
  }
}

// Lens implementation for functional updates
class Lens<S, A> {
  constructor(
    public path: string,
    private getter: (s: S) => A,
    private setter: (s: S, a: A) => S
  ) {}

  get(s: S): A {
    return this.getter(s);
  }

  set(s: S, a: A): S {
    return this.setter(s, a);
  }

  modify(s: S, f: (a: A) => A): S {
    return this.set(s, f(this.get(s)));
  }

  // Compose lenses for nested access
  compose<B>(other: Lens<A, B>): Lens<S, B> {
    return new Lens(
      `${this.path}.${other.path}`,
      s => other.get(this.get(s)),
      (s, b) => this.set(s, other.set(this.get(s), b))
    );
  }
}

// Factory for creating type-safe lenses
const createLens = {
  prop: <S, K extends keyof S>(key: K): Lens<S, S[K]> =>
    new Lens(
      String(key),
      s => s[key],
      (s, a) => ({ ...s, [key]: a })
    ),

  index: <T>(index: number): Lens<T[], T> =>
    new Lens(
      `[${index}]`,
      arr => arr[index],
      (arr, item) => [
        ...arr.slice(0, index),
        item,
        ...arr.slice(index + 1)
      ]
    ),

  find: <T>(predicate: (item: T) => boolean): Lens<T[], T | undefined> =>
    new Lens(
      `find(${predicate.toString()})`,
      arr => arr.find(predicate),
      (arr, item) => {
        if (!item) return arr;
        const index = arr.findIndex(predicate);
        return index >= 0
          ? [...arr.slice(0, index), item, ...arr.slice(index + 1)]
          : [...arr, item];
      }
    )
};
```

#### **Challenge 2: High-Performance Functional Data Processing**

```typescript
// 🎯 Netflix/Amazon Level Challenge: Process millions of data points functionally
// Requirements: Handle 1M+ records, real-time updates, memory efficient

interface Challenge2Requirements {
  // Build a functional data processing pipeline that:
  // 1. Processes streaming data in real-time
  // 2. Maintains memory efficiency with 1M+ records
  // 3. Provides sub-100ms response times
  // 4. Handles backpressure gracefully
  // 5. Supports complex aggregations and filtering
}

// SOLUTION: Streaming Functional Data Processor
@Injectable({
  providedIn: 'root'
})
export class StreamingDataProcessor {
  
  // Windowed processing for memory efficiency
  processStreamingData<T, R>(
    dataStream: Observable<T>,
    windowSize: number,
    windowTime: number,
    processor: (window: T[]) => R[]
  ): Observable<R> {
    return dataStream.pipe(
      bufferTime(windowTime, null, windowSize),
      filter(buffer => buffer.length > 0),
      mergeMap(buffer => 
        from(processor(buffer)).pipe(
          // Use scheduler for non-blocking processing
          observeOn(asyncScheduler)
        )
      ),
      // Handle backpressure
      onBackpressureBuffer(10000, () => 
        console.warn('Buffer overflow in streaming processor')
      )
    );
  }

  // Functional aggregation pipeline
  createAggregationPipeline<T>(
    groupBy: (item: T) => string,
    aggregators: Record<string, (items: T[]) => number>
  ): (data: T[]) => Record<string, Record<string, number>> {
    return (data: T[]) => {
      // Use Map for O(1) grouping performance
      const groups = new Map<string, T[]>();
      
      // Functional grouping
      data.forEach(item => {
        const key = groupBy(item);
        const existing = groups.get(key) || [];
        groups.set(key, [...existing, item]);
      });
      
      // Functional aggregation
      const result: Record<string, Record<string, number>> = {};
      
      groups.forEach((items, groupKey) => {
        result[groupKey] = Object.entries(aggregators).reduce(
          (acc, [aggKey, aggFunc]) => ({
            ...acc,
            [aggKey]: aggFunc(items)
          }),
          {}
        );
      });
      
      return result;
    };
  }

  // Memory-efficient functional filtering
  createFilterPipeline<T>(
    filters: Array<(item: T) => boolean>
  ): (data: T[]) => T[] {
    // Compose all filters into single pass
    const composedFilter = (item: T): boolean =>
      filters.every(filter => filter(item));
    
    return (data: T[]) => data.filter(composedFilter);
  }

  // Parallel processing with workers
  processInParallel<T, R>(
    data: T[],
    processor: (chunk: T[]) => R[],
    chunkSize: number = 1000
  ): Observable<R[]> {
    const chunks = this.chunkArray(data, chunkSize);
    
    return from(chunks).pipe(
      mergeMap(chunk => 
        from(this.processInWorker(chunk, processor)).pipe(
          catchError(error => {
            console.error('Worker processing error:', error);
            return of([]);
          })
        )
      ),
      scan((acc: R[], chunk: R[]) => [...acc, ...chunk], []),
      last()
    );
  }

  private chunkArray<T>(array: T[], size: number): T[][] {
    const chunks: T[][] = [];
    for (let i = 0; i < array.length; i += size) {
      chunks.push(array.slice(i, i + size));
    }
    return chunks;
  }

  private async processInWorker<T, R>(
    data: T[],
    processor: (chunk: T[]) => R[]
  ): Promise<R[]> {
    // In a real implementation, this would use Web Workers
    // For now, simulate async processing
    return new Promise(resolve => {
      setTimeout(() => {
        resolve(processor(data));
      }, 1);
    });
  }
}

// Custom backpressure operator
function onBackpressureBuffer<T>(
  bufferSize: number,
  onOverflow: () => void
): OperatorFunction<T, T> {
  return source => new Observable(subscriber => {
    const buffer: T[] = [];
    let isProcessing = false;
    
    const processBuffer = () => {
      if (isProcessing || buffer.length === 0) return;
      
      isProcessing = true;
      const item = buffer.shift()!;
      
      try {
        subscriber.next(item);
      } catch (error) {
        subscriber.error(error);
        return;
      }
      
      isProcessing = false;
      
      // Schedule next processing
      if (buffer.length > 0) {
        setTimeout(processBuffer, 0);
      }
    };
    
    source.subscribe({
      next: value => {
        if (buffer.length >= bufferSize) {
          onOverflow();
          buffer.shift(); // Drop oldest item
        }
        
        buffer.push(value);
        processBuffer();
      },
      error: err => subscriber.error(err),
      complete: () => {
        // Process remaining buffer
        const processRemaining = () => {
          if (buffer.length > 0) {
            const item = buffer.shift()!;
            subscriber.next(item);
            setTimeout(processRemaining, 0);
          } else {
            subscriber.complete();
          }
        };
        
        processRemaining();
      }
    });
  });
}
```

<a name="tier2"></a>
### **15. Tier 2: Practical Functional Patterns**

Mid-tier companies focus on practical application of functional programming for business requirements.

#### **Challenge 3: E-commerce Cart Functional Design**

```typescript
// 🎯 Cognizant/EPAM Level Challenge: Functional shopping cart system
// Requirements: Type-safe, immutable, with business rules validation

interface CartItem {
  id: string;
  productId: string;
  name: string;
  price: number;
  quantity: number;
  discounts: Discount[];
  metadata: Record<string, any>;
}

interface Discount {
  id: string;
  type: 'percentage' | 'fixed' | 'buy-x-get-y';
  value: number;
  conditions?: Record<string, any>;
}

interface CartState {
  items: readonly CartItem[];
  appliedCoupons: readonly string[];
  shippingMethod?: string;
  totalItems: number;
  subtotal: number;
  discountAmount: number;
  shippingCost: number;
  taxAmount: number;
  total: number;
}

// SOLUTION: Functional Shopping Cart
@Injectable({
  providedIn: 'root'
})
export class FunctionalCartService {
  
  private cartSubject = new BehaviorSubject<CartState>(this.getEmptyCart());
  
  // Pure calculation functions
  private readonly calculations = {
    
    calculateItemTotal: (item: CartItem): number => {
      const baseTotal = item.price * item.quantity;
      const discountAmount = item.discounts.reduce((total, discount) => {
        switch (discount.type) {
          case 'percentage':
            return total + (baseTotal * discount.value / 100);
          case 'fixed':
            return total + discount.value;
          default:
            return total;
        }
      }, 0);
      
      return Math.max(0, baseTotal - discountAmount);
    },

    calculateSubtotal: (items: readonly CartItem[]): number =>
      items.reduce((sum, item) => 
        sum + this.calculations.calculateItemTotal(item), 0
      ),

    calculateTotalItems: (items: readonly CartItem[]): number =>
      items.reduce((sum, item) => sum + item.quantity, 0),

    calculateShipping: (
      items: readonly CartItem[],
      method?: string,
      subtotal?: number
    ): number => {
      if (!method) return 0;
      
      // Business logic for shipping calculation
      const itemCount = this.calculations.calculateTotalItems(items);
      const baseShipping = method === 'express' ? 15 : 5;
      
      // Free shipping for orders over $50
      if (subtotal && subtotal > 50) return 0;
      
      return baseShipping + (itemCount > 5 ? itemCount - 5 : 0);
    },

    calculateTax: (subtotal: number, shippingCost: number): number => {
      const taxableAmount = subtotal + shippingCost;
      return taxableAmount * 0.08; // 8% tax
    }
  };

  // Pure state update functions
  private readonly updaters = {
    
    addItem: (state: CartState, newItem: CartItem): CartState => {
      const existingIndex = state.items.findIndex(
        item => item.productId === newItem.productId
      );
      
      const updatedItems = existingIndex >= 0
        ? state.items.map((item, index) =>
            index === existingIndex
              ? { ...item, quantity: item.quantity + newItem.quantity }
              : item
          )
        : [...state.items, newItem];
      
      return this.recalculateCart({ ...state, items: updatedItems });
    },

    updateQuantity: (
      state: CartState,
      productId: string,
      quantity: number
    ): CartState => {
      if (quantity <= 0) {
        return this.updaters.removeItem(state, productId);
      }
      
      const updatedItems = state.items.map(item =>
        item.productId === productId
          ? { ...item, quantity }
          : item
      );
      
      return this.recalculateCart({ ...state, items: updatedItems });
    },

    removeItem: (state: CartState, productId: string): CartState => {
      const updatedItems = state.items.filter(
        item => item.productId !== productId
      );
      
      return this.recalculateCart({ ...state, items: updatedItems });
    },

    applyCoupon: (state: CartState, couponCode: string): CartState => {
      if (state.appliedCoupons.includes(couponCode)) {
        return state; // Already applied
      }
      
      const updatedCoupons = [...state.appliedCoupons, couponCode];
      return this.recalculateCart({ ...state, appliedCoupons: updatedCoupons });
    },

    setShippingMethod: (state: CartState, method: string): CartState => {
      return this.recalculateCart({ ...state, shippingMethod: method });
    }
  };

  // Recalculate entire cart state
  private recalculateCart(state: Partial<CartState>): CartState {
    const items = state.items || [];
    const subtotal = this.calculations.calculateSubtotal(items);
    const totalItems = this.calculations.calculateTotalItems(items);
    const shippingCost = this.calculations.calculateShipping(
      items,
      state.shippingMethod,
      subtotal
    );
    const taxAmount = this.calculations.calculateTax(subtotal, shippingCost);
    const total = subtotal + shippingCost + taxAmount;

    return {
      items,
      appliedCoupons: state.appliedCoupons || [],
      shippingMethod: state.shippingMethod,
      totalItems,
      subtotal,
      discountAmount: 0, // Would be calculated based on coupons
      shippingCost,
      taxAmount,
      total
    };
  }

  // Public API methods
  getCart(): Observable<CartState> {
    return this.cartSubject.asObservable();
  }

  addToCart(item: Omit<CartItem, 'id'>): void {
    const cartItem: CartItem = {
      ...item,
      id: this.generateId()
    };
    
    const currentState = this.cartSubject.value;
    const newState = this.updaters.addItem(currentState, cartItem);
    this.cartSubject.next(newState);
  }

  updateItemQuantity(productId: string, quantity: number): void {
    const currentState = this.cartSubject.value;
    const newState = this.updaters.updateQuantity(currentState, productId, quantity);
    this.cartSubject.next(newState);
  }

  removeFromCart(productId: string): void {
    const currentState = this.cartSubject.value;
    const newState = this.updaters.removeItem(currentState, productId);
    this.cartSubject.next(newState);
  }

  applyCoupon(couponCode: string): Observable<boolean> {
    return this.validateCoupon(couponCode).pipe(
      tap(isValid => {
        if (isValid) {
          const currentState = this.cartSubject.value;
          const newState = this.updaters.applyCoupon(currentState, couponCode);
          this.cartSubject.next(newState);
        }
      })
    );
  }

  setShippingMethod(method: string): void {
    const currentState = this.cartSubject.value;
    const newState = this.updaters.setShippingMethod(currentState, method);
    this.cartSubject.next(newState);
  }

  clearCart(): void {
    this.cartSubject.next(this.getEmptyCart());
  }

  // Validation functions
  private validateCoupon(couponCode: string): Observable<boolean> {
    // Simulate API validation
    return of(couponCode.startsWith('VALID')).pipe(delay(500));
  }

  private getEmptyCart(): CartState {
    return this.recalculateCart({
      items: [],
      appliedCoupons: [],
      shippingMethod: undefined
    });
  }

  private generateId(): string {
    return Date.now().toString(36) + Math.random().toString(36).substr(2);
  }

  constructor() {}
}
```

<a name="tier3"></a>
### **16. Tier 3: Functional Basics**

Entry-level companies focus on understanding and applying basic functional programming concepts.

#### **Challenge 4: Todo List with Functional Updates**

```typescript
// 🎯 Startup/Agency Level Challenge: Functional todo management
// Requirements: Basic functional patterns, immutable updates, simple state

interface TodoItem {
  id: number;
  text: string;
  completed: boolean;
  createdAt: Date;
  priority: 'low' | 'medium' | 'high';
  tags: string[];
}

interface TodoState {
  todos: readonly TodoItem[];
  filter: 'all' | 'active' | 'completed';
  sortBy: 'created' | 'priority' | 'alphabetical';
}

// SOLUTION: Functional Todo Service
@Injectable({
  providedIn: 'root'
})
export class FunctionalTodoService {
  
  private stateSubject = new BehaviorSubject<TodoState>({
    todos: [],
    filter: 'all',
    sortBy: 'created'
  });

  // Pure functions for todo operations
  private readonly todoOperations = {
    
    create: (text: string, priority: TodoItem['priority'] = 'medium'): TodoItem => ({
      id: Date.now(),
      text: text.trim(),
      completed: false,
      createdAt: new Date(),
      priority,
      tags: []
    }),

    toggle: (todo: TodoItem): TodoItem => ({
      ...todo,
      completed: !todo.completed
    }),

    updateText: (todo: TodoItem, newText: string): TodoItem => ({
      ...todo,
      text: newText.trim()
    }),

    addTag: (todo: TodoItem, tag: string): TodoItem => ({
      ...todo,
      tags: [...todo.tags, tag].filter((t, i, arr) => arr.indexOf(t) === i)
    }),

    removeTag: (todo: TodoItem, tag: string): TodoItem => ({
      ...todo,
      tags: todo.tags.filter(t => t !== tag)
    })
  };

  // Pure functions for filtering and sorting
  private readonly listOperations = {
    
    filter: (todos: readonly TodoItem[], filter: TodoState['filter']): readonly TodoItem[] => {
      switch (filter) {
        case 'active':
          return todos.filter(todo => !todo.completed);
        case 'completed':
          return todos.filter(todo => todo.completed);
        default:
          return todos;
      }
    },

    sort: (todos: readonly TodoItem[], sortBy: TodoState['sortBy']): readonly TodoItem[] => {
      const sortedTodos = [...todos];
      
      switch (sortBy) {
        case 'alphabetical':
          return sortedTodos.sort((a, b) => a.text.localeCompare(b.text));
        case 'priority':
          const priorityOrder = { high: 3, medium: 2, low: 1 };
          return sortedTodos.sort((a, b) => 
            priorityOrder[b.priority] - priorityOrder[a.priority]
          );
        case 'created':
        default:
          return sortedTodos.sort((a, b) => 
            b.createdAt.getTime() - a.createdAt.getTime()
          );
      }
    },

    search: (todos: readonly TodoItem[], searchTerm: string): readonly TodoItem[] => {
      if (!searchTerm.trim()) return todos;
      
      const term = searchTerm.toLowerCase();
      return todos.filter(todo =>
        todo.text.toLowerCase().includes(term) ||
        todo.tags.some(tag => tag.toLowerCase().includes(term))
      );
    }
  };

  // State update methods
  addTodo(text: string, priority?: TodoItem['priority']): void {
    if (!text.trim()) return;
    
    const newTodo = this.todoOperations.create(text, priority);
    const currentState = this.stateSubject.value;
    
    this.stateSubject.next({
      ...currentState,
      todos: [...currentState.todos, newTodo]
    });
  }

  toggleTodo(id: number): void {
    const currentState = this.stateSubject.value;
    const updatedTodos = currentState.todos.map(todo =>
      todo.id === id ? this.todoOperations.toggle(todo) : todo
    );
    
    this.stateSubject.next({
      ...currentState,
      todos: updatedTodos
    });
  }

  updateTodoText(id: number, newText: string): void {
    const currentState = this.stateSubject.value;
    const updatedTodos = currentState.todos.map(todo =>
      todo.id === id ? this.todoOperations.updateText(todo, newText) : todo
    );
    
    this.stateSubject.next({
      ...currentState,
      todos: updatedTodos
    });
  }

  deleteTodo(id: number): void {
    const currentState = this.stateSubject.value;
    const updatedTodos = currentState.todos.filter(todo => todo.id !== id);
    
    this.stateSubject.next({
      ...currentState,
      todos: updatedTodos
    });
  }

  addTagToTodo(id: number, tag: string): void {
    const currentState = this.stateSubject.value;
    const updatedTodos = currentState.todos.map(todo =>
      todo.id === id ? this.todoOperations.addTag(todo, tag) : todo
    );
    
    this.stateSubject.next({
      ...currentState,
      todos: updatedTodos
    });
  }

  setFilter(filter: TodoState['filter']): void {
    const currentState = this.stateSubject.value;
    this.stateSubject.next({
      ...currentState,
      filter
    });
  }

  setSortBy(sortBy: TodoState['sortBy']): void {
    const currentState = this.stateSubject.value;
    this.stateSubject.next({
      ...currentState,
      sortBy
    });
  }

  // Computed properties as observables
  getFilteredAndSortedTodos(): Observable<readonly TodoItem[]> {
    return this.stateSubject.pipe(
      map(state => {
        const filtered = this.listOperations.filter(state.todos, state.filter);
        return this.listOperations.sort(filtered, state.sortBy);
      })
    );
  }

  searchTodos(searchTerm: string): Observable<readonly TodoItem[]> {
    return this.getFilteredAndSortedTodos().pipe(
      map(todos => this.listOperations.search(todos, searchTerm))
    );
  }

  getTodoStats(): Observable<{total: number, completed: number, active: number}> {
    return this.stateSubject.pipe(
      map(state => ({
        total: state.todos.length,
        completed: state.todos.filter(t => t.completed).length,
        active: state.todos.filter(t => !t.completed).length
      }))
    );
  }

  // Complete all todos (demonstration of batch operations)
  toggleAllTodos(): void {
    const currentState = this.stateSubject.value;
    const allCompleted = currentState.todos.every(todo => todo.completed);
    
    const updatedTodos = currentState.todos.map(todo => ({
      ...todo,
      completed: !allCompleted
    }));
    
    this.stateSubject.next({
      ...currentState,
      todos: updatedTodos
    });
  }

  clearCompleted(): void {
    const currentState = this.stateSubject.value;
    const updatedTodos = currentState.todos.filter(todo => !todo.completed);
    
    this.stateSubject.next({
      ...currentState,
      todos: updatedTodos
    });
  }
}
```

---

## 🎯 **PRACTICAL EXERCISES**

### **Exercise 1: Build a Functional Data Pipeline**

```typescript
// 🎯 Create a functional data transformation pipeline
// Transform raw API data through multiple steps functionally

interface RawUserData {
  id: number;
  name: string;
  email: string;
  posts: Array<{
    id: number;
    title: string;
    content: string;
    likes: number;
    comments: Array<{id: number, text: string, author: string}>;
  }>;
}

interface ProcessedUser {
  id: number;
  displayName: string;
  email: string;
  stats: {
    totalPosts: number;
    totalLikes: number;
    totalComments: number;
    averageLikes: number;
    engagementScore: number;
  };
  topPost?: {
    title: string;
    likes: number;
  };
}

// YOUR TASK: Implement the functional pipeline
class UserDataPipeline {
  
  // Step 1: Validate and clean raw data
  validateUser = (user: RawUserData): Result<RawUserData, string> => {
    // TODO: Implement validation
    throw new Error('Not implemented');
  };

  // Step 2: Calculate user statistics
  calculateStats = (user: RawUserData): UserStats => {
    // TODO: Implement pure calculation function
    throw new Error('Not implemented');
  };

  // Step 3: Find top performing post
  findTopPost = (posts: RawUserData['posts']): {title: string, likes: number} | undefined => {
    // TODO: Implement functional search
    throw new Error('Not implemented');
  };

  // Step 4: Transform to final format
  transformUser = (user: RawUserData): ProcessedUser => {
    // TODO: Compose all transformations
    throw new Error('Not implemented');
  };

  // Step 5: Process batch of users
  processUserBatch = (users: RawUserData[]): ProcessedUser[] => {
    // TODO: Implement functional batch processing with error handling
    throw new Error('Not implemented');
  };
}
```

### **Exercise 2: Functional Form Validation**

```typescript
// 🎯 Build a composable validation system
// Create reusable validators that can be composed

interface ValidationRule<T> {
  validate: (value: T) => ValidationResult;
  message: string;
}

interface ValidationResult {
  isValid: boolean;
  errors: string[];
}

// YOUR TASK: Build composable validators
class FunctionalValidators {
  
  // Basic validators
  required = <T>(fieldName: string): ValidationRule<T> => {
    // TODO: Implement required validator
    throw new Error('Not implemented');
  };

  minLength = (min: number, fieldName: string): ValidationRule<string> => {
    // TODO: Implement min length validator
    throw new Error('Not implemented');
  };

  pattern = (regex: RegExp, message: string): ValidationRule<string> => {
    // TODO: Implement pattern validator
    throw new Error('Not implemented');
  };

  // Compose validators
  compose = <T>(...rules: ValidationRule<T>[]): ValidationRule<T> => {
    // TODO: Combine multiple validators
    throw new Error('Not implemented');
  };

  // Conditional validators
  when = <T>(
    condition: (value: T) => boolean,
    rule: ValidationRule<T>
  ): ValidationRule<T> => {
    // TODO: Conditional validation
    throw new Error('Not implemented');
  };

  // Cross-field validation
  matches = <T>(
    otherField: keyof T,
    message: string
  ): ValidationRule<T> => {
    // TODO: Field matching validator
    throw new Error('Not implemented');
  };
}
```

---

## 🎓 **CHAPTER SUMMARY**

### **🎯 Key Achievements**

You've mastered functional programming in Angular by learning:

1. **Functional Fundamentals**: Pure functions, immutability, and composition
2. **RxJS Functional Patterns**: Operators as functions and reactive composition
3. **Angular Integration**: OnPush optimization and functional components  
4. **Advanced Concepts**: Error handling, Railway patterns, and performance optimization
5. **Real-World Application**: Company-tier challenges and practical exercises

### **💼 Interview Readiness**

- **Tier 1 (FAANG)**: Advanced functional architecture and performance optimization
- **Tier 2 (Enterprise)**: Practical functional patterns for business applications
- **Tier 3 (Startups)**: Basic functional programming concepts and immutable updates

### **🚀 Next Steps**

- **Practice**: Implement the practical exercises in your own projects
- **Explore**: Investigate functional reactive programming libraries like NgRx
- **Apply**: Use these patterns in your next Angular application
- **Share**: Teach others functional programming concepts

---

*🎉 **Congratulations!** You now have the functional programming expertise to build robust, maintainable Angular applications that scale and perform exceptionally in any interview scenario.*
