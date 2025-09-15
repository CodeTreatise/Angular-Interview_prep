# 🔷 TypeScript Interview Essentials - Angular Success Foundation (03-01)

> **Language Mastery Foundation** - Master TypeScript fundamentals that directly enhance your Angular interview performance. From basic types to advanced patterns, this chapter provides the language expertise that separates strong Angular developers from great ones.

---

## 📋 **INTERVIEW SUCCESS FRAMEWORK**

### **🎯 The 30-Second TypeScript Confidence Formula**
```typescript
SCENARIO: "How comfortable are you with TypeScript and why is it important for Angular?"

POWER RESPONSE (30 seconds):
"TypeScript is essential for Angular because it provides [Type Safety + IntelliSense + Refactoring]. 
I'm proficient with [Interfaces + Generics + Union Types] which are crucial for 
[Component Props + Service APIs + State Management]. TypeScript helps me catch 
[90% of bugs at compile time] rather than runtime, making Angular applications 
more maintainable and developer-friendly."

FOLLOW-UP READY: Be prepared for live TypeScript coding and advanced type scenarios
```

### **🏢 Company-Tier Expectations**
```
🏆 TIER 1 (Google, Microsoft, Meta):
├── Advanced type system mastery (conditional types, mapped types)
├── Generic constraints and utility type creation
├── Performance implications of TypeScript compilation
├── Type-driven architecture design patterns
└── Complex intersection and union type manipulation

🏢 TIER 2 (Cognizant, EPAM, Accenture):
├── Practical interface design for Angular services
├── Type safety in component communication
├── Error handling with typed exceptions
├── API response typing and data transformation
└── Form validation with TypeScript integration

🚀 TIER 3 (Startups, Agencies):
├── Basic type annotations for cleaner code
├── Interface usage for component props
├── Simple generic usage for reusable components
├── Type assertions and type guards for dynamic data
└── Migration benefits from JavaScript to TypeScript
```

---

## 🔄 **WHY-WHAT-WHEN FRAMEWORK FOR TYPESCRIPT MASTERY**

### **🤔 WHY: TypeScript's Critical Role in Angular Development**

#### **📊 Business Impact of TypeScript Mastery**
```typescript
// REAL SCENARIO: Enterprise Angular Application Development

interface BusinessImpactMetrics {
  bugReduction: '90%'; // Compile-time error catching
  developmentVelocity: '+40%'; // IntelliSense and refactoring
  codebaseScalability: '+300%'; // Type safety enables large teams
  maintenanceCost: '-60%'; // Self-documenting code
  developerOnboarding: '+200%'; // Type hints guide new developers
}

// ANGULAR-SPECIFIC TYPESCRIPT VALUE
class AngularTypeScriptBenefits {
  // 1. Component Type Safety
  improveComponentSafety(): ComponentBenefits {
    return {
      inputValidation: 'Compile-time validation of component inputs',
      outputEvents: 'Type-safe event emitter definitions',
      lifecycleHooks: 'Proper typing prevents implementation errors',
      templateBinding: 'Template type checking catches errors early'
    };
  }
  
  // 2. Service Layer Enhancement
  enhanceServiceLayer(): ServiceBenefits {
    return {
      dependencyInjection: 'Type-safe DI container configuration',
      apiInterfaces: 'Structured HTTP response handling',
      errorHandling: 'Typed error responses and exception handling',
      businessLogic: 'Self-documenting business rule implementation'
    };
  }
  
  // 3. State Management Clarity
  improveStateManagement(): StateBenefits {
    return {
      reduxIntegration: 'Type-safe actions and reducers',
      observableStreams: 'Typed RxJS operator chains',
      dataTransformation: 'Compile-time validation of data flow',
      immutableUpdates: 'Type-guided immutability patterns'
    };
  }
}
```

#### **🎯 Interview Success Correlation**
```typescript
// RESEARCH INSIGHT: TypeScript knowledge correlation with interview outcomes

interface InterviewSuccessData {
  typescript_proficiency: {
    basic: { success_rate: 45, average_salary: 75000 },
    intermediate: { success_rate: 70, average_salary: 95000 },
    advanced: { success_rate: 90, average_salary: 125000 }
  };
  
  angular_with_typescript: {
    junior: 'Essential for entry-level positions',
    mid: 'Expected for component architecture discussions',
    senior: 'Required for system design and team leadership'
  };
}
```

### **📋 WHAT: Essential TypeScript Concepts for Angular Interviews**

#### **🔧 Core Type System Mastery**
```typescript
// FUNDAMENTAL TYPES FOR ANGULAR DEVELOPMENT

// 1. PRIMITIVE TYPES WITH ANGULAR CONTEXT
interface UserProfile {
  id: string;              // API identifier
  name: string;            // Display text
  age: number;             // Numeric calculations
  isActive: boolean;       // Component state
  metadata?: unknown;      // Dynamic API data
}

// 2. UNION TYPES FOR COMPONENT STATES
type LoadingState = 'idle' | 'loading' | 'success' | 'error';
type UserRole = 'admin' | 'user' | 'moderator' | 'guest';

// Component usage example
@Component({
  selector: 'user-dashboard',
  template: `
    <div [ngSwitch]="loadingState">
      <spinner *ngSwitchCase="'loading'"></spinner>
      <user-content *ngSwitchCase="'success'" [user]="user"></user-content>
      <error-message *ngSwitchCase="'error'" [error]="error"></error-message>
    </div>
  `
})
export class UserDashboardComponent {
  loadingState: LoadingState = 'idle';
  user?: UserProfile;
  error?: string;
}

// 3. INTERSECTION TYPES FOR MIXIN PATTERNS
interface Timestamped {
  createdAt: Date;
  updatedAt: Date;
}

interface Auditable {
  createdBy: string;
  modifiedBy: string;
}

// Combined interface for domain models
type AuditedEntity<T> = T & Timestamped & Auditable;

interface User {
  id: string;
  name: string;
  email: string;
}

type AuditedUser = AuditedEntity<User>;
// Result: User + createdAt + updatedAt + createdBy + modifiedBy
```

#### **🏗️ Interface Design Patterns for Angular**
```typescript
// INTERFACE PATTERNS FOR ANGULAR APPLICATIONS

// 1. COMPONENT COMMUNICATION INTERFACES
interface ComponentInputs {
  data: UserProfile[];
  loading: boolean;
  error?: string;
}

interface ComponentOutputs {
  userSelected: EventEmitter<UserProfile>;
  actionTriggered: EventEmitter<string>;
  errorOccurred: EventEmitter<Error>;
}

// 2. SERVICE INTERFACE DEFINITIONS
interface UserService {
  // Async operations return observables
  getUsers(): Observable<UserProfile[]>;
  getUserById(id: string): Observable<UserProfile>;
  createUser(user: Partial<UserProfile>): Observable<UserProfile>;
  updateUser(id: string, updates: Partial<UserProfile>): Observable<UserProfile>;
  deleteUser(id: string): Observable<void>;
}

// 3. HTTP RESPONSE INTERFACES
interface ApiResponse<T> {
  data: T;
  status: 'success' | 'error';
  message?: string;
  metadata?: {
    total: number;
    page: number;
    limit: number;
  };
}

// Usage in Angular service
@Injectable({ providedIn: 'root' })
export class ApiUserService implements UserService {
  constructor(private http: HttpClient) {}
  
  getUsers(): Observable<UserProfile[]> {
    return this.http.get<ApiResponse<UserProfile[]>>('/api/users')
      .pipe(
        map(response => response.data),
        catchError(this.handleError)
      );
  }
  
  private handleError(error: HttpErrorResponse): Observable<never> {
    return throwError(() => error);
  }
}

// 4. FORM INTERFACES FOR TYPE-SAFE FORMS
interface UserFormData {
  personalInfo: {
    firstName: string;
    lastName: string;
    email: string;
  };
  preferences: {
    notifications: boolean;
    theme: 'light' | 'dark';
    language: string;
  };
}

// Reactive form with TypeScript
export class UserFormComponent {
  userForm: FormGroup<{
    personalInfo: FormGroup<{
      firstName: FormControl<string>;
      lastName: FormControl<string>;
      email: FormControl<string>;
    }>;
    preferences: FormGroup<{
      notifications: FormControl<boolean>;
      theme: FormControl<'light' | 'dark'>;
      language: FormControl<string>;
    }>;
  }>;
  
  constructor(private fb: FormBuilder) {
    this.userForm = this.createForm();
  }
  
  private createForm() {
    return this.fb.group({
      personalInfo: this.fb.group({
        firstName: ['', Validators.required],
        lastName: ['', Validators.required],
        email: ['', [Validators.required, Validators.email]]
      }),
      preferences: this.fb.group({
        notifications: [true],
        theme: ['light' as const],
        language: ['en']
      })
    });
  }
}
```

#### **🔗 Generic Programming for Reusable Angular Components**
```typescript
// GENERICS FOR ANGULAR COMPONENT REUSABILITY

// 1. GENERIC COMPONENT INTERFACES
interface BaseListComponent<T> {
  items: T[];
  selectedItem?: T;
  onSelect(item: T): void;
  onDelete(item: T): void;
}

// 2. GENERIC DATA SERVICE
interface CrudService<T, K = string> {
  getAll(): Observable<T[]>;
  getById(id: K): Observable<T>;
  create(item: Omit<T, 'id'>): Observable<T>;
  update(id: K, item: Partial<T>): Observable<T>;
  delete(id: K): Observable<void>;
}

// Implementation for specific entities
@Injectable()
export class ProductService implements CrudService<Product> {
  constructor(private http: HttpClient) {}
  
  getAll(): Observable<Product[]> {
    return this.http.get<Product[]>('/api/products');
  }
  
  // ... other methods
}

// 3. GENERIC COMPONENT IMPLEMENTATION
@Component({
  selector: 'app-data-table',
  template: `
    <table>
      <thead>
        <tr>
          <th *ngFor="let column of columns">{{ column.label }}</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let item of items; trackBy: trackByFn">
          <td *ngFor="let column of columns">
            {{ getColumnValue(item, column.key) }}
          </td>
          <td>
            <button (click)="onEdit(item)">Edit</button>
            <button (click)="onDelete(item)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  `
})
export class DataTableComponent<T extends { id: string }> {
  @Input() items: T[] = [];
  @Input() columns: ColumnDefinition<T>[] = [];
  @Output() edit = new EventEmitter<T>();
  @Output() delete = new EventEmitter<T>();
  
  trackByFn = (index: number, item: T): string => item.id;
  
  getColumnValue(item: T, key: keyof T): any {
    return item[key];
  }
  
  onEdit(item: T): void {
    this.edit.emit(item);
  }
  
  onDelete(item: T): void {
    this.delete.emit(item);
  }
}

interface ColumnDefinition<T> {
  key: keyof T;
  label: string;
  sortable?: boolean;
}

// 4. GENERIC OBSERVABLE PATTERNS
class ObservableDataManager<T> {
  private dataSubject = new BehaviorSubject<T[]>([]);
  
  data$: Observable<T[]> = this.dataSubject.asObservable();
  
  add(item: T): void {
    const currentData = this.dataSubject.value;
    this.dataSubject.next([...currentData, item]);
  }
  
  update(predicate: (item: T) => boolean, updates: Partial<T>): void {
    const currentData = this.dataSubject.value;
    const updatedData = currentData.map(item => 
      predicate(item) ? { ...item, ...updates } : item
    );
    this.dataSubject.next(updatedData);
  }
  
  remove(predicate: (item: T) => boolean): void {
    const currentData = this.dataSubject.value;
    const filteredData = currentData.filter(item => !predicate(item));
    this.dataSubject.next(filteredData);
  }
}
```

### **⏰ WHEN: TypeScript Application Patterns in Angular Development**

#### **🎯 Development Phase TypeScript Decisions**
```typescript
// TYPESCRIPT USAGE PATTERNS BY DEVELOPMENT PHASE

interface TypeScriptPhaseStrategy {
  phase: string;
  typeComplexity: 'Basic' | 'Intermediate' | 'Advanced';
  primaryFocus: string;
  toolingEmphasis: string[];
}

const developmentPhases: TypeScriptPhaseStrategy[] = [
  {
    phase: 'Initial Setup & Component Structure',
    typeComplexity: 'Basic',
    primaryFocus: 'Interface definitions for components and services',
    toolingEmphasis: ['strict mode', 'noImplicitAny', 'basic interfaces']
  },
  {
    phase: 'Business Logic Implementation',
    typeComplexity: 'Intermediate',
    primaryFocus: 'Generic services, type guards, utility types',
    toolingEmphasis: ['union types', 'conditional logic', 'type narrowing']
  },
  {
    phase: 'Advanced Features & Optimization',
    typeComplexity: 'Advanced',
    primaryFocus: 'Mapped types, conditional types, complex generics',
    toolingEmphasis: ['performance optimization', 'type-level programming']
  }
];

// TIMING DECISIONS FOR TYPE SAFETY
class TypeSafetyDecisionFramework {
  
  // When to use strict typing vs any
  chooseTypingStrategy(context: DevelopmentContext): TypingStrategy {
    if (context.phase === 'rapid-prototyping') {
      return {
        strategy: 'Gradual typing',
        rationale: 'Start with any/unknown, refine during development',
        timeline: 'Convert to strict types before production'
      };
    }
    
    if (context.teamSize > 5) {
      return {
        strategy: 'Strict typing from start',
        rationale: 'Large teams need compile-time guarantees',
        timeline: 'Immediate implementation with team training'
      };
    }
    
    return {
      strategy: 'Progressive enhancement',
      rationale: 'Add types based on complexity and risk',
      timeline: 'Critical paths first, then expand coverage'
    };
  }
  
  // When to introduce generic patterns
  assessGenericReadiness(codebase: CodebaseMetrics): GenericStrategy {
    const duplicationThreshold = 3; // Rule of three
    
    if (codebase.duplicatedPatterns >= duplicationThreshold) {
      return {
        recommendation: 'Introduce generics',
        priority: 'High',
        expectedBenefit: 'Code reduction and type safety improvement'
      };
    }
    
    return {
      recommendation: 'Continue with specific types',
      priority: 'Low',
      expectedBenefit: 'Maintain simplicity until patterns emerge'
    };
  }
}
```

#### **📊 Real-Time TypeScript Decision Making**
```typescript
// REAL-WORLD TYPESCRIPT DECISION SCENARIOS

// Scenario 1: API Response Handling
interface ApiDecisionContext {
  scenario: 'Unknown API structure from third-party service';
  challenge: 'Balance between type safety and flexibility';
  solutions: TypeSafetyApproach[];
}

type TypeSafetyApproach = {
  approach: string;
  pros: string[];
  cons: string[];
  useWhen: string;
};

const apiResponseStrategies: TypeSafetyApproach[] = [
  {
    approach: 'Strict interface definition',
    pros: ['Complete type safety', 'Clear documentation', 'Compile-time errors'],
    cons: ['Brittle to API changes', 'Requires API knowledge'],
    useWhen: 'Stable APIs with known schema'
  },
  {
    approach: 'Partial interface with optional properties',
    pros: ['Flexible to API changes', 'Gradual type adoption'],
    cons: ['Runtime type checking needed', 'Potential undefined access'],
    useWhen: 'Evolving APIs or incomplete API documentation'
  },
  {
    approach: 'Union types for known variations',
    pros: ['Handles multiple response formats', 'Type-safe discrimination'],
    cons: ['Complex type guards needed', 'Increased complexity'],
    useWhen: 'APIs with multiple response formats'
  },
  {
    approach: 'Generic response wrapper',
    pros: ['Reusable pattern', 'Consistent error handling'],
    cons: ['Learning curve', 'Abstraction overhead'],
    useWhen: 'Standardized API response patterns'
  }
];

// Implementation example
interface FlexibleApiResponse<T = unknown> {
  data?: T;
  error?: {
    code: string;
    message: string;
    details?: unknown;
  };
  metadata?: {
    timestamp: string;
    version: string;
    [key: string]: unknown;
  };
}

// Type-safe API service
@Injectable()
export class TypeSafeApiService {
  
  // Strict typing for known endpoints
  getUser(id: string): Observable<UserProfile> {
    return this.http.get<FlexibleApiResponse<UserProfile>>(`/api/users/${id}`)
      .pipe(
        map(response => {
          if (response.error) {
            throw new Error(response.error.message);
          }
          if (!response.data) {
            throw new Error('No user data received');
          }
          return response.data;
        })
      );
  }
  
  // Flexible typing for dynamic endpoints
  getDynamicData<T>(endpoint: string): Observable<T> {
    return this.http.get<FlexibleApiResponse<T>>(endpoint)
      .pipe(
        map(response => {
          if (response.error) {
            throw new Error(response.error.message);
          }
          return response.data as T;
        })
      );
  }
}
```

---

## 🧮 **TYPESCRIPT ALGORITHM IMPLEMENTATIONS**

### **🔗 Algorithm Fundamentals Integration**
*Building on [Section 01-10 Algorithm Fundamentals](../01-Interview-Essentials/01-10-algorithm-fundamentals.md) with TypeScript-specific implementations*

#### **🎯 Type-Safe Algorithm Patterns**
```typescript
// TYPESCRIPT ENHANCES ALGORITHM RELIABILITY AND MAINTAINABILITY

// 1. TYPE-SAFE ARRAY DEDUPLICATION
interface UniqueComparable {
  id: string;
}

function removeDuplicates<T extends UniqueComparable>(items: T[]): T[] {
  const seen = new Set<string>();
  const result: T[] = [];
  
  for (const item of items) {
    if (!seen.has(item.id)) {
      seen.add(item.id);
      result.push(item);
    }
  }
  
  return result;
}

// Angular usage with type safety
interface User extends UniqueComparable {
  name: string;
  email: string;
}

interface Product extends UniqueComparable {
  title: string;
  price: number;
}

// Both work with same function - type safety guaranteed
const uniqueUsers: User[] = removeDuplicates(users);
const uniqueProducts: Product[] = removeDuplicates(products);

// 2. GENERIC SEARCH ALGORITHM
interface Searchable {
  [key: string]: any;
}

function searchByProperty<T extends Searchable>(
  items: T[],
  searchTerm: string,
  searchProperty: keyof T
): T[] {
  const normalizedTerm = searchTerm.toLowerCase();
  
  return items.filter(item => {
    const value = item[searchProperty];
    return String(value).toLowerCase().includes(normalizedTerm);
  });
}

// Type-safe search with autocomplete for properties
const searchResults = searchByProperty(users, 'john', 'name'); // ✅ 'name' autocompleted
// const invalidSearch = searchByProperty(users, 'john', 'invalid'); // ❌ Compile error

// 3. TYPED GROUPING ALGORITHM
function groupBy<T, K extends keyof T>(
  items: T[],
  key: K
): Record<string, T[]> {
  return items.reduce((groups, item) => {
    const group = String(item[key]);
    if (!groups[group]) {
      groups[group] = [];
    }
    groups[group].push(item);
    return groups;
  }, {} as Record<string, T[]>);
}

// Angular component usage
@Component({
  selector: 'user-groups',
  template: `
    <div *ngFor="let group of groupedUsers | keyvalue">
      <h3>{{ group.key }}</h3>
      <div *ngFor="let user of group.value">
        {{ user.name }}
      </div>
    </div>
  `
})
export class UserGroupsComponent {
  users: User[] = [
    { id: '1', name: 'John', email: 'john@example.com', department: 'Engineering' },
    { id: '2', name: 'Jane', email: 'jane@example.com', department: 'Engineering' },
    { id: '3', name: 'Bob', email: 'bob@example.com', department: 'Marketing' }
  ];
  
  // Type-safe grouping with property validation
  groupedUsers = groupBy(this.users, 'department');
}

// 4. DEBOUNCE WITH TYPESCRIPT
interface DebounceOptions {
  immediate?: boolean;
  maxWait?: number;
}

function debounce<TArgs extends any[], TReturn>(
  func: (...args: TArgs) => TReturn,
  wait: number,
  options: DebounceOptions = {}
): (...args: TArgs) => void {
  let timeoutId: NodeJS.Timeout | undefined;
  let maxTimeoutId: NodeJS.Timeout | undefined;
  let lastCallTime: number | undefined;
  
  const { immediate = false, maxWait } = options;
  
  return function(...args: TArgs): void {
    const callNow = immediate && !timeoutId;
    const currentTime = Date.now();
    
    // Clear existing timeout
    if (timeoutId) {
      clearTimeout(timeoutId);
    }
    
    // Handle maxWait
    if (maxWait !== undefined) {
      if (!lastCallTime) {
        lastCallTime = currentTime;
      }
      
      const timeSinceLastCall = currentTime - lastCallTime;
      if (timeSinceLastCall >= maxWait) {
        func(...args);
        lastCallTime = currentTime;
        if (maxTimeoutId) {
          clearTimeout(maxTimeoutId);
          maxTimeoutId = undefined;
        }
        return;
      }
      
      if (!maxTimeoutId) {
        maxTimeoutId = setTimeout(() => {
          func(...args);
          lastCallTime = Date.now();
          maxTimeoutId = undefined;
        }, maxWait - timeSinceLastCall);
      }
    }
    
    // Set new timeout
    timeoutId = setTimeout(() => {
      if (!immediate) {
        func(...args);
      }
      timeoutId = undefined;
      lastCallTime = undefined;
    }, wait);
    
    // Call immediately if configured
    if (callNow) {
      func(...args);
    }
  };
}

// Angular service with typed debounce
@Injectable({ providedIn: 'root' })
export class SearchService {
  private searchApiCall = debounce(
    (query: string) => this.performSearch(query),
    300,
    { maxWait: 1000 }
  );
  
  search(query: string): void {
    this.searchApiCall(query); // Type-safe call
  }
  
  private performSearch(query: string): void {
    // Actual search implementation
    console.log('Searching for:', query);
  }
}
```

#### **⚡ Performance-Optimized TypeScript Algorithms**
```typescript
// ALGORITHMS OPTIMIZED FOR ANGULAR PERFORMANCE

// 1. MEMOIZED COMPUTATION WITH TYPES
interface MemoCache<TArgs extends readonly unknown[], TReturn> {
  cache: Map<string, TReturn>;
  compute: (...args: TArgs) => TReturn;
}

function memoize<TArgs extends readonly unknown[], TReturn>(
  fn: (...args: TArgs) => TReturn,
  keyGenerator?: (...args: TArgs) => string
): (...args: TArgs) => TReturn {
  const cache = new Map<string, TReturn>();
  
  return (...args: TArgs): TReturn => {
    const key = keyGenerator ? keyGenerator(...args) : JSON.stringify(args);
    
    if (cache.has(key)) {
      return cache.get(key)!;
    }
    
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

// Angular service with memoized expensive operations
@Injectable({ providedIn: 'root' })
export class DataProcessingService {
  // Memoized complex calculation
  private calculateMetrics = memoize(
    (data: number[], type: 'average' | 'median' | 'mode') => {
      console.log('Computing metrics...'); // Only logs on cache miss
      
      switch (type) {
        case 'average':
          return data.reduce((sum, val) => sum + val, 0) / data.length;
        case 'median':
          const sorted = [...data].sort((a, b) => a - b);
          const mid = Math.floor(sorted.length / 2);
          return sorted.length % 2 ? sorted[mid] : (sorted[mid - 1] + sorted[mid]) / 2;
        case 'mode':
          const frequency = new Map<number, number>();
          data.forEach(val => frequency.set(val, (frequency.get(val) || 0) + 1));
          return [...frequency.entries()].reduce((a, b) => a[1] > b[1] ? a : b)[0];
        default:
          throw new Error('Invalid metric type');
      }
    },
    (data, type) => `${type}-${data.join(',')}`
  );
  
  getMetrics(data: number[], type: 'average' | 'median' | 'mode'): number {
    return this.calculateMetrics(data, type);
  }
}

// 2. BINARY SEARCH WITH GENERICS
interface Comparable<T> {
  compare(other: T): number; // -1, 0, 1
}

function binarySearch<T>(
  sortedArray: T[],
  target: T,
  compareFn: (a: T, b: T) => number
): number {
  let left = 0;
  let right = sortedArray.length - 1;
  
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    const comparison = compareFn(sortedArray[mid], target);
    
    if (comparison === 0) {
      return mid;
    } else if (comparison < 0) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  
  return -1; // Not found
}

// Usage with typed objects
interface Product {
  id: string;
  name: string;
  price: number;
}

const products: Product[] = [
  { id: '1', name: 'A', price: 10 },
  { id: '2', name: 'B', price: 20 },
  { id: '3', name: 'C', price: 30 }
];

const targetProduct = { id: '2', name: 'B', price: 20 };
const index = binarySearch(
  products,
  targetProduct,
  (a, b) => a.price - b.price
);

// 3. EFFICIENT DEEP CLONING WITH TYPES
function deepClone<T>(obj: T): T {
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }
  
  if (obj instanceof Date) {
    return new Date(obj.getTime()) as T;
  }
  
  if (obj instanceof Array) {
    return obj.map(item => deepClone(item)) as T;
  }
  
  if (typeof obj === 'object') {
    const clonedObj = {} as T;
    for (const key in obj) {
      if (obj.hasOwnProperty(key)) {
        clonedObj[key] = deepClone(obj[key]);
      }
    }
    return clonedObj;
  }
  
  return obj;
}

// Angular state management with type-safe cloning
@Injectable({ providedIn: 'root' })
export class StateService {
  private userState: User[] = [];
  
  addUser(user: User): User[] {
    // Type-safe deep clone prevents mutations
    const currentState = deepClone(this.userState);
    currentState.push(deepClone(user));
    this.userState = currentState;
    return this.getUserState();
  }
  
  getUserState(): User[] {
    return deepClone(this.userState);
  }
}
```

---

## 🎯 **ADVANCED TYPESCRIPT PATTERNS FOR ANGULAR INTERVIEWS**

### **🔧 Utility Types Mastery**
```typescript
// TYPESCRIPT UTILITY TYPES IN ANGULAR CONTEXT

// 1. PARTIAL FOR UPDATE OPERATIONS
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
  isActive: boolean;
}

// Service method for partial updates
class UserService {
  updateUser(id: string, updates: Partial<User>): Observable<User> {
    // Only required to pass changed fields
    return this.http.patch<User>(`/api/users/${id}`, updates);
  }
}

// 2. PICK FOR FORM SUBSETS
type UserRegistrationForm = Pick<User, 'name' | 'email' | 'age'>;
// Result: { name: string; email: string; age: number; }

// 3. OMIT FOR CREATION OPERATIONS
type CreateUserRequest = Omit<User, 'id'>;
// Result: { name: string; email: string; age: number; isActive: boolean; }

// 4. RECORD FOR DYNAMIC KEY-VALUE PAIRS
type UserPermissions = Record<string, boolean>;
// Usage in component
@Component({
  selector: 'user-permissions',
  template: `
    <div *ngFor="let permission of permissionEntries">
      <label>
        <input 
          type="checkbox" 
          [checked]="permissions[permission.key]"
          (change)="updatePermission(permission.key, $event)">
        {{ permission.label }}
      </label>
    </div>
  `
})
export class UserPermissionsComponent {
  @Input() permissions: UserPermissions = {};
  
  permissionEntries = [
    { key: 'read', label: 'Read Access' },
    { key: 'write', label: 'Write Access' },
    { key: 'delete', label: 'Delete Access' }
  ];
  
  updatePermission(key: string, event: Event): void {
    const checkbox = event.target as HTMLInputElement;
    this.permissions = {
      ...this.permissions,
      [key]: checkbox.checked
    };
  }
}

// 5. CONDITIONAL TYPES FOR ADVANCED PATTERNS
type ApiResponse<T> = T extends string 
  ? { message: T } 
  : T extends number 
    ? { count: T }
    : { data: T };

// Usage examples
type StringResponse = ApiResponse<string>;   // { message: string }
type NumberResponse = ApiResponse<number>;   // { count: number }
type ObjectResponse = ApiResponse<User>;     // { data: User }

// 6. MAPPED TYPES FOR TRANSFORMATION
type Optional<T> = {
  [K in keyof T]?: T[K];
};

type ReadOnly<T> = {
  readonly [K in keyof T]: T[K];
};

// Create readonly version of user for display
type ReadOnlyUser = ReadOnly<User>;
// Result: { readonly id: string; readonly name: string; ... }
```

### **🎭 Type Guards and Type Narrowing**
```typescript
// TYPE GUARDS FOR RUNTIME TYPE SAFETY

// 1. BASIC TYPE GUARDS
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function isUser(obj: unknown): obj is User {
  return typeof obj === 'object' && 
         obj !== null && 
         'id' in obj && 
         'name' in obj && 
         'email' in obj;
}

// 2. DISCRIMINATED UNIONS FOR STATE MANAGEMENT
interface LoadingState {
  status: 'loading';
}

interface SuccessState {
  status: 'success';
  data: User[];
}

interface ErrorState {
  status: 'error';
  error: string;
}

type DataState = LoadingState | SuccessState | ErrorState;

// Component using discriminated unions
@Component({
  selector: 'data-display',
  template: `
    <div [ngSwitch]="state.status">
      <div *ngSwitchCase="'loading'">Loading...</div>
      <div *ngSwitchCase="'success'">
        <user-list [users]="getSuccessData()"></user-list>
      </div>
      <div *ngSwitchCase="'error'">
        Error: {{ getErrorMessage() }}
      </div>
    </div>
  `
})
export class DataDisplayComponent {
  state: DataState = { status: 'loading' };
  
  // Type-safe accessor methods
  getSuccessData(): User[] {
    return this.state.status === 'success' ? this.state.data : [];
  }
  
  getErrorMessage(): string {
    return this.state.status === 'error' ? this.state.error : '';
  }
  
  // Type guard usage
  isSuccessState(state: DataState): state is SuccessState {
    return state.status === 'success';
  }
  
  isErrorState(state: DataState): state is ErrorState {
    return state.status === 'error';
  }
}

// 3. TEMPLATE TYPE GUARDS
class ComponentTypeGuards {
  
  // Type guard for template usage
  asSuccessState(state: DataState): SuccessState | null {
    return this.isSuccessState(state) ? state : null;
  }
  
  asErrorState(state: DataState): ErrorState | null {
    return this.isErrorState(state) ? state : null;
  }
  
  private isSuccessState(state: DataState): state is SuccessState {
    return state.status === 'success';
  }
  
  private isErrorState(state: DataState): state is ErrorState {
    return state.status === 'error';
  }
}
```

---

## 🎬 **REAL TYPESCRIPT INTERVIEW SCENARIOS**

### **📊 Tier 1 Company Scenarios (Google, Microsoft, Meta)**

#### **🔬 SCENARIO: "Design a type-safe event system for Angular components"**
```typescript
// ADVANCED TYPESCRIPT CHALLENGE: Type-safe event emitter system

// Interview expectation: Demonstrate advanced TypeScript knowledge
// with practical Angular application

// 1. DEFINE EVENT MAP INTERFACE
interface ComponentEventMap {
  'user-selected': { user: User; timestamp: Date };
  'data-loaded': { count: number; source: string };
  'error-occurred': { error: Error; context: string };
  'navigation-requested': { route: string; params?: Record<string, unknown> };
}

// 2. GENERIC EVENT EMITTER WITH TYPE SAFETY
class TypeSafeEventEmitter<TEventMap> {
  private listeners = new Map<keyof TEventMap, Function[]>();
  
  // Type-safe event emission
  emit<K extends keyof TEventMap>(
    event: K, 
    data: TEventMap[K]
  ): void {
    const eventListeners = this.listeners.get(event) || [];
    eventListeners.forEach(listener => listener(data));
  }
  
  // Type-safe event listening
  on<K extends keyof TEventMap>(
    event: K,
    listener: (data: TEventMap[K]) => void
  ): () => void {
    const eventListeners = this.listeners.get(event) || [];
    eventListeners.push(listener);
    this.listeners.set(event, eventListeners);
    
    // Return unsubscribe function
    return () => {
      const currentListeners = this.listeners.get(event) || [];
      const index = currentListeners.indexOf(listener);
      if (index > -1) {
        currentListeners.splice(index, 1);
      }
    };
  }
}

// 3. ANGULAR COMPONENT INTEGRATION
@Component({
  selector: 'user-management',
  template: `
    <user-list 
      [users]="users" 
      (userSelected)="onUserSelected($event)">
    </user-list>
    
    <user-details 
      *ngIf="selectedUser"
      [user]="selectedUser">
    </user-details>
  `
})
export class UserManagementComponent implements OnInit, OnDestroy {
  users: User[] = [];
  selectedUser?: User;
  
  private eventEmitter = new TypeSafeEventEmitter<ComponentEventMap>();
  private subscriptions: Array<() => void> = [];
  
  ngOnInit(): void {
    // Type-safe event subscriptions
    this.subscriptions.push(
      this.eventEmitter.on('user-selected', (data) => {
        // data is automatically typed as { user: User; timestamp: Date }
        this.selectedUser = data.user;
        console.log(`User selected at ${data.timestamp}`);
      })
    );
    
    this.subscriptions.push(
      this.eventEmitter.on('error-occurred', (data) => {
        // data is automatically typed as { error: Error; context: string }
        console.error(`Error in ${data.context}:`, data.error);
      })
    );
  }
  
  ngOnDestroy(): void {
    // Clean up subscriptions
    this.subscriptions.forEach(unsubscribe => unsubscribe());
  }
  
  onUserSelected(user: User): void {
    // Type-safe event emission
    this.eventEmitter.emit('user-selected', {
      user,
      timestamp: new Date()
    });
  }
  
  // INTERVIEW DISCUSSION POINTS:
  // - Generic constraints and mapped types
  // - Type inference and autocomplete benefits
  // - Memory management and subscription cleanup
  // - Scalability for large component hierarchies
}

// 4. ADVANCED: CONDITIONAL EVENT TYPES
type ConditionalEventMap<T> = T extends 'admin' 
  ? ComponentEventMap & {
      'admin-action': { action: string; target: string };
    }
  : ComponentEventMap;

// Usage with conditional types
class AdminEventEmitter extends TypeSafeEventEmitter<ConditionalEventMap<'admin'>> {
  // Now has access to admin-specific events
}
```

### **🏢 Tier 2 Company Scenarios (Cognizant, EPAM, Accenture)**

#### **📋 SCENARIO: "Implement type-safe HTTP service with error handling"**
```typescript
// PRACTICAL TYPESCRIPT IMPLEMENTATION: Business-focused HTTP service

// Interview expectation: Practical TypeScript application
// with error handling and business logic

// 1. API RESPONSE TYPES
interface ApiSuccessResponse<T> {
  success: true;
  data: T;
  metadata?: {
    total?: number;
    page?: number;
    limit?: number;
  };
}

interface ApiErrorResponse {
  success: false;
  error: {
    code: string;
    message: string;
    details?: unknown;
  };
}

type ApiResponse<T> = ApiSuccessResponse<T> | ApiErrorResponse;

// 2. BUSINESS DOMAIN TYPES
interface Customer {
  id: string;
  companyName: string;
  contactEmail: string;
  isActive: boolean;
  createdAt: Date;
}

interface CreateCustomerRequest {
  companyName: string;
  contactEmail: string;
}

interface UpdateCustomerRequest extends Partial<CreateCustomerRequest> {
  isActive?: boolean;
}

// 3. TYPE-SAFE HTTP SERVICE
@Injectable({ providedIn: 'root' })
export class CustomerService {
  private readonly baseUrl = '/api/customers';
  
  constructor(private http: HttpClient) {}
  
  // Get all customers with type safety
  getCustomers(): Observable<Customer[]> {
    return this.http.get<ApiResponse<Customer[]>>(this.baseUrl)
      .pipe(
        map(response => this.handleResponse(response)),
        catchError(this.handleHttpError)
      );
  }
  
  // Get customer by ID with error handling
  getCustomerById(id: string): Observable<Customer> {
    return this.http.get<ApiResponse<Customer>>(`${this.baseUrl}/${id}`)
      .pipe(
        map(response => this.handleResponse(response)),
        catchError(this.handleHttpError)
      );
  }
  
  // Create customer with validation
  createCustomer(request: CreateCustomerRequest): Observable<Customer> {
    return this.http.post<ApiResponse<Customer>>(this.baseUrl, request)
      .pipe(
        map(response => this.handleResponse(response)),
        catchError(this.handleHttpError)
      );
  }
  
  // Update customer with partial updates
  updateCustomer(id: string, request: UpdateCustomerRequest): Observable<Customer> {
    return this.http.patch<ApiResponse<Customer>>(`${this.baseUrl}/${id}`, request)
      .pipe(
        map(response => this.handleResponse(response)),
        catchError(this.handleHttpError)
      );
  }
  
  // Type-safe response handler
  private handleResponse<T>(response: ApiResponse<T>): T {
    if (response.success) {
      return response.data;
    } else {
      throw new BusinessError(response.error.code, response.error.message);
    }
  }
  
  // HTTP error handler
  private handleHttpError(error: HttpErrorResponse): Observable<never> {
    let businessError: BusinessError;
    
    if (error.status === 0) {
      businessError = new BusinessError('NETWORK_ERROR', 'Network connection failed');
    } else if (error.status >= 400 && error.status < 500) {
      businessError = new BusinessError('CLIENT_ERROR', error.message);
    } else if (error.status >= 500) {
      businessError = new BusinessError('SERVER_ERROR', 'Internal server error');
    } else {
      businessError = new BusinessError('UNKNOWN_ERROR', 'An unexpected error occurred');
    }
    
    return throwError(() => businessError);
  }
}

// 4. CUSTOM ERROR TYPES
class BusinessError extends Error {
  constructor(
    public readonly code: string,
    message: string,
    public readonly details?: unknown
  ) {
    super(message);
    this.name = 'BusinessError';
  }
}

// 5. COMPONENT INTEGRATION WITH TYPE SAFETY
@Component({
  selector: 'customer-list',
  template: `
    <div class="customer-list">
      <div *ngIf="loading">Loading customers...</div>
      <div *ngIf="error" class="error">{{ error.message }}</div>
      
      <div *ngIf="customers.length > 0" class="customers">
        <div *ngFor="let customer of customers; trackBy: trackByCustomerId" 
             class="customer-card"
             [class.inactive]="!customer.isActive">
          <h3>{{ customer.companyName }}</h3>
          <p>{{ customer.contactEmail }}</p>
          <button (click)="editCustomer(customer)">Edit</button>
        </div>
      </div>
    </div>
  `
})
export class CustomerListComponent implements OnInit {
  customers: Customer[] = [];
  loading = false;
  error?: BusinessError;
  
  constructor(private customerService: CustomerService) {}
  
  ngOnInit(): void {
    this.loadCustomers();
  }
  
  private loadCustomers(): void {
    this.loading = true;
    this.error = undefined;
    
    this.customerService.getCustomers()
      .subscribe({
        next: (customers) => {
          this.customers = customers;
          this.loading = false;
        },
        error: (error: BusinessError) => {
          this.error = error;
          this.loading = false;
          console.error('Failed to load customers:', error);
        }
      });
  }
  
  editCustomer(customer: Customer): void {
    // Type-safe navigation with customer data
    console.log('Editing customer:', customer.companyName);
  }
  
  trackByCustomerId(index: number, customer: Customer): string {
    return customer.id;
  }
  
  // INTERVIEW DISCUSSION POINTS:
  // - Type safety prevents runtime errors
  // - Error handling strategy with custom types
  // - Reusable service patterns for business logic
  // - Component-service communication patterns
}
```

### **🚀 Tier 3 Company Scenarios (Startups, Agencies)**

#### **⚡ SCENARIO: "Quick TypeScript setup for rapid Angular development"**
```typescript
// PRAGMATIC TYPESCRIPT: Fast development with gradual typing

// Interview expectation: Practical TypeScript knowledge
// for rapid development and small team productivity

// 1. SIMPLE TYPE DEFINITIONS FOR IMMEDIATE VALUE
interface User {
  id: string;
  name: string;
  email: string;
}

interface Product {
  id: string;
  title: string;
  price: number;
  inStock: boolean;
}

// 2. UTILITY TYPES FOR COMMON PATTERNS
type ApiData<T> = {
  data: T;
  loading: boolean;
  error?: string;
};

// Quick component state management
@Component({
  selector: 'product-catalog',
  template: `
    <div class="catalog">
      <div *ngIf="productState.loading">Loading products...</div>
      <div *ngIf="productState.error">Error: {{ productState.error }}</div>
      
      <div *ngFor="let product of productState.data" class="product">
        <h3>{{ product.title }}</h3>
        <p>Price: {{ product.price | currency }}</p>
        <p [class.out-of-stock]="!product.inStock">
          {{ product.inStock ? 'In Stock' : 'Out of Stock' }}
        </p>
        <button 
          [disabled]="!product.inStock"
          (click)="addToCart(product)">
          Add to Cart
        </button>
      </div>
    </div>
  `
})
export class ProductCatalogComponent implements OnInit {
  productState: ApiData<Product[]> = {
    data: [],
    loading: false
  };
  
  constructor(private http: HttpClient) {}
  
  ngOnInit(): void {
    this.loadProducts();
  }
  
  private loadProducts(): void {
    this.productState.loading = true;
    this.productState.error = undefined;
    
    this.http.get<Product[]>('/api/products')
      .subscribe({
        next: (products) => {
          this.productState = {
            data: products,
            loading: false
          };
        },
        error: (error) => {
          this.productState = {
            data: [],
            loading: false,
            error: 'Failed to load products'
          };
        }
      });
  }
  
  addToCart(product: Product): void {
    if (product.inStock) {
      console.log('Adding to cart:', product.title);
      // Type-safe product handling
    }
  }
  
  // STARTUP BENEFITS:
  // - Quick setup with immediate type safety
  // - Simple patterns that new developers can follow
  // - Gradual typing allows for rapid iteration
  // - IntelliSense improves developer productivity
}

// 3. PROGRESSIVE ENHANCEMENT STRATEGY
interface BaseEntity {
  id: string;
  createdAt?: Date;
  updatedAt?: Date;
}

// Start simple, add complexity as needed
interface SimpleProduct extends BaseEntity {
  title: string;
  price: number;
}

// Enhance when requirements grow
interface EnhancedProduct extends SimpleProduct {
  description: string;
  category: string;
  tags: string[];
  images: string[];
  variants: ProductVariant[];
}

interface ProductVariant {
  id: string;
  name: string;
  price: number;
  inStock: boolean;
}

// 4. QUICK FORM TYPING
interface ContactForm {
  name: string;
  email: string;
  message: string;
}

@Component({
  selector: 'contact-form',
  template: `
    <form [formGroup]="contactForm" (ngSubmit)="onSubmit()">
      <input formControlName="name" placeholder="Your Name" required>
      <input formControlName="email" type="email" placeholder="Email" required>
      <textarea formControlName="message" placeholder="Message" required></textarea>
      <button type="submit" [disabled]="contactForm.invalid">Send</button>
    </form>
  `
})
export class ContactFormComponent {
  contactForm: FormGroup;
  
  constructor(private fb: FormBuilder) {
    this.contactForm = this.fb.group({
      name: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]],
      message: ['', Validators.required]
    });
  }
  
  onSubmit(): void {
    if (this.contactForm.valid) {
      const formData: ContactForm = this.contactForm.value;
      console.log('Form submitted:', formData);
      // Type-safe form handling
    }
  }
}
```

---

## 🔥 **LIVE CODING CHALLENGES BY COMPANY TIER**

### **🏆 Tier 1 Company Challenges (Google, Microsoft, Meta)**

#### **🎯 CHALLENGE 1: "Design a Type-Safe State Management System"**
```typescript
// INTERVIEWER: "Create a Redux-like state management system with full TypeScript support"
// TIME LIMIT: 45 minutes
// EVALUATION: Type safety, architecture design, error handling

// State definition with strict typing
interface AppState {
  users: UserState;
  products: ProductState;
  ui: UIState;
}

interface UserState {
  entities: Record<string, User>;
  loading: boolean;
  error?: string;
  selectedUserId?: string;
}

interface ProductState {
  entities: Record<string, Product>;
  categories: string[];
  filters: ProductFilters;
}

interface UIState {
  theme: 'light' | 'dark';
  sidebarOpen: boolean;
  notifications: Notification[];
}

// Action system with discriminated unions
interface SetUsersAction {
  type: 'SET_USERS';
  payload: User[];
}

interface SetLoadingAction {
  type: 'SET_LOADING';
  payload: boolean;
}

interface SetErrorAction {
  type: 'SET_ERROR';
  payload: string;
}

interface SelectUserAction {
  type: 'SELECT_USER';
  payload: string;
}

type UserActions = SetUsersAction | SetLoadingAction | SetErrorAction | SelectUserAction;
type AllActions = UserActions; // Extend as needed

// Type-safe reducer
function userReducer(state: UserState, action: UserActions): UserState {
  switch (action.type) {
    case 'SET_USERS':
      return {
        ...state,
        entities: action.payload.reduce((acc, user) => {
          acc[user.id] = user;
          return acc;
        }, {} as Record<string, User>),
        loading: false,
        error: undefined
      };
    
    case 'SET_LOADING':
      return {
        ...state,
        loading: action.payload
      };
    
    case 'SET_ERROR':
      return {
        ...state,
        error: action.payload,
        loading: false
      };
    
    case 'SELECT_USER':
      return {
        ...state,
        selectedUserId: action.payload
      };
    
    default:
      return state;
  }
}

// Type-safe store implementation
class TypeSafeStore<TState, TAction> {
  private state: TState;
  private listeners: Array<(state: TState) => void> = [];
  
  constructor(
    private reducer: (state: TState, action: TAction) => TState,
    initialState: TState
  ) {
    this.state = initialState;
  }
  
  getState(): TState {
    return this.state;
  }
  
  dispatch(action: TAction): void {
    this.state = this.reducer(this.state, action);
    this.listeners.forEach(listener => listener(this.state));
  }
  
  subscribe(listener: (state: TState) => void): () => void {
    this.listeners.push(listener);
    return () => {
      const index = this.listeners.indexOf(listener);
      if (index > -1) {
        this.listeners.splice(index, 1);
      }
    };
  }
}

// Angular service integration
@Injectable({ providedIn: 'root' })
export class StoreService {
  private store = new TypeSafeStore(
    userReducer,
    {
      entities: {},
      loading: false,
      selectedUserId: undefined
    } as UserState
  );
  
  // Typed selectors
  selectUsers(): Observable<User[]> {
    return new Observable(observer => {
      const unsubscribe = this.store.subscribe(state => {
        observer.next(Object.values(state.entities));
      });
      
      // Initial emit
      observer.next(Object.values(this.store.getState().entities));
      
      return unsubscribe;
    });
  }
  
  selectSelectedUser(): Observable<User | undefined> {
    return new Observable(observer => {
      const unsubscribe = this.store.subscribe(state => {
        const selectedUser = state.selectedUserId 
          ? state.entities[state.selectedUserId] 
          : undefined;
        observer.next(selectedUser);
      });
      
      return unsubscribe;
    });
  }
  
  // Action creators
  setUsers(users: User[]): void {
    this.store.dispatch({ type: 'SET_USERS', payload: users });
  }
  
  selectUser(userId: string): void {
    this.store.dispatch({ type: 'SELECT_USER', payload: userId });
  }
  
  setLoading(loading: boolean): void {
    this.store.dispatch({ type: 'SET_LOADING', payload: loading });
  }
}

// INTERVIEW DISCUSSION POINTS:
// - How does TypeScript ensure action type safety?
// - What are the performance implications of this design?
// - How would you add middleware support?
// - How does this compare to NgRx?
// - How would you handle async actions?
```

#### **🎯 CHALLENGE 2: "Implement Advanced Generic Constraints"**
```typescript
// INTERVIEWER: "Create a type-safe ORM-like query builder"
// TIME LIMIT: 30 minutes
// EVALUATION: Advanced generics, conditional types, mapped types

// Entity base interface
interface Entity {
  id: string;
  createdAt: Date;
  updatedAt: Date;
}

// Sample entities
interface User extends Entity {
  name: string;
  email: string;
  age: number;
  isActive: boolean;
}

interface Product extends Entity {
  title: string;
  price: number;
  category: string;
  inStock: boolean;
}

// Advanced type utilities
type Primitive = string | number | boolean | Date;

type FieldsOfType<T, U> = {
  [K in keyof T]: T[K] extends U ? K : never;
}[keyof T];

type StringFields<T> = FieldsOfType<T, string>;
type NumberFields<T> = FieldsOfType<T, number>;
type BooleanFields<T> = FieldsOfType<T, boolean>;

// Query operators
interface QueryOperators<T> {
  $eq?: T;
  $ne?: T;
  $in?: T[];
  $nin?: T[];
  $gt?: T extends number ? T : never;
  $gte?: T extends number ? T : never;
  $lt?: T extends number ? T : never;
  $lte?: T extends number ? T : never;
  $contains?: T extends string ? string : never;
  $startsWith?: T extends string ? string : never;
}

type QueryCondition<T> = {
  [K in keyof T]?: T[K] | QueryOperators<T[K]>;
};

// Query builder
class QueryBuilder<T extends Entity> {
  private conditions: QueryCondition<T>[] = [];
  private _select?: (keyof T)[];
  private _limit?: number;
  private _skip?: number;
  private _orderBy?: { field: keyof T; direction: 'asc' | 'desc' }[];
  
  where(condition: QueryCondition<T>): QueryBuilder<T> {
    this.conditions.push(condition);
    return this;
  }
  
  select<K extends keyof T>(...fields: K[]): QueryBuilder<Pick<T, K>> {
    this._select = fields as (keyof T)[];
    return this as any;
  }
  
  orderBy<K extends keyof T>(
    field: K,
    direction: 'asc' | 'desc' = 'asc'
  ): QueryBuilder<T> {
    if (!this._orderBy) this._orderBy = [];
    this._orderBy.push({ field, direction });
    return this;
  }
  
  limit(count: number): QueryBuilder<T> {
    this._limit = count;
    return this;
  }
  
  skip(count: number): QueryBuilder<T> {
    this._skip = count;
    return this;
  }
  
  // Execute query (simplified implementation)
  execute(data: T[]): T[] {
    let result = data.filter(item => 
      this.conditions.every(condition => this.matchesCondition(item, condition))
    );
    
    if (this._orderBy) {
      result.sort((a, b) => {
        for (const order of this._orderBy!) {
          const aVal = a[order.field];
          const bVal = b[order.field];
          
          if (aVal < bVal) return order.direction === 'asc' ? -1 : 1;
          if (aVal > bVal) return order.direction === 'asc' ? 1 : -1;
        }
        return 0;
      });
    }
    
    if (this._skip) {
      result = result.slice(this._skip);
    }
    
    if (this._limit) {
      result = result.slice(0, this._limit);
    }
    
    return result;
  }
  
  private matchesCondition(item: T, condition: QueryCondition<T>): boolean {
    return Object.entries(condition).every(([key, value]) => {
      const itemValue = item[key as keyof T];
      
      if (typeof value === 'object' && value !== null && !(value instanceof Date)) {
        const operators = value as QueryOperators<any>;
        
        if ('$eq' in operators) return itemValue === operators.$eq;
        if ('$ne' in operators) return itemValue !== operators.$ne;
        if ('$in' in operators) return operators.$in!.includes(itemValue);
        if ('$nin' in operators) return !operators.$nin!.includes(itemValue);
        if ('$gt' in operators) return (itemValue as number) > operators.$gt!;
        if ('$gte' in operators) return (itemValue as number) >= operators.$gte!;
        if ('$lt' in operators) return (itemValue as number) < operators.$lt!;
        if ('$lte' in operators) return (itemValue as number) <= operators.$lte!;
        if ('$contains' in operators) return String(itemValue).includes(operators.$contains!);
        if ('$startsWith' in operators) return String(itemValue).startsWith(operators.$startsWith!);
      }
      
      return itemValue === value;
    });
  }
}

// Type-safe repository
class Repository<T extends Entity> {
  constructor(private data: T[]) {}
  
  query(): QueryBuilder<T> {
    return new QueryBuilder<T>();
  }
  
  find(query: QueryCondition<T>): T[] {
    return this.query().where(query).execute(this.data);
  }
  
  findById(id: string): T | undefined {
    return this.data.find(item => item.id === id);
  }
}

// Usage examples with full type safety
const userRepo = new Repository<User>([
  { id: '1', name: 'John', email: 'john@test.com', age: 30, isActive: true, createdAt: new Date(), updatedAt: new Date() }
]);

// Type-safe queries with autocomplete
const activeAdults = userRepo
  .query()
  .where({ isActive: true })
  .where({ age: { $gte: 18 } })
  .where({ name: { $contains: 'Jo' } })
  .orderBy('age', 'desc')
  .limit(10)
  .execute(userRepo['data']);

// INTERVIEW DISCUSSION POINTS:
// - How do conditional types improve API design?
// - What are the performance implications of this approach?
// - How would you handle relationships between entities?
// - How does this compare to TypeORM or Prisma?
```

### **🏢 Tier 2 Company Challenges (Cognizant, EPAM, Accenture)**

#### **🎯 CHALLENGE 3: "Build Type-Safe Form Validation System"**
```typescript
// INTERVIEWER: "Create a reusable form validation system with TypeScript"
// TIME LIMIT: 30 minutes
// EVALUATION: Practical TypeScript, form handling, validation patterns

// Validation rule definitions
interface ValidationRule<T = any> {
  validate: (value: T) => boolean;
  message: string;
}

interface FieldValidation<T = any> {
  required?: boolean;
  rules?: ValidationRule<T>[];
}

type FormValidationSchema<T> = {
  [K in keyof T]?: FieldValidation<T[K]>;
};

interface ValidationResult {
  isValid: boolean;
  errors: Record<string, string[]>;
}

// Common validation rules
const ValidationRules = {
  email: (): ValidationRule<string> => ({
    validate: (value: string) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value),
    message: 'Please enter a valid email address'
  }),
  
  minLength: (min: number): ValidationRule<string> => ({
    validate: (value: string) => value.length >= min,
    message: `Must be at least ${min} characters long`
  }),
  
  maxLength: (max: number): ValidationRule<string> => ({
    validate: (value: string) => value.length <= max,
    message: `Must be no more than ${max} characters long`
  }),
  
  min: (min: number): ValidationRule<number> => ({
    validate: (value: number) => value >= min,
    message: `Must be at least ${min}`
  }),
  
  max: (max: number): ValidationRule<number> => ({
    validate: (value: number) => value <= max,
    message: `Must be no more than ${max}`
  }),
  
  pattern: (pattern: RegExp, message: string): ValidationRule<string> => ({
    validate: (value: string) => pattern.test(value),
    message
  })
};

// Form validator class
class FormValidator<T extends Record<string, any>> {
  constructor(private schema: FormValidationSchema<T>) {}
  
  validate(data: Partial<T>): ValidationResult {
    const errors: Record<string, string[]> = {};
    let isValid = true;
    
    // Check required fields
    Object.keys(this.schema).forEach(fieldName => {
      const field = this.schema[fieldName as keyof T];
      const value = data[fieldName as keyof T];
      
      if (field?.required && (value === undefined || value === null || value === '')) {
        errors[fieldName] = ['This field is required'];
        isValid = false;
        return;
      }
      
      // Skip validation if field is not required and empty
      if (!field?.required && (value === undefined || value === null || value === '')) {
        return;
      }
      
      // Run validation rules
      if (field?.rules) {
        const fieldErrors: string[] = [];
        
        field.rules.forEach(rule => {
          if (!rule.validate(value)) {
            fieldErrors.push(rule.message);
            isValid = false;
          }
        });
        
        if (fieldErrors.length > 0) {
          errors[fieldName] = fieldErrors;
        }
      }
    });
    
    return { isValid, errors };
  }
  
  validateField(fieldName: keyof T, value: T[keyof T]): string[] {
    const field = this.schema[fieldName];
    const errors: string[] = [];
    
    if (field?.required && (value === undefined || value === null || value === '')) {
      errors.push('This field is required');
      return errors;
    }
    
    if (!field?.required && (value === undefined || value === null || value === '')) {
      return errors;
    }
    
    if (field?.rules) {
      field.rules.forEach(rule => {
        if (!rule.validate(value)) {
          errors.push(rule.message);
        }
      });
    }
    
    return errors;
  }
}

// Form data interfaces
interface UserRegistrationForm {
  firstName: string;
  lastName: string;
  email: string;
  password: string;
  confirmPassword: string;
  age: number;
  terms: boolean;
}

// Validation schema definition
const userRegistrationSchema: FormValidationSchema<UserRegistrationForm> = {
  firstName: {
    required: true,
    rules: [
      ValidationRules.minLength(2),
      ValidationRules.maxLength(50),
      ValidationRules.pattern(/^[a-zA-Z\s]+$/, 'Only letters and spaces allowed')
    ]
  },
  lastName: {
    required: true,
    rules: [
      ValidationRules.minLength(2),
      ValidationRules.maxLength(50),
      ValidationRules.pattern(/^[a-zA-Z\s]+$/, 'Only letters and spaces allowed')
    ]
  },
  email: {
    required: true,
    rules: [ValidationRules.email()]
  },
  password: {
    required: true,
    rules: [
      ValidationRules.minLength(8),
      ValidationRules.pattern(
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/,
        'Password must contain uppercase, lowercase, number, and special character'
      )
    ]
  },
  confirmPassword: {
    required: true,
    rules: [
      {
        validate: (value: string) => {
          // Note: In real implementation, you'd have access to the full form
          return true; // Simplified for example
        },
        message: 'Passwords must match'
      }
    ]
  },
  age: {
    required: true,
    rules: [
      ValidationRules.min(18),
      ValidationRules.max(120)
    ]
  },
  terms: {
    required: true,
    rules: [
      {
        validate: (value: boolean) => value === true,
        message: 'You must agree to the terms and conditions'
      }
    ]
  }
};

// Angular component integration
@Component({
  selector: 'user-registration',
  template: `
    <form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">
      <div class="form-field">
        <label>First Name</label>
        <input formControlName="firstName" type="text">
        <div *ngIf="getFieldErrors('firstName').length > 0" class="error">
          <div *ngFor="let error of getFieldErrors('firstName')">{{ error }}</div>
        </div>
      </div>
      
      <div class="form-field">
        <label>Last Name</label>
        <input formControlName="lastName" type="text">
        <div *ngIf="getFieldErrors('lastName').length > 0" class="error">
          <div *ngFor="let error of getFieldErrors('lastName')">{{ error }}</div>
        </div>
      </div>
      
      <div class="form-field">
        <label>Email</label>
        <input formControlName="email" type="email">
        <div *ngIf="getFieldErrors('email').length > 0" class="error">
          <div *ngFor="let error of getFieldErrors('email')">{{ error }}</div>
        </div>
      </div>
      
      <button type="submit" [disabled]="!isFormValid">Register</button>
    </form>
  `
})
export class UserRegistrationComponent implements OnInit {
  registrationForm: FormGroup;
  private validator = new FormValidator(userRegistrationSchema);
  private validationResult: ValidationResult = { isValid: false, errors: {} };
  
  constructor(private fb: FormBuilder) {
    this.registrationForm = this.createForm();
  }
  
  ngOnInit(): void {
    this.registrationForm.valueChanges.subscribe(() => {
      this.validateForm();
    });
  }
  
  private createForm(): FormGroup {
    return this.fb.group({
      firstName: [''],
      lastName: [''],
      email: [''],
      password: [''],
      confirmPassword: [''],
      age: [null],
      terms: [false]
    });
  }
  
  private validateForm(): void {
    const formValue: Partial<UserRegistrationForm> = this.registrationForm.value;
    this.validationResult = this.validator.validate(formValue);
  }
  
  getFieldErrors(fieldName: keyof UserRegistrationForm): string[] {
    return this.validationResult.errors[fieldName] || [];
  }
  
  get isFormValid(): boolean {
    return this.validationResult.isValid;
  }
  
  onSubmit(): void {
    if (this.isFormValid) {
      const formData: UserRegistrationForm = this.registrationForm.value;
      console.log('Registration data:', formData);
      // Process registration
    }
  }
  
  // INTERVIEW DISCUSSION POINTS:
  // - How does TypeScript ensure validation schema matches form structure?
  // - What are the benefits of separating validation logic from Angular forms?
  // - How would you handle async validation?
  // - How would you make this work with template-driven forms?
  // - How would you add cross-field validation?
}
```

### **🚀 Tier 3 Company Challenges (Startups, Agencies)**

#### **🎯 CHALLENGE 4: "Type-Safe Event Communication System"**
```typescript
// INTERVIEWER: "Build a simple but type-safe event system for component communication"
// TIME LIMIT: 20 minutes
// EVALUATION: Basic TypeScript, practical problem solving

// Event definitions
interface AppEvents {
  'user-login': { userId: string; timestamp: Date };
  'user-logout': { userId: string };
  'data-refresh': { source: string; count: number };
  'notification-show': { message: string; type: 'info' | 'warning' | 'error' };
  'modal-open': { modalId: string; data?: any };
  'modal-close': { modalId: string };
}

// Type-safe event emitter
class EventBus {
  private listeners = new Map<keyof AppEvents, Function[]>();
  
  // Type-safe event emission
  emit<K extends keyof AppEvents>(event: K, data: AppEvents[K]): void {
    const eventListeners = this.listeners.get(event) || [];
    eventListeners.forEach(listener => {
      try {
        listener(data);
      } catch (error) {
        console.error(`Error in event listener for ${event}:`, error);
      }
    });
  }
  
  // Type-safe event listening
  on<K extends keyof AppEvents>(
    event: K,
    listener: (data: AppEvents[K]) => void
  ): () => void {
    const eventListeners = this.listeners.get(event) || [];
    eventListeners.push(listener);
    this.listeners.set(event, eventListeners);
    
    // Return unsubscribe function
    return () => {
      const currentListeners = this.listeners.get(event) || [];
      const index = currentListeners.indexOf(listener);
      if (index > -1) {
        currentListeners.splice(index, 1);
      }
    };
  }
  
  // Remove all listeners for an event
  off<K extends keyof AppEvents>(event: K): void {
    this.listeners.delete(event);
  }
  
  // Get listener count for debugging
  getListenerCount<K extends keyof AppEvents>(event: K): number {
    return (this.listeners.get(event) || []).length;
  }
}

// Angular service
@Injectable({ providedIn: 'root' })
export class EventBusService {
  private eventBus = new EventBus();
  
  emit<K extends keyof AppEvents>(event: K, data: AppEvents[K]): void {
    this.eventBus.emit(event, data);
  }
  
  on<K extends keyof AppEvents>(
    event: K,
    listener: (data: AppEvents[K]) => void
  ): () => void {
    return this.eventBus.on(event, listener);
  }
  
  off<K extends keyof AppEvents>(event: K): void {
    this.eventBus.off(event);
  }
}

// Component examples
@Component({
  selector: 'login-form',
  template: `
    <form (ngSubmit)="onLogin()">
      <input [(ngModel)]="username" placeholder="Username">
      <input [(ngModel)]="password" type="password" placeholder="Password">
      <button type="submit">Login</button>
    </form>
  `
})
export class LoginFormComponent {
  username = '';
  password = '';
  
  constructor(private eventBus: EventBusService) {}
  
  onLogin(): void {
    // Simulate login
    const userId = 'user-123';
    
    // Type-safe event emission
    this.eventBus.emit('user-login', {
      userId,
      timestamp: new Date()
    });
    
    this.eventBus.emit('notification-show', {
      message: 'Login successful!',
      type: 'info'
    });
  }
}

@Component({
  selector: 'notification-display',
  template: `
    <div *ngIf="notification" 
         [class]="'notification notification-' + notification.type">
      {{ notification.message }}
      <button (click)="dismiss()">×</button>
    </div>
  `
})
export class NotificationComponent implements OnInit, OnDestroy {
  notification?: { message: string; type: string };
  private unsubscribe?: () => void;
  
  constructor(private eventBus: EventBusService) {}
  
  ngOnInit(): void {
    // Type-safe event listening
    this.unsubscribe = this.eventBus.on('notification-show', (data) => {
      // data is automatically typed as { message: string; type: 'info' | 'warning' | 'error' }
      this.notification = data;
      
      // Auto-dismiss after 3 seconds
      setTimeout(() => this.dismiss(), 3000);
    });
  }
  
  ngOnDestroy(): void {
    if (this.unsubscribe) {
      this.unsubscribe();
    }
  }
  
  dismiss(): void {
    this.notification = undefined;
  }
}

@Component({
  selector: 'user-dashboard',
  template: `
    <div class="dashboard">
      <h1>Welcome, {{ username }}!</h1>
      <button (click)="refreshData()">Refresh</button>
      <button (click)="logout()">Logout</button>
    </div>
  `
})
export class UserDashboardComponent implements OnInit, OnDestroy {
  username = '';
  private unsubscribes: Array<() => void> = [];
  
  constructor(private eventBus: EventBusService) {}
  
  ngOnInit(): void {
    // Listen for login events
    this.unsubscribes.push(
      this.eventBus.on('user-login', (data) => {
        this.username = `User ${data.userId}`;
        console.log(`User logged in at ${data.timestamp}`);
      })
    );
    
    // Listen for logout events
    this.unsubscribes.push(
      this.eventBus.on('user-logout', (data) => {
        this.username = '';
        console.log(`User ${data.userId} logged out`);
      })
    );
    
    // Listen for data refresh events
    this.unsubscribes.push(
      this.eventBus.on('data-refresh', (data) => {
        console.log(`Data refreshed from ${data.source}, ${data.count} items`);
      })
    );
  }
  
  ngOnDestroy(): void {
    this.unsubscribes.forEach(unsubscribe => unsubscribe());
  }
  
  refreshData(): void {
    // Simulate data refresh
    this.eventBus.emit('data-refresh', {
      source: 'dashboard',
      count: 42
    });
  }
  
  logout(): void {
    this.eventBus.emit('user-logout', {
      userId: 'user-123'
    });
  }
  
  // INTERVIEW DISCUSSION POINTS:
  // - How does TypeScript ensure event data consistency?
  // - What are the benefits of centralized event management?
  // - How would you handle event debugging and logging?
  // - How does this compare to @Output() events?
  // - What are potential memory leak concerns?
}
```

---

## ⚡ **RAPID-FIRE TYPESCRIPT QUESTIONS**

### **🔥 30-Second Response Preparation**
```typescript
// QUESTION: "What's the difference between interface and type in TypeScript?"
RESPONSE: "Interfaces are extendable and merged, ideal for object shapes and class contracts. 
Types are more flexible for unions, intersections, and computed types. Use interfaces 
for Angular service contracts and component props, types for state unions and utility types."

// QUESTION: "How do you handle optional properties in TypeScript?"
RESPONSE: "Use the ? operator for optional properties (name?: string), Partial<T> utility 
type for all optional, or union with undefined. In Angular, this is crucial for component 
inputs and API responses where data might be incomplete."

// QUESTION: "Explain generics and when you'd use them in Angular."
RESPONSE: "Generics enable type-safe reusable code. In Angular, use them for reusable 
components like data tables, service interfaces for different entities, and Observable 
streams to maintain type safety through transformation chains."

// QUESTION: "How do you ensure type safety with HTTP responses?"
RESPONSE: "Define response interfaces, use generic HttpClient methods, implement type 
guards for runtime validation, and create wrapper types for API responses with success/error 
discrimination to handle all response scenarios type-safely."

// QUESTION: "What are utility types and name your most used ones?"
RESPONSE: "Built-in type transformations: Partial for updates, Pick for subsets, Omit for 
creation without IDs, Record for key-value pairs, and Required for making all properties 
mandatory. Essential for Angular form handling and API operations."
```

---

## 🚀 **WHY-WHAT-WHEN TYPESCRIPT MASTERY FRAMEWORK**

### **🎯 Why Framework: TypeScript Strategic Value**

#### **💰 TypeScript ROI in Angular Development**
```typescript
interface TypeScriptROI {
  bugReduction: {
    compileTime: '90%'; // Catch errors before runtime
    runtime: '70%';     // Prevent type-related runtime errors
    integration: '80%'; // Interface contract violations
  };
  
  developmentEfficiency: {
    intelliSense: '+200%'; // Autocomplete and navigation
    refactoring: '+300%';  // Safe large-scale changes
    debugging: '+150%';    // Type-guided error location
  };
  
  teamProductivity: {
    onboarding: '+250%';   // Self-documenting code
    collaboration: '+180%'; // Clear interface contracts
    maintenance: '+300%';   // Easier code understanding
  };
  
  businessImpact: {
    timeToMarket: '-30%';  // Faster development cycles
    qualityScore: '+200%'; // Fewer production bugs
    scalability: '+400%';  // Maintainable large codebases
  };
}
```

### **🛠️ What Framework: TypeScript Implementation Patterns**

#### **📋 Progressive TypeScript Adoption Strategy**
```typescript
// PHASE 1: Basic Type Adoption (Week 1-2)
interface Phase1Strategy {
  focus: 'Essential type annotations and interfaces';
  implementation: [
    'Component input/output typing',
    'Service method signatures',
    'Basic interface definitions',
    'HTTP response types'
  ];
  successMetrics: [
    'Zero implicit any errors',
    'All component props typed',
    'Service interfaces defined',
    'API response models created'
  ];
}

// PHASE 2: Intermediate Patterns (Week 3-4)
interface Phase2Strategy {
  focus: 'Utility types and generic patterns';
  implementation: [
    'Generic service implementations',
    'Utility type usage (Partial, Pick, Omit)',
    'Union types for state management',
    'Type guards for runtime safety'
  ];
  successMetrics: [
    'Reusable generic components',
    'Type-safe state transitions',
    'Runtime type validation',
    'Flexible API handling'
  ];
}

// PHASE 3: Advanced Patterns (Week 5-6)
interface Phase3Strategy {
  focus: 'Advanced type system features';
  implementation: [
    'Conditional types for complex logic',
    'Mapped types for transformations',
    'Template literal types',
    'Advanced generic constraints'
  ];
  successMetrics: [
    'Type-level programming',
    'Complex business logic typing',
    'Framework-like abstractions',
    'Domain-specific type languages'
  ];
}
```

### **⏰ When Framework: TypeScript Implementation Timing**

#### **📅 TypeScript Migration Decision Matrix**
```typescript
interface MigrationDecisionFramework {
  projectSize: 'small' | 'medium' | 'large';
  teamExperience: 'junior' | 'mixed' | 'senior';
  timeConstraints: 'tight' | 'moderate' | 'flexible';
  recommendation: MigrationStrategy;
}

type MigrationStrategy = {
  approach: string;
  timeline: string;
  riskLevel: 'low' | 'medium' | 'high';
  expectedROI: string;
};

const migrationDecisions: MigrationDecisionFramework[] = [
  {
    projectSize: 'small',
    teamExperience: 'junior',
    timeConstraints: 'tight',
    recommendation: {
      approach: 'Gradual typing with any escape hatches',
      timeline: '2-3 weeks',
      riskLevel: 'low',
      expectedROI: 'Immediate development assistance'
    }
  },
  {
    projectSize: 'large',
    teamExperience: 'senior',
    timeConstraints: 'flexible',
    recommendation: {
      approach: 'Strict typing from start with comprehensive patterns',
      timeline: '6-8 weeks',
      riskLevel: 'medium',
      expectedROI: 'Long-term maintainability and scalability'
    }
  }
];
```

---

## 🏆 **TYPESCRIPT MASTERY VALIDATION**

### **✅ Interview Readiness Checklist**
- [ ] Can explain interface vs type differences with examples
- [ ] Demonstrates generic usage in Angular context
- [ ] Shows utility type mastery for real scenarios
- [ ] Implements type guards for runtime safety
- [ ] Creates type-safe service interfaces
- [ ] Handles complex state typing with unions
- [ ] Applies advanced patterns appropriately

### **🎯 Company-Tier Readiness Assessment**
- [ ] **Tier 1**: Advanced type-level programming and conditional types
- [ ] **Tier 2**: Practical interface design and error handling patterns
- [ ] **Tier 3**: Basic type safety with immediate productivity benefits

---

## 🔗 **INTEGRATION WITH OTHER SECTIONS**

### **IMMEDIATE CONNECTIONS**
- **[01-10 Algorithm Fundamentals](../01-Interview-Essentials/01-10-algorithm-fundamentals.md)** - TypeScript algorithm implementations
- **[01-04 Services & DI](../01-Interview-Essentials/01-04-services-dependency-injection.md)** - Service interface typing
- **[01-06 Forms & Validation](../01-Interview-Essentials/01-06-forms-validation.md)** - Form typing patterns

### **UPCOMING CONNECTIONS**
- **03-02 JavaScript Core Concepts** - Language foundation integration
- **03-06 Algorithm Data Structures** - TypeScript algorithm patterns
- **04-01 Change Detection Performance** - Type-driven optimization

---

*🔷 Master these TypeScript fundamentals to provide the language foundation that elevates your Angular expertise from good to exceptional!* 🚀
