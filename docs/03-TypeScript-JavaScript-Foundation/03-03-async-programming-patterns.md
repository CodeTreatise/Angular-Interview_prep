# 🔄 Async Programming Patterns - Chapter 03-03

> **Master Asynchronous JavaScript for Angular Excellence** - From Promises to Observables, build expertise in async patterns that power modern Angular applications and ace technical interviews.

---

## 🎯 **CHAPTER OVERVIEW**

Asynchronous programming is the backbone of modern web applications. This chapter transforms you from someone who "uses async/await" to an expert who architects robust, performant async systems that interviewers recognize as senior-level thinking.

### **🎪 Why This Chapter Matters**
- **🎯 Interview Reality**: 78% of Angular interviews test async pattern knowledge
- **💼 Career Impact**: Async mastery differentiates mid-level from senior developers  
- **🚀 Performance**: Proper async patterns improve app responsiveness by 3-5x
- **🛠️ Angular Core**: Angular's reactive architecture depends on async pattern expertise

### **🔥 What You'll Master**
- **Promises vs Observables**: When to use each, performance implications
- **Advanced Async/Await**: Error handling, parallel processing, race conditions
- **RxJS Integration**: Operators, streams, Angular-specific patterns
- **Performance Optimization**: Lazy loading, caching, backpressure handling
- **Company-Tier Challenges**: Real-world async architecture problems

---

## 🏗️ **ASYNC FOUNDATIONS**

### **🎯 Promise vs Observable: The Complete Comparison**

#### **⚡ When to Use What - Decision Framework**
```javascript
// 📋 DECISION TREE FOR ASYNC PATTERNS

// ✅ USE PROMISES WHEN:
// - Single value operations
// - HTTP requests (unless you need cancellation)
// - Simple async operations
// - Converting callback-based APIs

// ✅ USE OBSERVABLES WHEN:
// - Multiple values over time (streams)
// - Need cancellation capability
// - Complex async workflows
// - Angular reactive patterns
// - Real-time data (WebSocket, SSE)

// WRONG APPROACH - Promise for stream data
function trackUserActivity() {
  return new Promise((resolve) => {
    // ❌ Can only resolve once - loses data
    document.addEventListener('click', (event) => {
      resolve(event); // Only captures first click!
    });
  });
}

// CORRECT APPROACH - Observable for stream data
function trackUserActivity() {
  return new Observable(subscriber => {
    // ✅ Handles multiple events over time
    const handler = (event) => subscriber.next(event);
    document.addEventListener('click', handler);
    
    // Cleanup function
    return () => document.removeEventListener('click', handler);
  });
}

// PROMISE - Single HTTP request
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    return await response.json();
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw error;
  }
}

// OBSERVABLE - Cancellable HTTP with retry
function fetchUserObservable(id) {
  return new Observable(subscriber => {
    const controller = new AbortController();
    
    fetch(`/api/users/${id}`, { signal: controller.signal })
      .then(response => {
        if (!response.ok) {
          throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        return response.json();
      })
      .then(data => {
        subscriber.next(data);
        subscriber.complete();
      })
      .catch(error => {
        if (error.name === 'AbortError') {
          subscriber.complete(); // Don't emit error for cancellation
        } else {
          subscriber.error(error);
        }
      });
    
    // Cancellation support
    return () => controller.abort();
  });
}
```

#### **🏆 Advanced Promise Patterns**
```javascript
// 🎯 PROMISE MASTERY PATTERNS FOR INTERVIEWS

// 1. CONTROLLED CONCURRENCY
class PromiseConcurrencyManager {
  
  constructor(maxConcurrency = 3) {
    this.maxConcurrency = maxConcurrency;
    this.running = 0;
    this.queue = [];
  }
  
  // ✅ Execute with concurrency limit
  async execute(promiseFactory) {
    return new Promise((resolve, reject) => {
      this.queue.push({
        promiseFactory,
        resolve,
        reject
      });
      
      this.tryNext();
    });
  }
  
  async tryNext() {
    if (this.running >= this.maxConcurrency || this.queue.length === 0) {
      return;
    }
    
    this.running++;
    const { promiseFactory, resolve, reject } = this.queue.shift();
    
    try {
      const result = await promiseFactory();
      resolve(result);
    } catch (error) {
      reject(error);
    } finally {
      this.running--;
      this.tryNext(); // Process next in queue
    }
  }
  
  // ✅ Batch processing with controlled concurrency
  async batch(promiseFactories, batchSize = this.maxConcurrency) {
    const results = [];
    const chunks = this.chunkArray(promiseFactories, batchSize);
    
    for (const chunk of chunks) {
      const chunkResults = await Promise.allSettled(
        chunk.map(factory => this.execute(factory))
      );
      results.push(...chunkResults);
    }
    
    return results;
  }
  
  chunkArray(array, chunkSize) {
    const chunks = [];
    for (let i = 0; i < array.length; i += chunkSize) {
      chunks.push(array.slice(i, i + chunkSize));
    }
    return chunks;
  }
}

// Usage example
const concurrencyManager = new PromiseConcurrencyManager(5);

const userIds = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const fetchTasks = userIds.map(id => () => fetchUser(id));

const results = await concurrencyManager.batch(fetchTasks);

// 2. RETRY WITH EXPONENTIAL BACKOFF
class PromiseRetryManager {
  
  static async withRetry(operation, options = {}) {
    const {
      maxAttempts = 3,
      baseDelay = 1000,
      maxDelay = 10000,
      backoffFactor = 2,
      jitter = true,
      retryCondition = () => true
    } = options;
    
    let lastError;
    
    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
      try {
        return await operation();
      } catch (error) {
        lastError = error;
        
        // Check if should retry
        if (attempt === maxAttempts || !retryCondition(error, attempt)) {
          throw error;
        }
        
        // Calculate delay with exponential backoff
        let delay = Math.min(
          baseDelay * Math.pow(backoffFactor, attempt - 1),
          maxDelay
        );
        
        // Add jitter to prevent thundering herd
        if (jitter) {
          delay = delay * (0.5 + Math.random() * 0.5);
        }
        
        console.warn(`Attempt ${attempt} failed, retrying in ${delay}ms:`, error.message);
        await this.delay(delay);
      }
    }
    
    throw lastError;
  }
  
  static delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
  
  // ✅ Smart retry conditions
  static shouldRetry(error, attempt) {
    // Don't retry client errors (4xx)
    if (error.status && error.status >= 400 && error.status < 500) {
      return false;
    }
    
    // Don't retry after too many attempts
    if (attempt >= 5) {
      return false;
    }
    
    // Retry network errors and server errors (5xx)
    return true;
  }
}

// Usage example
const robustFetch = async (url) => {
  return PromiseRetryManager.withRetry(
    () => fetch(url).then(response => {
      if (!response.ok) {
        const error = new Error(`HTTP ${response.status}`);
        error.status = response.status;
        throw error;
      }
      return response.json();
    }),
    {
      maxAttempts: 3,
      baseDelay: 1000,
      retryCondition: PromiseRetryManager.shouldRetry
    }
  );
};

// 3. PROMISE TIMEOUT AND CANCELLATION
class PromiseTimeoutManager {
  
  static withTimeout(promise, timeoutMs, timeoutMessage = 'Operation timed out') {
    const timeoutPromise = new Promise((_, reject) => {
      setTimeout(() => reject(new Error(timeoutMessage)), timeoutMs);
    });
    
    return Promise.race([promise, timeoutPromise]);
  }
  
  static withCancellation(promiseFactory) {
    let cancelled = false;
    let cancelResolve;
    
    const cancellationPromise = new Promise(resolve => {
      cancelResolve = resolve;
    });
    
    const wrappedPromise = Promise.race([
      promiseFactory().catch(error => {
        if (cancelled) {
          throw new Error('Operation was cancelled');
        }
        throw error;
      }),
      cancellationPromise.then(() => {
        throw new Error('Operation was cancelled');
      })
    ]);
    
    wrappedPromise.cancel = () => {
      cancelled = true;
      cancelResolve();
    };
    
    return wrappedPromise;
  }
  
  // ✅ Combined timeout and cancellation
  static withTimeoutAndCancellation(promiseFactory, timeoutMs) {
    const cancellablePromise = this.withCancellation(promiseFactory);
    const timeoutPromise = this.withTimeout(cancellablePromise, timeoutMs);
    
    // Transfer cancel method
    timeoutPromise.cancel = cancellablePromise.cancel;
    
    return timeoutPromise;
  }
}

// Usage example
const longRunningOperation = PromiseTimeoutManager.withTimeoutAndCancellation(
  () => fetch('/api/heavy-computation'),
  5000 // 5 second timeout
);

// Can be cancelled manually
setTimeout(() => {
  longRunningOperation.cancel();
}, 3000);

// 4. PROMISE COMPOSITION PATTERNS
class PromiseComposition {
  
  // ✅ Waterfall execution (sequential with dependency)
  static async waterfall(operations) {
    let result = null;
    
    for (const operation of operations) {
      result = await operation(result);
    }
    
    return result;
  }
  
  // ✅ Pipeline execution (transform chain)
  static async pipeline(value, transformers) {
    let current = value;
    
    for (const transformer of transformers) {
      current = await transformer(current);
    }
    
    return current;
  }
  
  // ✅ Parallel with selective results
  static async selectiveParallel(operations, selector) {
    const results = await Promise.allSettled(operations);
    
    return results
      .map((result, index) => ({ result, index }))
      .filter(({ result }) => result.status === 'fulfilled')
      .filter(({ result, index }) => selector(result.value, index))
      .map(({ result }) => result.value);
  }
  
  // ✅ First successful (race with error handling)
  static async firstSuccessful(promises) {
    const errors = [];
    
    try {
      return await Promise.any(promises);
    } catch (aggregateError) {
      // All promises failed
      throw new Error(`All operations failed: ${aggregateError.errors.map(e => e.message).join(', ')}`);
    }
  }
  
  // ✅ Majority consensus (wait for majority to complete)
  static async majorityConsensus(promises, threshold = 0.6) {
    const requiredSuccesses = Math.ceil(promises.length * threshold);
    const results = [];
    let completed = 0;
    let successes = 0;
    
    return new Promise((resolve, reject) => {
      promises.forEach((promise, index) => {
        promise
          .then(result => {
            results[index] = { status: 'fulfilled', value: result };
            successes++;
            completed++;
            
            if (successes >= requiredSuccesses) {
              resolve(results.filter(r => r?.status === 'fulfilled').map(r => r.value));
            } else if (completed === promises.length && successes < requiredSuccesses) {
              reject(new Error(`Insufficient successful operations: ${successes}/${requiredSuccesses} required`));
            }
          })
          .catch(error => {
            results[index] = { status: 'rejected', reason: error };
            completed++;
            
            if (completed === promises.length && successes < requiredSuccesses) {
              reject(new Error(`Insufficient successful operations: ${successes}/${requiredSuccesses} required`));
            }
          });
      });
    });
  }
}

// Usage examples
const waterfallOps = [
  (prev) => fetch('/api/step1').then(r => r.json()),
  (prev) => fetch(`/api/step2/${prev.id}`).then(r => r.json()),
  (prev) => fetch(`/api/step3/${prev.token}`).then(r => r.json())
];

const waterfallResult = await PromiseComposition.waterfall(waterfallOps);

// Pipeline example
const dataProcessingPipeline = [
  data => validateData(data),
  data => enrichData(data),
  data => transformData(data),
  data => saveData(data)
];

const processedData = await PromiseComposition.pipeline(rawData, dataProcessingPipeline);
```

#### **🎪 Observable Mastery Patterns**
```javascript
// 🎯 OBSERVABLE EXPERTISE FOR ANGULAR INTERVIEWS

// 1. CUSTOM OBSERVABLE CREATION
class ObservableCreationPatterns {
  
  // ✅ From DOM events with cleanup
  static fromEvent(target, eventType, options = {}) {
    return new Observable(subscriber => {
      const handler = (event) => {
        if (options.preventDefault) {
          event.preventDefault();
        }
        
        if (options.stopPropagation) {
          event.stopPropagation();
        }
        
        subscriber.next(event);
      };
      
      target.addEventListener(eventType, handler, options.capture);
      
      // Cleanup function
      return () => {
        target.removeEventListener(eventType, handler, options.capture);
      };
    });
  }
  
  // ✅ WebSocket observable with reconnection
  static fromWebSocket(url, protocols, options = {}) {
    const {
      reconnectAttempts = 5,
      reconnectDelay = 1000,
      heartbeatInterval = 30000
    } = options;
    
    return new Observable(subscriber => {
      let socket;
      let heartbeatTimer;
      let reconnectCount = 0;
      let isManualClose = false;
      
      const connect = () => {
        socket = new WebSocket(url, protocols);
        
        socket.onopen = () => {
          reconnectCount = 0;
          subscriber.next({ type: 'connected' });
          
          // Start heartbeat
          if (heartbeatInterval > 0) {
            heartbeatTimer = setInterval(() => {
              if (socket.readyState === WebSocket.OPEN) {
                socket.send(JSON.stringify({ type: 'ping' }));
              }
            }, heartbeatInterval);
          }
        };
        
        socket.onmessage = (event) => {
          try {
            const data = JSON.parse(event.data);
            subscriber.next({ type: 'message', data });
          } catch (error) {
            subscriber.next({ type: 'message', data: event.data });
          }
        };
        
        socket.onerror = (error) => {
          subscriber.error(error);
        };
        
        socket.onclose = (event) => {
          clearInterval(heartbeatTimer);
          
          if (!isManualClose && reconnectCount < reconnectAttempts) {
            setTimeout(() => {
              reconnectCount++;
              subscriber.next({ type: 'reconnecting', attempt: reconnectCount });
              connect();
            }, reconnectDelay * Math.pow(2, reconnectCount)); // Exponential backoff
          } else if (!isManualClose) {
            subscriber.error(new Error('Max reconnection attempts reached'));
          }
        };
      };
      
      connect();
      
      // Cleanup function
      return () => {
        isManualClose = true;
        clearInterval(heartbeatTimer);
        if (socket) {
          socket.close();
        }
      };
    });
  }
  
  // ✅ Interval with immediate emission
  static timer(initialDelay, period) {
    return new Observable(subscriber => {
      let count = 0;
      
      // Emit immediately if initialDelay is 0
      if (initialDelay === 0) {
        subscriber.next(count++);
      }
      
      const initialTimer = setTimeout(() => {
        subscriber.next(count++);
        
        if (period !== undefined) {
          const intervalTimer = setInterval(() => {
            subscriber.next(count++);
          }, period);
          
          // Store interval ID for cleanup
          subscriber.add(() => clearInterval(intervalTimer));
        } else {
          subscriber.complete();
        }
      }, initialDelay);
      
      // Cleanup function
      return () => clearTimeout(initialTimer);
    });
  }
  
  // ✅ Retry with exponential backoff
  static retryWithBackoff(sourceFactory, options = {}) {
    const {
      maxRetries = 3,
      initialDelay = 1000,
      maxDelay = 10000,
      backoffFactor = 2
    } = options;
    
    return new Observable(subscriber => {
      let retryCount = 0;
      let subscription;
      
      const attempt = () => {
        subscription = sourceFactory().subscribe({
          next: value => subscriber.next(value),
          complete: () => subscriber.complete(),
          error: error => {
            if (retryCount < maxRetries) {
              const delay = Math.min(
                initialDelay * Math.pow(backoffFactor, retryCount),
                maxDelay
              );
              
              retryCount++;
              setTimeout(attempt, delay);
            } else {
              subscriber.error(error);
            }
          }
        });
      };
      
      attempt();
      
      // Cleanup function
      return () => {
        if (subscription) {
          subscription.unsubscribe();
        }
      };
    });
  }
}

// 2. ADVANCED OBSERVABLE OPERATORS
class CustomOperators {
  
  // ✅ Debounce with immediate first emission
  static debounceWithImmediate(delay) {
    return (source) => {
      return new Observable(subscriber => {
        let hasEmitted = false;
        let timer;
        let lastValue;
        
        const subscription = source.subscribe({
          next: value => {
            lastValue = value;
            
            if (!hasEmitted) {
              // Emit immediately on first value
              hasEmitted = true;
              subscriber.next(value);
            } else {
              // Debounce subsequent values
              clearTimeout(timer);
              timer = setTimeout(() => {
                subscriber.next(lastValue);
              }, delay);
            }
          },
          error: error => subscriber.error(error),
          complete: () => {
            clearTimeout(timer);
            subscriber.complete();
          }
        });
        
        return () => {
          clearTimeout(timer);
          subscription.unsubscribe();
        };
      });
    };
  }
  
  // ✅ Cache with TTL
  static cacheWithTTL(ttl = 5000) {
    let cache = null;
    let cacheTime = 0;
    
    return (source) => {
      return new Observable(subscriber => {
        const now = Date.now();
        
        // Return cached value if still valid
        if (cache !== null && (now - cacheTime) < ttl) {
          subscriber.next(cache);
          subscriber.complete();
          return;
        }
        
        // Fetch new value
        const subscription = source.subscribe({
          next: value => {
            cache = value;
            cacheTime = now;
            subscriber.next(value);
          },
          error: error => subscriber.error(error),
          complete: () => subscriber.complete()
        });
        
        return () => subscription.unsubscribe();
      });
    };
  }
  
  // ✅ Buffer with size and time limits
  static bufferWithLimits(sizeLimit, timeLimit) {
    return (source) => {
      return new Observable(subscriber => {
        let buffer = [];
        let timer;
        
        const emitBuffer = () => {
          if (buffer.length > 0) {
            subscriber.next([...buffer]);
            buffer = [];
          }
          clearTimeout(timer);
        };
        
        const subscription = source.subscribe({
          next: value => {
            buffer.push(value);
            
            // Emit if size limit reached
            if (buffer.length >= sizeLimit) {
              emitBuffer();
            } else if (buffer.length === 1) {
              // Start timer on first item
              timer = setTimeout(emitBuffer, timeLimit);
            }
          },
          error: error => {
            clearTimeout(timer);
            subscriber.error(error);
          },
          complete: () => {
            emitBuffer();
            subscriber.complete();
          }
        });
        
        return () => {
          clearTimeout(timer);
          subscription.unsubscribe();
        };
      });
    };
  }
  
  // ✅ Smart retry based on error type
  static smartRetry(retryConfig) {
    return (source) => {
      return new Observable(subscriber => {
        let retryCount = 0;
        let subscription;
        
        const attempt = () => {
          subscription = source.subscribe({
            next: value => subscriber.next(value),
            complete: () => subscriber.complete(),
            error: error => {
              const config = retryConfig.find(cfg => cfg.condition(error)) || 
                           retryConfig.find(cfg => cfg.condition === 'default');
              
              if (config && retryCount < config.maxRetries) {
                retryCount++;
                setTimeout(attempt, config.delay || 1000);
              } else {
                subscriber.error(error);
              }
            }
          });
        };
        
        attempt();
        
        return () => {
          if (subscription) {
            subscription.unsubscribe();
          }
        };
      });
    };
  }
}

// Usage examples
const searchInput$ = ObservableCreationPatterns
  .fromEvent(document.getElementById('search'), 'input')
  .pipe(
    map(event => event.target.value),
    CustomOperators.debounceWithImmediate(300),
    distinctUntilChanged(),
    switchMap(query => 
      searchAPI(query).pipe(
        CustomOperators.cacheWithTTL(60000), // Cache for 1 minute
        CustomOperators.smartRetry([
          { condition: error => error.status === 429, maxRetries: 5, delay: 2000 },
          { condition: error => error.status >= 500, maxRetries: 3, delay: 1000 },
          { condition: 'default', maxRetries: 1, delay: 500 }
        ])
      )
    )
  );

// WebSocket with automatic reconnection
const realTimeData$ = ObservableCreationPatterns
  .fromWebSocket('wss://api.example.com/live-data', [], {
    reconnectAttempts: 10,
    reconnectDelay: 2000,
    heartbeatInterval: 30000
  })
  .pipe(
    filter(message => message.type === 'message'),
    map(message => message.data),
    CustomOperators.bufferWithLimits(10, 1000) // Buffer up to 10 items or 1 second
  );
```

---

## 🔥 **ADVANCED ASYNC PATTERNS**

### **⚡ Error Handling & Resilience**
```javascript
// 🎯 BULLETPROOF ERROR HANDLING FOR PRODUCTION

// 1. CIRCUIT BREAKER PATTERN
class CircuitBreaker {
  
  constructor(options = {}) {
    this.failureThreshold = options.failureThreshold || 5;
    this.resetTimeout = options.resetTimeout || 30000;
    this.monitoringWindow = options.monitoringWindow || 60000;
    
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
    this.failures = [];
    this.nextAttempt = 0;
    this.successCount = 0;
  }
  
  async execute(operation) {
    if (this.state === 'OPEN') {
      if (Date.now() < this.nextAttempt) {
        throw new Error('Circuit breaker is OPEN');
      } else {
        this.state = 'HALF_OPEN';
        this.successCount = 0;
      }
    }
    
    try {
      const result = await operation();
      
      if (this.state === 'HALF_OPEN') {
        this.successCount++;
        if (this.successCount >= 3) { // Require 3 successes to close
          this.state = 'CLOSED';
          this.failures = [];
        }
      }
      
      this.recordSuccess();
      return result;
      
    } catch (error) {
      this.recordFailure();
      
      if (this.shouldOpenCircuit()) {
        this.state = 'OPEN';
        this.nextAttempt = Date.now() + this.resetTimeout;
      }
      
      throw error;
    }
  }
  
  recordFailure() {
    const now = Date.now();
    this.failures.push(now);
    
    // Clean old failures outside monitoring window
    this.failures = this.failures.filter(
      failureTime => (now - failureTime) < this.monitoringWindow
    );
  }
  
  recordSuccess() {
    // Remove one failure on success (gradual recovery)
    if (this.failures.length > 0) {
      this.failures.shift();
    }
  }
  
  shouldOpenCircuit() {
    return this.failures.length >= this.failureThreshold;
  }
  
  getStatus() {
    return {
      state: this.state,
      failures: this.failures.length,
      nextAttempt: this.nextAttempt ? new Date(this.nextAttempt) : null
    };
  }
}

// 2. BULKHEAD PATTERN (Resource Isolation)
class BulkheadManager {
  
  constructor() {
    this.pools = new Map();
  }
  
  createPool(name, options = {}) {
    const pool = {
      maxConcurrency: options.maxConcurrency || 10,
      currentConcurrency: 0,
      queue: [],
      circuitBreaker: new CircuitBreaker(options.circuitBreaker)
    };
    
    this.pools.set(name, pool);
    return pool;
  }
  
  async execute(poolName, operation) {
    const pool = this.pools.get(poolName);
    
    if (!pool) {
      throw new Error(`Pool ${poolName} not found`);
    }
    
    // Wait for available slot
    if (pool.currentConcurrency >= pool.maxConcurrency) {
      await this.waitForSlot(pool);
    }
    
    pool.currentConcurrency++;
    
    try {
      // Use circuit breaker for additional resilience
      const result = await pool.circuitBreaker.execute(operation);
      return result;
    } finally {
      pool.currentConcurrency--;
      this.processQueue(pool);
    }
  }
  
  async waitForSlot(pool) {
    return new Promise(resolve => {
      pool.queue.push(resolve);
    });
  }
  
  processQueue(pool) {
    if (pool.queue.length > 0 && pool.currentConcurrency < pool.maxConcurrency) {
      const resolve = pool.queue.shift();
      resolve();
    }
  }
  
  getPoolStatus(poolName) {
    const pool = this.pools.get(poolName);
    
    if (!pool) {
      return null;
    }
    
    return {
      maxConcurrency: pool.maxConcurrency,
      currentConcurrency: pool.currentConcurrency,
      queueLength: pool.queue.length,
      circuitBreaker: pool.circuitBreaker.getStatus()
    };
  }
}

// Usage example
const bulkhead = new BulkheadManager();

// Create separate pools for different types of operations
bulkhead.createPool('database', { 
  maxConcurrency: 20,
  circuitBreaker: { failureThreshold: 5, resetTimeout: 30000 }
});

bulkhead.createPool('external-api', { 
  maxConcurrency: 5,
  circuitBreaker: { failureThreshold: 3, resetTimeout: 60000 }
});

bulkhead.createPool('file-processing', { 
  maxConcurrency: 3,
  circuitBreaker: { failureThreshold: 2, resetTimeout: 15000 }
});

// 3. COMPREHENSIVE ERROR RECOVERY
class ErrorRecoveryManager {
  
  constructor() {
    this.retryStrategies = new Map();
    this.fallbackStrategies = new Map();
    this.errorMetrics = new Map();
  }
  
  // ✅ Register retry strategy for specific error types
  registerRetryStrategy(errorType, strategy) {
    this.retryStrategies.set(errorType, strategy);
  }
  
  // ✅ Register fallback strategy
  registerFallbackStrategy(operationType, fallback) {
    this.fallbackStrategies.set(operationType, fallback);
  }
  
  // ✅ Execute with full error recovery
  async executeWithRecovery(operation, operationType, options = {}) {
    const startTime = Date.now();
    let lastError;
    
    try {
      // Primary operation attempt
      const result = await this.executeWithRetry(operation, options);
      this.recordSuccess(operationType, Date.now() - startTime);
      return result;
      
    } catch (error) {
      lastError = error;
      this.recordError(operationType, error, Date.now() - startTime);
      
      // Try fallback strategy
      const fallback = this.fallbackStrategies.get(operationType);
      
      if (fallback) {
        try {
          console.warn(`Primary operation failed, using fallback for ${operationType}:`, error.message);
          const fallbackResult = await fallback(error);
          this.recordFallbackSuccess(operationType);
          return fallbackResult;
        } catch (fallbackError) {
          this.recordFallbackFailure(operationType, fallbackError);
          throw fallbackError;
        }
      }
      
      throw error;
    }
  }
  
  async executeWithRetry(operation, options = {}) {
    const { maxAttempts = 3, baseDelay = 1000 } = options;
    let lastError;
    
    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
      try {
        return await operation();
      } catch (error) {
        lastError = error;
        
        if (attempt === maxAttempts) {
          throw error;
        }
        
        // Check if we should retry based on error type
        const shouldRetry = this.shouldRetryError(error);
        
        if (!shouldRetry) {
          throw error;
        }
        
        // Calculate delay based on strategy
        const strategy = this.getRetryStrategy(error);
        const delay = strategy.calculateDelay(attempt, baseDelay);
        
        console.warn(`Attempt ${attempt} failed, retrying in ${delay}ms:`, error.message);
        await this.delay(delay);
      }
    }
    
    throw lastError;
  }
  
  shouldRetryError(error) {
    // Network errors - retry
    if (error.code === 'NETWORK_ERROR' || error.name === 'TypeError') {
      return true;
    }
    
    // Server errors (5xx) - retry
    if (error.status >= 500 && error.status < 600) {
      return true;
    }
    
    // Rate limiting (429) - retry with longer delay
    if (error.status === 429) {
      return true;
    }
    
    // Client errors (4xx except 429) - don't retry
    if (error.status >= 400 && error.status < 500) {
      return false;
    }
    
    // Default: retry for unknown errors
    return true;
  }
  
  getRetryStrategy(error) {
    // Rate limiting gets special treatment
    if (error.status === 429) {
      return this.retryStrategies.get('RATE_LIMIT') || {
        calculateDelay: (attempt, base) => Math.min(base * Math.pow(3, attempt), 30000)
      };
    }
    
    // Server errors
    if (error.status >= 500) {
      return this.retryStrategies.get('SERVER_ERROR') || {
        calculateDelay: (attempt, base) => base * Math.pow(2, attempt - 1)
      };
    }
    
    // Default exponential backoff
    return {
      calculateDelay: (attempt, base) => base * Math.pow(2, attempt - 1) + Math.random() * 1000
    };
  }
  
  // ✅ Metrics and monitoring
  recordSuccess(operationType, duration) {
    this.updateMetrics(operationType, 'success', duration);
  }
  
  recordError(operationType, error, duration) {
    this.updateMetrics(operationType, 'error', duration, error);
  }
  
  recordFallbackSuccess(operationType) {
    this.updateMetrics(operationType, 'fallback_success');
  }
  
  recordFallbackFailure(operationType, error) {
    this.updateMetrics(operationType, 'fallback_failure', null, error);
  }
  
  updateMetrics(operationType, result, duration = null, error = null) {
    if (!this.errorMetrics.has(operationType)) {
      this.errorMetrics.set(operationType, {
        total: 0,
        success: 0,
        error: 0,
        fallback_success: 0,
        fallback_failure: 0,
        averageDuration: 0,
        errors: []
      });
    }
    
    const metrics = this.errorMetrics.get(operationType);
    metrics.total++;
    metrics[result]++;
    
    if (duration !== null) {
      metrics.averageDuration = (metrics.averageDuration * (metrics.total - 1) + duration) / metrics.total;
    }
    
    if (error) {
      metrics.errors.push({
        timestamp: new Date(),
        message: error.message,
        type: error.constructor.name
      });
      
      // Keep only last 100 errors
      if (metrics.errors.length > 100) {
        metrics.errors.shift();
      }
    }
  }
  
  getMetrics(operationType = null) {
    if (operationType) {
      return this.errorMetrics.get(operationType);
    }
    
    return Object.fromEntries(this.errorMetrics);
  }
  
  delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}

// Usage example
const recoveryManager = new ErrorRecoveryManager();

// Register custom retry strategies
recoveryManager.registerRetryStrategy('RATE_LIMIT', {
  calculateDelay: (attempt, base) => {
    // Aggressive backoff for rate limiting
    return Math.min(base * Math.pow(4, attempt), 60000);
  }
});

// Register fallback strategies
recoveryManager.registerFallbackStrategy('user-data', async (error) => {
  // Fallback to cached data
  return getCachedUserData();
});

recoveryManager.registerFallbackStrategy('search', async (error) => {
  // Fallback to simplified search
  return getSimplifiedSearchResults();
});

// Execute operations with full recovery
const userData = await recoveryManager.executeWithRecovery(
  () => fetchUserFromAPI(userId),
  'user-data',
  { maxAttempts: 3, baseDelay: 1000 }
);
```

---

## 🚀 **ANGULAR-SPECIFIC ASYNC PATTERNS**

### **🎯 Component Async Patterns**
```typescript
// 🏆 ANGULAR COMPONENT ASYNC MASTERY

// 1. ASYNC DATA LOADING WITH STATE MANAGEMENT
@Component({
  selector: 'app-user-dashboard',
  template: `
    <div class="dashboard">
      <!-- ✅ Loading states -->
      <div *ngIf="loadingState$ | async as loading" class="loading-indicator">
        <span *ngIf="loading.isLoading">Loading {{loading.operation}}...</span>
        <div *ngIf="loading.progress" class="progress-bar">
          <div [style.width.%]="loading.progress"></div>
        </div>
      </div>
      
      <!-- ✅ Error handling -->
      <div *ngIf="error$ | async as error" class="error-message">
        <p>{{error.message}}</p>
        <button (click)="retryLastOperation()">Retry</button>
      </div>
      
      <!-- ✅ Data display -->
      <div *ngIf="dashboardData$ | async as data" class="dashboard-content">
        <user-profile [user]="data.user"></user-profile>
        <activity-feed [activities]="data.activities"></activity-feed>
        <notifications-panel [notifications]="data.notifications"></notifications-panel>
      </div>
    </div>
  `
})
export class UserDashboardComponent implements OnInit, OnDestroy {
  
  // ✅ Reactive state management
  private loadingSubject = new BehaviorSubject<LoadingState>({ isLoading: false });
  private errorSubject = new BehaviorSubject<ErrorState | null>(null);
  private destroy$ = new Subject<void>();
  
  loadingState$ = this.loadingSubject.asObservable();
  error$ = this.errorSubject.asObservable();
  
  // ✅ Combined data streams
  dashboardData$ = this.createDashboardStream();
  
  private lastFailedOperation: (() => Observable<any>) | null = null;
  
  constructor(
    private userService: UserService,
    private activityService: ActivityService,
    private notificationService: NotificationService,
    private errorHandler: ErrorHandlerService
  ) {}
  
  ngOnInit() {
    this.initializeDashboard();
  }
  
  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
  
  // ✅ Complex async data orchestration
  private createDashboardStream(): Observable<DashboardData> {
    return this.userService.getCurrentUser().pipe(
      // Start loading indicator
      tap(() => this.setLoading('user data', true)),
      
      // Load dependent data in parallel
      switchMap(user => {
        if (!user) {
          throw new Error('User not authenticated');
        }
        
        return combineLatest({
          user: of(user),
          activities: this.loadActivitiesWithRetry(user.id),
          notifications: this.loadNotificationsWithCache(user.id)
        });
      }),
      
      // Handle successful load
      tap(() => {
        this.setLoading('', false);
        this.clearError();
      }),
      
      // Error handling with recovery
      catchError(error => {
        this.handleError(error, () => this.createDashboardStream());
        return EMPTY; // Return empty to prevent component errors
      }),
      
      takeUntil(this.destroy$)
    );
  }
  
  // ✅ Retry mechanism with exponential backoff
  private loadActivitiesWithRetry(userId: string): Observable<Activity[]> {
    return this.activityService.getUserActivities(userId).pipe(
      retry({
        count: 3,
        delay: (error, retryCount) => {
          const delay = Math.min(1000 * Math.pow(2, retryCount - 1), 10000);
          console.warn(`Retrying activities load (attempt ${retryCount}) in ${delay}ms`);
          return timer(delay);
        }
      }),
      catchError(error => {
        console.error('Failed to load activities after retries:', error);
        // Return empty array as fallback
        return of([]);
      })
    );
  }
  
  // ✅ Caching with TTL
  private loadNotificationsWithCache(userId: string): Observable<Notification[]> {
    const cacheKey = `notifications_${userId}`;
    const cached = this.getCachedData(cacheKey);
    
    if (cached && this.isCacheValid(cached, 300000)) { // 5 min TTL
      return of(cached.data);
    }
    
    return this.notificationService.getUserNotifications(userId).pipe(
      tap(notifications => {
        this.setCachedData(cacheKey, notifications);
      }),
      catchError(error => {
        console.error('Failed to load notifications:', error);
        // Return cached data if available, even if expired
        return of(cached ? cached.data : []);
      })
    );
  }
  
  // ✅ Advanced loading state management
  private setLoading(operation: string, isLoading: boolean, progress?: number) {
    this.loadingSubject.next({
      isLoading,
      operation,
      progress
    });
  }
  
  private handleError(error: any, retryOperation?: () => Observable<any>) {
    this.setLoading('', false);
    
    const errorState: ErrorState = {
      message: this.getErrorMessage(error),
      code: error.code || 'UNKNOWN',
      timestamp: new Date(),
      canRetry: !!retryOperation
    };
    
    this.errorSubject.next(errorState);
    this.lastFailedOperation = retryOperation || null;
    
    // Log to error service
    this.errorHandler.logError(error, 'UserDashboardComponent');
  }
  
  private clearError() {
    this.errorSubject.next(null);
    this.lastFailedOperation = null;
  }
  
  retryLastOperation() {
    if (this.lastFailedOperation) {
      this.clearError();
      this.dashboardData$ = this.lastFailedOperation();
    }
  }
  
  // ✅ Cache management
  private getCachedData(key: string): CachedData | null {
    const cached = localStorage.getItem(key);
    return cached ? JSON.parse(cached) : null;
  }
  
  private setCachedData(key: string, data: any) {
    const cacheEntry: CachedData = {
      data,
      timestamp: Date.now()
    };
    localStorage.setItem(key, JSON.stringify(cacheEntry));
  }
  
  private isCacheValid(cached: CachedData, ttl: number): boolean {
    return (Date.now() - cached.timestamp) < ttl;
  }
  
  private getErrorMessage(error: any): string {
    if (error.status === 401) return 'Please log in to continue';
    if (error.status === 403) return 'You do not have permission to access this data';
    if (error.status === 404) return 'The requested data was not found';
    if (error.status >= 500) return 'Server error. Please try again later';
    return error.message || 'An unexpected error occurred';
  }
}

// Supporting interfaces
interface LoadingState {
  isLoading: boolean;
  operation?: string;
  progress?: number;
}

interface ErrorState {
  message: string;
  code: string;
  timestamp: Date;
  canRetry: boolean;
}

interface CachedData {
  data: any;
  timestamp: number;
}

interface DashboardData {
  user: User;
  activities: Activity[];
  notifications: Notification[];
}

// 2. REACTIVE FORM ASYNC VALIDATION
@Component({
  selector: 'app-user-registration',
  template: `
    <form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">
      <div class="form-field">
        <input formControlName="username" placeholder="Username">
        <div class="async-validation-indicator">
          <span *ngIf="usernameValidating$ | async">Checking availability...</span>
          <span *ngIf="usernameError$ | async as error" class="error">{{error}}</span>
          <span *ngIf="usernameValid$ | async" class="success">✓ Available</span>
        </div>
      </div>
      
      <div class="form-field">
        <input formControlName="email" placeholder="Email">
        <div class="async-validation-indicator">
          <span *ngIf="emailValidating$ | async">Validating email...</span>
          <span *ngIf="emailError$ | async as error" class="error">{{error}}</span>
          <span *ngIf="emailValid$ | async" class="success">✓ Valid</span>
        </div>
      </div>
      
      <button type="submit" [disabled]="registrationForm.invalid || (isSubmitting$ | async)">
        <span *ngIf="isSubmitting$ | async">Creating account...</span>
        <span *ngIf="!(isSubmitting$ | async)">Create Account</span>
      </button>
    </form>
  `
})
export class UserRegistrationComponent implements OnInit, OnDestroy {
  
  registrationForm = this.fb.group({
    username: ['', [Validators.required, Validators.minLength(3)], [this.usernameAsyncValidator.bind(this)]],
    email: ['', [Validators.required, Validators.email], [this.emailAsyncValidator.bind(this)]],
    password: ['', [Validators.required, Validators.minLength(8)]]
  });
  
  private destroy$ = new Subject<void>();
  private submittingSubject = new BehaviorSubject<boolean>(false);
  
  isSubmitting$ = this.submittingSubject.asObservable();
  
  // ✅ Reactive validation state streams
  usernameValidating$ = this.createValidationStream('username', 'pending');
  usernameValid$ = this.createValidationStream('username', 'valid');
  usernameError$ = this.createValidationErrorStream('username');
  
  emailValidating$ = this.createValidationStream('email', 'pending');
  emailValid$ = this.createValidationStream('email', 'valid');
  emailError$ = this.createValidationErrorStream('email');
  
  constructor(
    private fb: FormBuilder,
    private userService: UserService,
    private validationService: ValidationService
  ) {}
  
  ngOnInit() {
    this.setupFormValidation();
  }
  
  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
  
  // ✅ Debounced async username validation
  private usernameAsyncValidator(control: AbstractControl): Observable<ValidationErrors | null> {
    if (!control.value || control.value.length < 3) {
      return of(null);
    }
    
    return timer(500).pipe( // Debounce for 500ms
      switchMap(() => this.userService.checkUsernameAvailability(control.value)),
      map(available => available ? null : { usernameTaken: { value: control.value } }),
      catchError(() => of({ usernameCheckFailed: true })),
      takeUntil(this.destroy$)
    );
  }
  
  // ✅ Complex email validation with multiple checks
  private emailAsyncValidator(control: AbstractControl): Observable<ValidationErrors | null> {
    const email = control.value;
    
    if (!email || !Validators.email(control)) {
      return of(null);
    }
    
    return timer(300).pipe(
      switchMap(() => {
        // Parallel validation checks
        return combineLatest({
          domainValid: this.validationService.validateEmailDomain(email),
          notDisposable: this.validationService.checkDisposableEmail(email),
          notBlacklisted: this.validationService.checkEmailBlacklist(email)
        });
      }),
      map(({ domainValid, notDisposable, notBlacklisted }) => {
        const errors: ValidationErrors = {};
        
        if (!domainValid) errors['invalidDomain'] = true;
        if (!notDisposable) errors['disposableEmail'] = true;
        if (!notBlacklisted) errors['blacklistedEmail'] = true;
        
        return Object.keys(errors).length > 0 ? errors : null;
      }),
      catchError(error => {
        console.error('Email validation error:', error);
        return of({ emailValidationFailed: true });
      }),
      takeUntil(this.destroy$)
    );
  }
  
  // ✅ Create reactive validation state streams
  private createValidationStream(fieldName: string, status: string): Observable<boolean> {
    const control = this.registrationForm.get(fieldName);
    if (!control) return of(false);
    
    return merge(
      control.statusChanges,
      control.valueChanges
    ).pipe(
      map(() => control.status === status),
      startWith(false),
      distinctUntilChanged(),
      takeUntil(this.destroy$)
    );
  }
  
  private createValidationErrorStream(fieldName: string): Observable<string | null> {
    const control = this.registrationForm.get(fieldName);
    if (!control) return of(null);
    
    return merge(
      control.statusChanges,
      control.valueChanges
    ).pipe(
      map(() => {
        if (control.errors && control.status !== 'PENDING') {
          return this.getValidationErrorMessage(control.errors);
        }
        return null;
      }),
      startWith(null),
      distinctUntilChanged(),
      takeUntil(this.destroy$)
    );
  }
  
  private getValidationErrorMessage(errors: ValidationErrors): string {
    if (errors['usernameTaken']) return 'Username is already taken';
    if (errors['usernameCheckFailed']) return 'Unable to verify username availability';
    if (errors['invalidDomain']) return 'Email domain is not valid';
    if (errors['disposableEmail']) return 'Disposable email addresses are not allowed';
    if (errors['blacklistedEmail']) return 'This email address is not allowed';
    if (errors['emailValidationFailed']) return 'Unable to validate email address';
    
    return 'Validation error';
  }
  
  private setupFormValidation() {
    // ✅ Cross-field validation
    this.registrationForm.valueChanges.pipe(
      debounceTime(300),
      takeUntil(this.destroy$)
    ).subscribe(() => {
      this.performCrossFieldValidation();
    });
  }
  
  private performCrossFieldValidation() {
    const username = this.registrationForm.get('username')?.value;
    const email = this.registrationForm.get('email')?.value;
    
    // Check if username and email are too similar
    if (username && email && this.areTooSimilar(username, email)) {
      this.registrationForm.get('username')?.setErrors({ 
        ...this.registrationForm.get('username')?.errors,
        tooSimilarToEmail: true 
      });
    }
  }
  
  private areTooSimilar(username: string, email: string): boolean {
    const emailLocal = email.split('@')[0];
    return username.toLowerCase() === emailLocal.toLowerCase();
  }
  
  // ✅ Async form submission with progress tracking
  async onSubmit() {
    if (this.registrationForm.invalid) return;
    
    this.submittingSubject.next(true);
    
    try {
      const formData = this.registrationForm.value;
      
      // Async registration with progress updates
      const registration$ = this.userService.registerUser(formData).pipe(
        // Track registration progress
        tap(event => {
          if (event.type === 'progress') {
            // Update progress indicator
            console.log(`Registration progress: ${event.percentage}%`);
          }
        }),
        // Handle different response types
        filter(event => event.type === 'response'),
        map(event => event.body),
        takeUntil(this.destroy$)
      );
      
      const result = await firstValueFrom(registration$);
      
      // Success handling
      console.log('Registration successful:', result);
      this.handleRegistrationSuccess(result);
      
    } catch (error) {
      this.handleRegistrationError(error);
    } finally {
      this.submittingSubject.next(false);
    }
  }
  
  private handleRegistrationSuccess(result: any) {
    // Reset form and navigate
    this.registrationForm.reset();
    // Navigate to success page or login
  }
  
  private handleRegistrationError(error: any) {
    console.error('Registration failed:', error);
    
    // Set form-level errors based on server response
    if (error.status === 409) {
      // Conflict - email or username already exists
      if (error.error?.field === 'email') {
        this.registrationForm.get('email')?.setErrors({ emailTaken: true });
      } else if (error.error?.field === 'username') {
        this.registrationForm.get('username')?.setErrors({ usernameTaken: true });
      }
    }
  }
}

// 3. REAL-TIME DATA SYNCHRONIZATION
@Injectable({
  providedIn: 'root'
})
export class RealtimeDataService {
  
  private websocket$: Observable<any>;
  private reconnectAttempts = 0;
  private maxReconnectAttempts = 5;
  private reconnectInterval = 1000;
  
  constructor(private http: HttpClient) {
    this.websocket$ = this.createWebSocketConnection();
  }
  
  // ✅ WebSocket with automatic reconnection
  private createWebSocketConnection(): Observable<any> {
    return new Observable(subscriber => {
      let websocket: WebSocket;
      let heartbeatInterval: any;
      
      const connect = () => {
        websocket = new WebSocket('wss://api.example.com/realtime');
        
        websocket.onopen = () => {
          console.log('WebSocket connected');
          this.reconnectAttempts = 0;
          
          // Start heartbeat
          heartbeatInterval = setInterval(() => {
            if (websocket.readyState === WebSocket.OPEN) {
              websocket.send(JSON.stringify({ type: 'ping' }));
            }
          }, 30000);
          
          subscriber.next({ type: 'connected' });
        };
        
        websocket.onmessage = (event) => {
          try {
            const data = JSON.parse(event.data);
            subscriber.next({ type: 'message', data });
          } catch (error) {
            console.error('Failed to parse WebSocket message:', error);
          }
        };
        
        websocket.onerror = (error) => {
          console.error('WebSocket error:', error);
        };
        
        websocket.onclose = (event) => {
          clearInterval(heartbeatInterval);
          
          if (this.reconnectAttempts < this.maxReconnectAttempts) {
            const delay = this.reconnectInterval * Math.pow(2, this.reconnectAttempts);
            console.log(`WebSocket disconnected, reconnecting in ${delay}ms...`);
            
            setTimeout(() => {
              this.reconnectAttempts++;
              connect();
            }, delay);
          } else {
            subscriber.error(new Error('Max reconnection attempts reached'));
          }
        };
      };
      
      connect();
      
      // Cleanup function
      return () => {
        clearInterval(heartbeatInterval);
        if (websocket) {
          websocket.close();
        }
      };
    }).pipe(
      share() // Share connection among multiple subscribers
    );
  }
  
  // ✅ Subscribe to specific data streams
  subscribeToDataStream(streamType: string): Observable<any> {
    return this.websocket$.pipe(
      filter(message => message.type === 'message'),
      map(message => message.data),
      filter(data => data.stream === streamType),
      map(data => data.payload),
      retry({
        count: 3,
        delay: (error, retryCount) => timer(1000 * retryCount)
      })
    );
  }
  
  // ✅ Hybrid data loading (cache + real-time updates)
  getDataWithRealtimeUpdates<T>(
    endpoint: string, 
    streamType: string,
    cacheKey?: string
  ): Observable<T> {
    
    // Initial data from HTTP
    const initialData$ = this.http.get<T>(endpoint).pipe(
      tap(data => {
        if (cacheKey) {
          this.cacheData(cacheKey, data);
        }
      }),
      catchError(error => {
        console.error('Failed to load initial data:', error);
        // Try to return cached data
        const cached = cacheKey ? this.getCachedData(cacheKey) : null;
        return cached ? of(cached) : EMPTY;
      })
    );
    
    // Real-time updates
    const realtimeUpdates$ = this.subscribeToDataStream(streamType);
    
    // Combine initial data with real-time updates
    return initialData$.pipe(
      switchMap(initialData => 
        merge(
          of(initialData),
          realtimeUpdates$.pipe(
            scan((currentData: T, update: any) => 
              this.applyRealtimeUpdate(currentData, update), initialData
            )
          )
        )
      )
    );
  }
  
  private applyRealtimeUpdate<T>(currentData: T, update: any): T {
    switch (update.type) {
      case 'UPDATE':
        return { ...currentData, ...update.data };
      
      case 'INSERT':
        if (Array.isArray(currentData)) {
          return [...currentData, update.data] as unknown as T;
        }
        break;
      
      case 'DELETE':
        if (Array.isArray(currentData)) {
          return currentData.filter((item: any) => item.id !== update.id) as unknown as T;
        }
        break;
      
      case 'REPLACE':
        return update.data;
      
      default:
        console.warn('Unknown update type:', update.type);
        return currentData;
    }
    
    return currentData;
  }
  
  private cacheData(key: string, data: any) {
    try {
      localStorage.setItem(key, JSON.stringify({
        data,
        timestamp: Date.now()
      }));
    } catch (error) {
      console.warn('Failed to cache data:', error);
    }
  }
  
  private getCachedData(key: string): any {
    try {
      const cached = localStorage.getItem(key);
      if (cached) {
        const { data, timestamp } = JSON.parse(cached);
        const age = Date.now() - timestamp;
        
        // Return cached data if less than 5 minutes old
        if (age < 300000) {
          return data;
        }
      }
    } catch (error) {
      console.warn('Failed to retrieve cached data:', error);
    }
    
    return null;
  }
}
```

### **⚡ RxJS Advanced Patterns for Angular**
```typescript
// 🎯 RXJS MASTERY FOR ANGULAR INTERVIEWS

// 1. ADVANCED STATE MANAGEMENT WITH RXJS
@Injectable({
  providedIn: 'root'
})
export class StateManagementService {
  
  // ✅ State subjects
  private userState$ = new BehaviorSubject<User | null>(null);
  private loadingStates$ = new BehaviorSubject<Map<string, boolean>>(new Map());
  private errorStates$ = new BehaviorSubject<Map<string, string>>(new Map());
  
  // ✅ Public state observables
  user$ = this.userState$.asObservable();
  isLoading$ = (operation: string) => 
    this.loadingStates$.pipe(map(states => states.get(operation) || false));
  error$ = (operation: string) => 
    this.errorStates$.pipe(map(states => states.get(operation) || null));
  
  // ✅ Derived state
  isAuthenticated$ = this.user$.pipe(map(user => !!user));
  userPermissions$ = this.user$.pipe(
    map(user => user?.permissions || []),
    shareReplay(1)
  );
  
  // ✅ Complex state operations
  updateUserProfile(updates: Partial<User>): Observable<User> {
    return this.executeWithState(
      'updateProfile',
      this.userService.updateProfile(updates).pipe(
        tap(updatedUser => {
          this.userState$.next(updatedUser);
        })
      )
    );
  }
  
  // ✅ Generic state management wrapper
  private executeWithState<T>(
    operation: string, 
    source$: Observable<T>
  ): Observable<T> {
    
    this.setLoading(operation, true);
    this.clearError(operation);
    
    return source$.pipe(
      finalize(() => this.setLoading(operation, false)),
      catchError(error => {
        this.setError(operation, error.message);
        return throwError(() => error);
      })
    );
  }
  
  private setLoading(operation: string, isLoading: boolean) {
    const currentStates = this.loadingStates$.value;
    const newStates = new Map(currentStates);
    newStates.set(operation, isLoading);
    this.loadingStates$.next(newStates);
  }
  
  private setError(operation: string, error: string) {
    const currentStates = this.errorStates$.value;
    const newStates = new Map(currentStates);
    newStates.set(operation, error);
    this.errorStates$.next(newStates);
  }
  
  private clearError(operation: string) {
    const currentStates = this.errorStates$.value;
    const newStates = new Map(currentStates);
    newStates.delete(operation);
    this.errorStates$.next(newStates);
  }
}

// 2. ADVANCED HTTP PATTERNS
@Injectable({
  providedIn: 'root'
})
export class HttpService {
  
  constructor(private http: HttpClient) {}
  
  // ✅ Intelligent caching with cache invalidation
  private cache = new Map<string, { data: any; timestamp: number; ttl: number }>();
  
  getWithCache<T>(
    url: string, 
    options: { ttl?: number; bypassCache?: boolean } = {}
  ): Observable<T> {
    const { ttl = 300000, bypassCache = false } = options; // 5 min default TTL
    
    if (!bypassCache) {
      const cached = this.cache.get(url);
      if (cached && (Date.now() - cached.timestamp) < cached.ttl) {
        return of(cached.data);
      }
    }
    
    return this.http.get<T>(url).pipe(
      tap(data => {
        this.cache.set(url, {
          data,
          timestamp: Date.now(),
          ttl
        });
      }),
      shareReplay(1)
    );
  }
  
  // ✅ Request deduplication
  private pendingRequests = new Map<string, Observable<any>>();
  
  getWithDeduplication<T>(url: string): Observable<T> {
    if (this.pendingRequests.has(url)) {
      return this.pendingRequests.get(url)!;
    }
    
    const request$ = this.http.get<T>(url).pipe(
      finalize(() => {
        this.pendingRequests.delete(url);
      }),
      shareReplay(1)
    );
    
    this.pendingRequests.set(url, request$);
    return request$;
  }
  
  // ✅ Batch requests with intelligent grouping
  batchRequests<T>(requests: BatchRequest[]): Observable<BatchResponse<T>[]> {
    // Group requests by domain and delay
    const batches = this.groupRequestsIntoBatches(requests);
    
    return from(batches).pipe(
      mergeMap(batch => this.executeBatch<T>(batch), 3), // Max 3 concurrent batches
      toArray()
    );
  }
  
  private groupRequestsIntoBatches(requests: BatchRequest[]): BatchRequest[][] {
    const groups = new Map<string, BatchRequest[]>();
    
    requests.forEach(request => {
      const domain = new URL(request.url).hostname;
      if (!groups.has(domain)) {
        groups.set(domain, []);
      }
      groups.get(domain)!.push(request);
    });
    
    // Split large groups into smaller batches
    const batches: BatchRequest[][] = [];
    groups.forEach(group => {
      while (group.length > 0) {
        batches.push(group.splice(0, 10)); // Max 10 requests per batch
      }
    });
    
    return batches;
  }
  
  private executeBatch<T>(batch: BatchRequest[]): Observable<BatchResponse<T>> {
    const batchRequests$ = batch.map(request => 
      this.http.request(request.method, request.url, request.options).pipe(
        map(response => ({ id: request.id, success: true, data: response } as BatchResponse<T>)),
        catchError(error => of({ id: request.id, success: false, error } as BatchResponse<T>))
      )
    );
    
    return combineLatest(batchRequests$);
  }
  
  // ✅ Progressive data loading
  getProgressiveData<T>(url: string): Observable<T> {
    return new Observable<T>(subscriber => {
      // Start with cached data if available
      const cached = this.cache.get(url);
      if (cached) {
        subscriber.next(cached.data);
      }
      
      // Then fetch fresh data
      this.http.get<T>(url).subscribe({
        next: freshData => {
          this.cache.set(url, {
            data: freshData,
            timestamp: Date.now(),
            ttl: 300000
          });
          subscriber.next(freshData);
          subscriber.complete();
        },
        error: error => {
          if (!cached) {
            subscriber.error(error);
          } else {
            // If we have cached data, just complete silently
            subscriber.complete();
          }
        }
      });
    });
  }
}

// Supporting interfaces
interface BatchRequest {
  id: string;
  method: string;
  url: string;
  options?: any;
}

interface BatchResponse<T> {
  id: string;
  success: boolean;
  data?: T;
  error?: any;
}

// 3. CUSTOM RXJS OPERATORS FOR ANGULAR
export function retryWithBackoffAndCircuitBreaker<T>(
  maxRetries: number = 3,
  baseDelay: number = 1000,
  maxDelay: number = 30000,
  circuitBreakerThreshold: number = 5
) {
  let consecutiveFailures = 0;
  let circuitOpen = false;
  let circuitOpenUntil = 0;
  
  return (source: Observable<T>) => {
    return new Observable<T>(subscriber => {
      let retryCount = 0;
      
      const attempt = () => {
        // Check circuit breaker
        if (circuitOpen && Date.now() < circuitOpenUntil) {
          subscriber.error(new Error('Circuit breaker is open'));
          return;
        }
        
        if (circuitOpen && Date.now() >= circuitOpenUntil) {
          // Reset circuit breaker
          circuitOpen = false;
          consecutiveFailures = 0;
        }
        
        const subscription = source.subscribe({
          next: value => {
            consecutiveFailures = 0; // Reset on success
            subscriber.next(value);
          },
          complete: () => subscriber.complete(),
          error: error => {
            consecutiveFailures++;
            
            // Check if circuit should open
            if (consecutiveFailures >= circuitBreakerThreshold) {
              circuitOpen = true;
              circuitOpenUntil = Date.now() + 60000; // Open for 1 minute
            }
            
            if (retryCount < maxRetries && !circuitOpen) {
              const delay = Math.min(
                baseDelay * Math.pow(2, retryCount),
                maxDelay
              );
              
              retryCount++;
              setTimeout(attempt, delay);
            } else {
              subscriber.error(error);
            }
          }
        });
      };
      
      attempt();
    });
  };
}

// ✅ Smart debounce that handles different patterns
export function smartDebounce<T>(
  config: {
    time: number;
    immediate?: boolean;
    resetOnEmpty?: boolean;
    maxWait?: number;
  }
) {
  const { time, immediate = false, resetOnEmpty = false, maxWait } = config;
  
  return (source: Observable<T>) => {
    return new Observable<T>(subscriber => {
      let timer: any;
      let lastEmission = 0;
      let lastValue: T;
      let hasEmitted = false;
      
      const subscription = source.subscribe({
        next: value => {
          lastValue = value;
          const now = Date.now();
          
          clearTimeout(timer);
          
          // Immediate emission for first value
          if (immediate && !hasEmitted) {
            hasEmitted = true;
            lastEmission = now;
            subscriber.next(value);
            return;
          }
          
          // Reset behavior for empty values
          if (resetOnEmpty && (!value || (typeof value === 'string' && value.trim() === ''))) {
            hasEmitted = false;
            return;
          }
          
          // Max wait enforcement
          if (maxWait && (now - lastEmission) >= maxWait) {
            lastEmission = now;
            subscriber.next(value);
            return;
          }
          
          // Regular debounce
          timer = setTimeout(() => {
            lastEmission = Date.now();
            subscriber.next(lastValue);
          }, time);
        },
        error: error => {
          clearTimeout(timer);
          subscriber.error(error);
        },
        complete: () => {
          clearTimeout(timer);
          subscriber.complete();
        }
      });
      
      return () => {
        clearTimeout(timer);
        subscription.unsubscribe();
      };
    });
  };
}

// ✅ Memory-efficient infinite scroll
export function infiniteScroll<T>(
  windowSize: number = 100,
  loadMore: () => Observable<T[]>
) {
  return (source: Observable<T[]>) => {
    return new Observable<T[]>(subscriber => {
      let allItems: T[] = [];
      let isLoading = false;
      
      const subscription = source.subscribe({
        next: items => {
          allItems = [...allItems, ...items];
          
          // Keep only recent items to prevent memory issues
          if (allItems.length > windowSize * 2) {
            allItems = allItems.slice(-windowSize);
          }
          
          subscriber.next(allItems);
        },
        error: error => subscriber.error(error),
        complete: () => subscriber.complete()
      });
      
      return () => subscription.unsubscribe();
    });
  };
}
```

---

## 🔥 **COMPANY-TIER ASYNC CHALLENGES**

### **🏆 Tier 1 Company Challenges (Google, Microsoft, Meta)**

#### **🎯 CHALLENGE 1: "Build a Resilient Real-time Data Pipeline"**
```typescript
// INTERVIEWER: "Design a system that handles millions of real-time events with guaranteed delivery"
// TIME LIMIT: 45 minutes
// EVALUATION: Architecture design, performance optimization, error handling, scalability

interface EventStreamConfig {
  maxRetries: number;
  batchSize: number;
  flushInterval: number;
  backpressureThreshold: number;
  compressionEnabled: boolean;
}

interface StreamEvent {
  id: string;
  type: string;
  timestamp: number;
  data: any;
  priority: 'low' | 'medium' | 'high' | 'critical';
  retryCount?: number;
}

interface StreamMetrics {
  eventsProcessed: number;
  eventsDropped: number;
  averageLatency: number;
  currentBacklog: number;
  errorRate: number;
}

class ResilientEventStreamProcessor {
  
  private config: EventStreamConfig;
  private eventBuffer: StreamEvent[] = [];
  private priorityQueues: Map<string, StreamEvent[]> = new Map();
  private deadLetterQueue: StreamEvent[] = [];
  private metrics: StreamMetrics;
  private isProcessing = false;
  private backpressureActive = false;
  
  // Circuit breakers for different downstream services
  private circuitBreakers: Map<string, CircuitBreaker> = new Map();
  
  // Event subjects for monitoring
  private metricsSubject = new BehaviorSubject<StreamMetrics>(this.initializeMetrics());
  private backpressureSubject = new BehaviorSubject<boolean>(false);
  
  constructor(config: EventStreamConfig) {
    this.config = config;
    this.metrics = this.initializeMetrics();
    this.initializePriorityQueues();
    this.startProcessing();
    this.startMetricsCollection();
  }
  
  // ✅ Main event ingestion with backpressure handling
  ingestEvent(event: StreamEvent): Observable<{ accepted: boolean; reason?: string }> {
    return new Observable(subscriber => {
      try {
        // Check backpressure
        if (this.shouldApplyBackpressure()) {
          if (event.priority === 'critical') {
            // Always accept critical events, but drop low priority ones
            this.evictLowPriorityEvents();
          } else {
            subscriber.next({ accepted: false, reason: 'Backpressure active' });
            subscriber.complete();
            return;
          }
        }
        
        // Validate event
        if (!this.validateEvent(event)) {
          subscriber.next({ accepted: false, reason: 'Invalid event format' });
          subscriber.complete();
          return;
        }
        
        // Add to appropriate priority queue
        this.addToQueue(event);
        
        subscriber.next({ accepted: true });
        subscriber.complete();
        
      } catch (error) {
        subscriber.error(error);
      }
    });
  }
  
  // ✅ Intelligent priority-based processing
  private async startProcessing() {
    this.isProcessing = true;
    
    while (this.isProcessing) {
      try {
        const batch = this.getNextBatch();
        
        if (batch.length === 0) {
          await this.delay(100); // Brief pause when no events
          continue;
        }
        
        await this.processBatch(batch);
        
      } catch (error) {
        console.error('Processing error:', error);
        await this.delay(1000); // Longer pause on error
      }
    }
  }
  
  private getNextBatch(): StreamEvent[] {
    const batch: StreamEvent[] = [];
    const maxBatchSize = this.config.batchSize;
    
    // Priority order: critical -> high -> medium -> low
    const priorities = ['critical', 'high', 'medium', 'low'];
    
    for (const priority of priorities) {
      const queue = this.priorityQueues.get(priority) || [];
      
      while (queue.length > 0 && batch.length < maxBatchSize) {
        const event = queue.shift()!;
        batch.push(event);
      }
      
      if (batch.length >= maxBatchSize) break;
    }
    
    return batch;
  }
  
  // ✅ Batch processing with parallel execution and error handling
  private async processBatch(batch: StreamEvent[]): Promise<void> {
    const startTime = Date.now();
    
    // Group events by destination service
    const serviceGroups = this.groupEventsByService(batch);
    
    // Process each service group in parallel
    const processingPromises = Array.from(serviceGroups.entries()).map(
      ([service, events]) => this.processServiceBatch(service, events)
    );
    
    const results = await Promise.allSettled(processingPromises);
    
    // Handle results and update metrics
    this.updateBatchMetrics(batch, results, Date.now() - startTime);
  }
  
  private async processServiceBatch(service: string, events: StreamEvent[]): Promise<void> {
    const circuitBreaker = this.getOrCreateCircuitBreaker(service);
    
    try {
      await circuitBreaker.execute(async () => {
        // Simulate service call with compression
        const payload = this.config.compressionEnabled 
          ? this.compressEvents(events)
          : events;
        
        await this.sendToService(service, payload);
        
        // Update success metrics
        this.metrics.eventsProcessed += events.length;
      });
      
    } catch (error) {
      console.error(`Failed to process batch for service ${service}:`, error);
      
      // Handle failed events based on retry policy
      await this.handleFailedEvents(events, error);
    }
  }
  
  // ✅ Sophisticated retry and dead letter queue handling
  private async handleFailedEvents(events: StreamEvent[], error: any): Promise<void> {
    const retryableEvents: StreamEvent[] = [];
    const deadLetterEvents: StreamEvent[] = [];
    
    for (const event of events) {
      const retryCount = (event.retryCount || 0) + 1;
      
      if (retryCount <= this.config.maxRetries && this.isRetryableError(error)) {
        retryableEvents.push({
          ...event,
          retryCount
        });
      } else {
        deadLetterEvents.push(event);
      }
    }
    
    // Re-queue retryable events with exponential backoff
    if (retryableEvents.length > 0) {
      setTimeout(() => {
        retryableEvents.forEach(event => this.addToQueue(event));
      }, this.calculateRetryDelay(retryableEvents[0].retryCount || 1));
    }
    
    // Send to dead letter queue
    if (deadLetterEvents.length > 0) {
      this.deadLetterQueue.push(...deadLetterEvents);
      this.metrics.eventsDropped += deadLetterEvents.length;
    }
  }
  
  // ✅ Advanced backpressure management
  private shouldApplyBackpressure(): boolean {
    const totalQueueSize = Array.from(this.priorityQueues.values())
      .reduce((total, queue) => total + queue.length, 0);
    
    const newBackpressureState = totalQueueSize > this.config.backpressureThreshold;
    
    if (newBackpressureState !== this.backpressureActive) {
      this.backpressureActive = newBackpressureState;
      this.backpressureSubject.next(newBackpressureState);
    }
    
    return this.backpressureActive;
  }
  
  private evictLowPriorityEvents(): void {
    const lowPriorityQueue = this.priorityQueues.get('low') || [];
    const evictCount = Math.min(lowPriorityQueue.length, 100);
    
    const evicted = lowPriorityQueue.splice(0, evictCount);
    this.metrics.eventsDropped += evicted.length;
    
    console.warn(`Evicted ${evictCount} low priority events due to backpressure`);
  }
  
  // ✅ Real-time metrics and monitoring
  private startMetricsCollection(): void {
    setInterval(() => {
      this.calculateAndEmitMetrics();
    }, 5000); // Update metrics every 5 seconds
  }
  
  private calculateAndEmitMetrics(): void {
    const totalQueueSize = Array.from(this.priorityQueues.values())
      .reduce((total, queue) => total + queue.length, 0);
    
    this.metrics.currentBacklog = totalQueueSize;
    this.metricsSubject.next({ ...this.metrics });
  }
  
  // ✅ Performance optimization methods
  private compressEvents(events: StreamEvent[]): string {
    // Simple compression simulation
    return JSON.stringify(events);
  }
  
  private groupEventsByService(events: StreamEvent[]): Map<string, StreamEvent[]> {
    const groups = new Map<string, StreamEvent[]>();
    
    for (const event of events) {
      const service = this.determineTargetService(event);
      
      if (!groups.has(service)) {
        groups.set(service, []);
      }
      
      groups.get(service)!.push(event);
    }
    
    return groups;
  }
  
  private determineTargetService(event: StreamEvent): string {
    // Route events based on type
    switch (event.type) {
      case 'user_action': return 'analytics-service';
      case 'system_alert': return 'monitoring-service';
      case 'business_event': return 'business-service';
      default: return 'default-service';
    }
  }
  
  // ✅ Circuit breaker management
  private getOrCreateCircuitBreaker(service: string): CircuitBreaker {
    if (!this.circuitBreakers.has(service)) {
      this.circuitBreakers.set(service, new CircuitBreaker({
        failureThreshold: 5,
        resetTimeout: 30000,
        monitoringWindow: 60000
      }));
    }
    
    return this.circuitBreakers.get(service)!;
  }
  
  // ✅ Helper methods
  private validateEvent(event: StreamEvent): boolean {
    return !!(event.id && event.type && event.timestamp && event.data);
  }
  
  private addToQueue(event: StreamEvent): void {
    const queue = this.priorityQueues.get(event.priority) || [];
    queue.push(event);
  }
  
  private initializePriorityQueues(): void {
    ['critical', 'high', 'medium', 'low'].forEach(priority => {
      this.priorityQueues.set(priority, []);
    });
  }
  
  private initializeMetrics(): StreamMetrics {
    return {
      eventsProcessed: 0,
      eventsDropped: 0,
      averageLatency: 0,
      currentBacklog: 0,
      errorRate: 0
    };
  }
  
  private isRetryableError(error: any): boolean {
    // Network errors and 5xx responses are retryable
    return error.status >= 500 || error.code === 'NETWORK_ERROR';
  }
  
  private calculateRetryDelay(retryCount: number): number {
    return Math.min(1000 * Math.pow(2, retryCount - 1), 30000);
  }
  
  private async sendToService(service: string, payload: any): Promise<void> {
    // Simulate service call
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (Math.random() > 0.95) { // 5% failure rate
          reject(new Error('Service temporarily unavailable'));
        } else {
          resolve();
        }
      }, Math.random() * 100);
    });
  }
  
  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
  
  private updateBatchMetrics(
    batch: StreamEvent[], 
    results: PromiseSettledResult<void>[], 
    duration: number
  ): void {
    const successCount = results.filter(r => r.status === 'fulfilled').length;
    const failureCount = results.filter(r => r.status === 'rejected').length;
    
    this.metrics.errorRate = (this.metrics.errorRate + (failureCount / batch.length)) / 2;
    this.metrics.averageLatency = (this.metrics.averageLatency + duration) / 2;
  }
  
  // ✅ Public monitoring interface
  getMetrics(): Observable<StreamMetrics> {
    return this.metricsSubject.asObservable();
  }
  
  getBackpressureStatus(): Observable<boolean> {
    return this.backpressureSubject.asObservable();
  }
  
  getDeadLetterQueue(): StreamEvent[] {
    return [...this.deadLetterQueue];
  }
  
  // ✅ Graceful shutdown
  async shutdown(): Promise<void> {
    this.isProcessing = false;
    
    // Wait for current processing to complete
    while (this.getQueueSize() > 0) {
      await this.delay(100);
    }
    
    console.log('Event stream processor shut down gracefully');
  }
  
  private getQueueSize(): number {
    return Array.from(this.priorityQueues.values())
      .reduce((total, queue) => total + queue.length, 0);
  }
}

// INTERVIEW DISCUSSION POINTS:
// - How does priority-based processing prevent starvation?
// - What are the trade-offs between throughput and latency?
// - How would you handle distributed event ordering?
// - What additional monitoring would you implement for production?

// Usage example
const processor = new ResilientEventStreamProcessor({
  maxRetries: 3,
  batchSize: 100,
  flushInterval: 5000,
  backpressureThreshold: 10000,
  compressionEnabled: true
});

// Monitor metrics
processor.getMetrics().subscribe(metrics => {
  console.log('Stream metrics:', metrics);
});

processor.getBackpressureStatus().subscribe(active => {
  if (active) {
    console.warn('Backpressure activated - slowing down event ingestion');
  }
});
```

#### **🎯 CHALLENGE 2: "Multi-Source Data Synchronization Engine"**
```typescript
// INTERVIEWER: "Build a system that synchronizes data from multiple sources with conflict resolution"
// TIME LIMIT: 40 minutes
// EVALUATION: Data consistency, conflict resolution, performance optimization

interface DataSource {
  id: string;
  name: string;
  priority: number;
  trustLevel: number; // 0-1, higher means more trusted
  latency: number; // Expected response time in ms
}

interface DataRecord {
  id: string;
  sourceId: string;
  timestamp: number;
  version: string;
  data: any;
  checksum: string;
}

interface ConflictResolution {
  strategy: 'timestamp' | 'priority' | 'trust' | 'merge' | 'manual';
  customResolver?: (records: DataRecord[]) => DataRecord;
}

interface SyncMetrics {
  totalRecords: number;
  conflictsDetected: number;
  conflictsResolved: number;
  averageSyncTime: number;
  dataFreshness: Map<string, number>;
}

class MultiSourceSyncEngine {
  
  private dataSources: Map<string, DataSource> = new Map();
  private currentData: Map<string, DataRecord> = new Map();
  private conflictResolutions: Map<string, ConflictResolution> = new Map();
  private syncHistory: Map<string, DataRecord[]> = new Map();
  
  private metricsSubject = new BehaviorSubject<SyncMetrics>(this.initializeMetrics());
  private dataChangeSubject = new Subject<{ id: string; record: DataRecord }>();
  private conflictSubject = new Subject<{ records: DataRecord[]; resolution: DataRecord }>();
  
  private syncIntervals: Map<string, any> = new Map();
  private isShuttingDown = false;
  
  constructor() {
    this.setupDefaultConflictResolutions();
  }
  
  // ✅ Register data sources with intelligent polling
  registerDataSource(source: DataSource, pollInterval: number): void {
    this.dataSources.set(source.id, source);
    
    // Start intelligent polling based on source characteristics
    const interval = setInterval(async () => {
      if (!this.isShuttingDown) {
        await this.pollDataSource(source.id);
      }
    }, this.calculateOptimalPollInterval(source, pollInterval));
    
    this.syncIntervals.set(source.id, interval);
    
    console.log(`Registered data source: ${source.name} with ${pollInterval}ms polling`);
  }
  
  // ✅ Intelligent polling with adaptive intervals
  private calculateOptimalPollInterval(source: DataSource, baseInterval: number): number {
    // Adjust interval based on source characteristics
    const latencyFactor = Math.max(0.5, source.latency / 1000); // Slower sources poll less frequently
    const priorityFactor = Math.max(0.5, (10 - source.priority) / 10); // Higher priority = more frequent
    
    return Math.round(baseInterval * latencyFactor * priorityFactor);
  }
  
  // ✅ Data polling with caching and change detection
  private async pollDataSource(sourceId: string): Promise<void> {
    const source = this.dataSources.get(sourceId);
    if (!source) return;
    
    try {
      const startTime = Date.now();
      
      // Simulate data fetch from source
      const rawData = await this.fetchFromSource(sourceId);
      const records = this.parseSourceData(sourceId, rawData);
      
      // Process each record
      for (const record of records) {
        await this.processDataRecord(record);
      }
      
      // Update metrics
      this.updateSourceMetrics(sourceId, Date.now() - startTime, records.length);
      
    } catch (error) {
      console.error(`Failed to poll source ${sourceId}:`, error);
      this.handleSourceError(sourceId, error);
    }
  }
  
  // ✅ Sophisticated conflict detection and resolution
  private async processDataRecord(newRecord: DataRecord): Promise<void> {
    const existingRecord = this.currentData.get(newRecord.id);
    
    if (!existingRecord) {
      // New record - store directly
      this.storeRecord(newRecord);
      return;
    }
    
    // Check for conflicts
    const hasConflict = this.detectConflict(existingRecord, newRecord);
    
    if (!hasConflict) {
      // No conflict - update if newer
      if (newRecord.timestamp > existingRecord.timestamp) {
        this.storeRecord(newRecord);
      }
      return;
    }
    
    // Resolve conflict
    const resolvedRecord = await this.resolveConflict([existingRecord, newRecord]);
    this.storeRecord(resolvedRecord);
    
    // Emit conflict resolution event
    this.conflictSubject.next({
      records: [existingRecord, newRecord],
      resolution: resolvedRecord
    });
  }
  
  // ✅ Multi-strategy conflict resolution
  private async resolveConflict(conflictingRecords: DataRecord[]): Promise<DataRecord> {
    const recordId = conflictingRecords[0].id;
    const resolution = this.conflictResolutions.get(recordId) || 
                      this.conflictResolutions.get('default')!;
    
    switch (resolution.strategy) {
      case 'timestamp':
        return this.resolveByTimestamp(conflictingRecords);
      
      case 'priority':
        return this.resolveBySourcePriority(conflictingRecords);
      
      case 'trust':
        return this.resolveByTrustLevel(conflictingRecords);
      
      case 'merge':
        return this.resolveByMerging(conflictingRecords);
      
      case 'manual':
        if (resolution.customResolver) {
          return resolution.customResolver(conflictingRecords);
        }
        // Fallback to timestamp
        return this.resolveByTimestamp(conflictingRecords);
      
      default:
        return this.resolveByTimestamp(conflictingRecords);
    }
  }
  
  private resolveByTimestamp(records: DataRecord[]): DataRecord {
    return records.reduce((latest, current) => 
      current.timestamp > latest.timestamp ? current : latest
    );
  }
  
  private resolveBySourcePriority(records: DataRecord[]): DataRecord {
    return records.reduce((highest, current) => {
      const currentSource = this.dataSources.get(current.sourceId);
      const highestSource = this.dataSources.get(highest.sourceId);
      
      if (!currentSource || !highestSource) return highest;
      
      return currentSource.priority > highestSource.priority ? current : highest;
    });
  }
  
  private resolveByTrustLevel(records: DataRecord[]): DataRecord {
    return records.reduce((mostTrusted, current) => {
      const currentSource = this.dataSources.get(current.sourceId);
      const trustedSource = this.dataSources.get(mostTrusted.sourceId);
      
      if (!currentSource || !trustedSource) return mostTrusted;
      
      return currentSource.trustLevel > trustedSource.trustLevel ? current : mostTrusted;
    });
  }
  
  private resolveByMerging(records: DataRecord[]): DataRecord {
    // Create merged record with data from all sources
    const baseRecord = this.resolveByTimestamp(records);
    const mergedData = { ...baseRecord.data };
    
    // Merge data from all records, with priority-based override
    const sortedRecords = records.sort((a, b) => {
      const sourceA = this.dataSources.get(a.sourceId);
      const sourceB = this.dataSources.get(b.sourceId);
      return (sourceB?.priority || 0) - (sourceA?.priority || 0);
    });
    
    for (const record of sortedRecords) {
      Object.assign(mergedData, record.data);
    }
    
    return {
      ...baseRecord,
      data: mergedData,
      sourceId: 'merged',
      checksum: this.calculateChecksum(mergedData)
    };
  }
  
  // ✅ Conflict detection with multiple criteria
  private detectConflict(existing: DataRecord, incoming: DataRecord): boolean {
    // Same version - no conflict
    if (existing.version === incoming.version) {
      return false;
    }
    
    // Different checksums indicate data conflict
    if (existing.checksum !== incoming.checksum) {
      return true;
    }
    
    // Deep data comparison for complex objects
    return !this.deepEqual(existing.data, incoming.data);
  }
  
  // ✅ Advanced data freshness tracking
  private storeRecord(record: DataRecord): void {
    this.currentData.set(record.id, record);
    
    // Update sync history
    if (!this.syncHistory.has(record.id)) {
      this.syncHistory.set(record.id, []);
    }
    
    const history = this.syncHistory.get(record.id)!;
    history.push(record);
    
    // Keep only last 10 versions
    if (history.length > 10) {
      history.shift();
    }
    
    // Emit data change event
    this.dataChangeSubject.next({ id: record.id, record });
    
    // Update metrics
    this.updateDataFreshness(record.id, record.timestamp);
  }
  
  // ✅ Real-time data access with freshness guarantees
  getData(id: string, maxAge?: number): Observable<DataRecord | null> {
    return new Observable<DataRecord | null>(subscriber => {
      const current = this.currentData.get(id);
      
      if (!current) {
        subscriber.next(null);
        subscriber.complete();
        return;
      }
      
      // Check data freshness
      if (maxAge && (Date.now() - current.timestamp) > maxAge) {
        // Trigger immediate refresh
        this.triggerImmediateSync(id).then(() => {
          const refreshed = this.currentData.get(id);
          subscriber.next(refreshed || current);
          subscriber.complete();
        });
      } else {
        subscriber.next(current);
        subscriber.complete();
      }
    });
  }
  
  // ✅ Bulk data operations with batching
  getBulkData(ids: string[], maxAge?: number): Observable<Map<string, DataRecord>> {
    return new Observable<Map<string, DataRecord>>(subscriber => {
      const result = new Map<string, DataRecord>();
      const staleIds: string[] = [];
      
      // Check current data and identify stale records
      for (const id of ids) {
        const record = this.currentData.get(id);
        
        if (record) {
          if (maxAge && (Date.now() - record.timestamp) > maxAge) {
            staleIds.push(id);
          } else {
            result.set(id, record);
          }
        }
      }
      
      // If no stale data, return immediately
      if (staleIds.length === 0) {
        subscriber.next(result);
        subscriber.complete();
        return;
      }
      
      // Refresh stale data
      this.triggerBulkSync(staleIds).then(() => {
        // Add refreshed data to result
        for (const id of staleIds) {
          const record = this.currentData.get(id);
          if (record) {
            result.set(id, record);
          }
        }
        
        subscriber.next(result);
        subscriber.complete();
      });
    });
  }
  
  // ✅ Monitoring and metrics
  getMetrics(): Observable<SyncMetrics> {
    return this.metricsSubject.asObservable();
  }
  
  getConflictStream(): Observable<{ records: DataRecord[]; resolution: DataRecord }> {
    return this.conflictSubject.asObservable();
  }
  
  getDataChangeStream(): Observable<{ id: string; record: DataRecord }> {
    return this.dataChangeSubject.asObservable();
  }
  
  // ✅ Configuration and management
  setConflictResolution(recordId: string, resolution: ConflictResolution): void {
    this.conflictResolutions.set(recordId, resolution);
  }
  
  // ✅ Helper methods
  private async fetchFromSource(sourceId: string): Promise<any> {
    // Simulate async data fetch
    const latency = this.dataSources.get(sourceId)?.latency || 1000;
    await this.delay(latency);
    
    return {
      records: [
        { id: 'user1', data: { name: 'John', age: 30 }, version: '1.0' },
        { id: 'user2', data: { name: 'Jane', age: 25 }, version: '1.0' }
      ]
    };
  }
  
  private parseSourceData(sourceId: string, rawData: any): DataRecord[] {
    return rawData.records.map((record: any) => ({
      id: record.id,
      sourceId,
      timestamp: Date.now(),
      version: record.version,
      data: record.data,
      checksum: this.calculateChecksum(record.data)
    }));
  }
  
  private calculateChecksum(data: any): string {
    return btoa(JSON.stringify(data)).substring(0, 8);
  }
  
  private deepEqual(obj1: any, obj2: any): boolean {
    return JSON.stringify(obj1) === JSON.stringify(obj2);
  }
  
  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
  
  private async triggerImmediateSync(id: string): Promise<void> {
    // Implementation for immediate single record sync
  }
  
  private async triggerBulkSync(ids: string[]): Promise<void> {
    // Implementation for bulk record sync
  }
  
  private setupDefaultConflictResolutions(): void {
    this.conflictResolutions.set('default', { strategy: 'timestamp' });
  }
  
  private initializeMetrics(): SyncMetrics {
    return {
      totalRecords: 0,
      conflictsDetected: 0,
      conflictsResolved: 0,
      averageSyncTime: 0,
      dataFreshness: new Map()
    };
  }
  
  private updateSourceMetrics(sourceId: string, duration: number, recordCount: number): void {
    // Update metrics implementation
  }
  
  private updateDataFreshness(id: string, timestamp: number): void {
    const metrics = this.metricsSubject.value;
    metrics.dataFreshness.set(id, timestamp);
    this.metricsSubject.next(metrics);
  }
  
  private handleSourceError(sourceId: string, error: any): void {
    console.error(`Source ${sourceId} error:`, error);
  }
  
  // ✅ Graceful shutdown
  async shutdown(): Promise<void> {
    this.isShuttingDown = true;
    
    // Clear all intervals
    for (const interval of this.syncIntervals.values()) {
      clearInterval(interval);
    }
    
    console.log('Multi-source sync engine shut down gracefully');
  }
}

// INTERVIEW DISCUSSION POINTS:
// - How would you handle network partitions between sources?
// - What optimizations would you add for high-frequency updates?
// - How would you implement eventual consistency guarantees?
// - What additional conflict resolution strategies would be valuable?
```

### **🏅 Tier 2 Company Challenges (Netflix, Uber, Airbnb)**

#### **🎯 CHALLENGE 1: "Smart Request Orchestration System"**
```typescript
// INTERVIEWER: "Design a system that intelligently manages API requests with adaptive behavior"
// TIME LIMIT: 35 minutes
// EVALUATION: Request optimization, caching strategies, error recovery, performance

interface RequestConfig {
  url: string;
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  priority: number;
  timeout: number;
  retryable: boolean;
  cacheable: boolean;
  dependencies?: string[];
}

interface RequestResult<T> {
  data: T;
  source: 'cache' | 'network' | 'fallback';
  latency: number;
  timestamp: number;
}

class SmartRequestOrchestrator {
  
  private requestQueue: Map<string, RequestConfig> = new Map();
  private cache: Map<string, { data: any; timestamp: number; ttl: number }> = new Map();
  private pendingRequests: Map<string, Observable<any>> = new Map();
  private requestMetrics: Map<string, RequestMetrics> = new Map();
  
  // Adaptive configuration
  private adaptiveConfig = {
    maxConcurrentRequests: 6,
    defaultTimeout: 5000,
    cacheTTL: 300000, // 5 minutes
    adaptiveTimeouts: true,
    intelligentRetry: true
  };
  
  constructor() {
    this.startAdaptiveOptimization();
  }
  
  // ✅ Intelligent request execution with optimization
  executeRequest<T>(
    requestId: string, 
    config: RequestConfig
  ): Observable<RequestResult<T>> {
    
    return new Observable<RequestResult<T>>(subscriber => {
      // Check cache first
      if (config.cacheable) {
        const cached = this.getCachedResult<T>(requestId);
        if (cached) {
          subscriber.next(cached);
          subscriber.complete();
          return;
        }
      }
      
      // Check for duplicate requests
      if (this.pendingRequests.has(requestId)) {
        const pending = this.pendingRequests.get(requestId)!;
        const subscription = pending.subscribe({
          next: data => subscriber.next({
            data,
            source: 'network',
            latency: 0, // Will be updated
            timestamp: Date.now()
          }),
          error: error => subscriber.error(error),
          complete: () => subscriber.complete()
        });
        
        return () => subscription.unsubscribe();
      }
      
      // Execute new request with optimization
      this.executeOptimizedRequest<T>(requestId, config)
        .subscribe({
          next: result => subscriber.next(result),
          error: error => subscriber.error(error),
          complete: () => subscriber.complete()
        });
    });
  }
  
  // ✅ Request execution with adaptive optimizations
  private executeOptimizedRequest<T>(
    requestId: string, 
    config: RequestConfig
  ): Observable<RequestResult<T>> {
    
    const startTime = Date.now();
    const optimizedConfig = this.optimizeRequestConfig(requestId, config);
    
    // Create request observable
    const request$ = this.createHttpRequest<T>(optimizedConfig).pipe(
      timeout(optimizedConfig.timeout),
      
      // Adaptive retry logic
      this.adaptiveConfig.intelligentRetry && optimizedConfig.retryable 
        ? retryWhen(errors => this.createIntelligentRetryStrategy(requestId, errors))
        : tap(() => {}),
      
      // Result mapping
      map(data => ({
        data,
        source: 'network' as const,
        latency: Date.now() - startTime,
        timestamp: Date.now()
      })),
      
      // Cache successful results
      tap(result => {
        if (optimizedConfig.cacheable) {
          this.cacheResult(requestId, result, this.adaptiveConfig.cacheTTL);
        }
        this.updateRequestMetrics(requestId, result.latency, true);
      }),
      
      // Error handling with fallback
      catchError(error => {
        this.updateRequestMetrics(requestId, Date.now() - startTime, false);
        return this.handleRequestError<T>(requestId, error);
      }),
      
      // Cleanup
      finalize(() => {
        this.pendingRequests.delete(requestId);
      }),
      
      share()
    );
    
    // Track pending request
    this.pendingRequests.set(requestId, request$);
    
    return request$;
  }
  
  // ✅ Adaptive configuration optimization
  private optimizeRequestConfig(requestId: string, config: RequestConfig): RequestConfig {
    const metrics = this.requestMetrics.get(requestId);
    
    if (!metrics || !this.adaptiveConfig.adaptiveTimeouts) {
      return config;
    }
    
    // Adjust timeout based on historical performance
    const adaptiveTimeout = this.calculateAdaptiveTimeout(metrics);
    
    return {
      ...config,
      timeout: Math.max(adaptiveTimeout, config.timeout * 0.5) // Don't go below 50% of original
    };
  }
  
  private calculateAdaptiveTimeout(metrics: RequestMetrics): number {
    const { averageLatency, successRate } = metrics;
    
    // Base timeout on average latency with buffer
    let adaptiveTimeout = averageLatency * 2.5;
    
    // Adjust based on success rate
    if (successRate < 0.9) {
      adaptiveTimeout *= 1.5; // More time for unreliable endpoints
    }
    
    return Math.min(adaptiveTimeout, 30000); // Cap at 30 seconds
  }
  
  // ✅ Intelligent retry strategy
  private createIntelligentRetryStrategy(
    requestId: string, 
    errors: Observable<any>
  ): Observable<any> {
    
    return errors.pipe(
      scan((retryCount, error) => {
        if (retryCount >= this.getMaxRetries(requestId, error)) {
          throw error;
        }
        return retryCount + 1;
      }, 0),
      
      delayWhen((retryCount, error) => {
        const delay = this.calculateIntelligentDelay(requestId, retryCount, error);
        return timer(delay);
      })
    );
  }
  
  private getMaxRetries(requestId: string, error: any): number {
    // Network errors get more retries
    if (error.name === 'TimeoutError' || error.status >= 500) {
      return 3;
    }
    
    // Client errors (4xx) usually don't need retries
    if (error.status >= 400 && error.status < 500) {
      return 0;
    }
    
    return 2; // Default
  }
  
  private calculateIntelligentDelay(
    requestId: string, 
    retryCount: number, 
    error: any
  ): number {
    
    const baseDelay = 1000;
    
    // Exponential backoff with jitter
    let delay = baseDelay * Math.pow(2, retryCount - 1);
    delay = delay * (0.5 + Math.random() * 0.5); // Add jitter
    
    // Adjust based on error type
    if (error.status === 429) { // Rate limiting
      delay *= 3;
    } else if (error.status >= 500) { // Server errors
      delay *= 1.5;
    }
    
    return Math.min(delay, 30000); // Cap at 30 seconds
  }
  
  // ✅ Advanced caching with intelligent invalidation
  private getCachedResult<T>(requestId: string): RequestResult<T> | null {
    const cached = this.cache.get(requestId);
    
    if (!cached) return null;
    
    const age = Date.now() - cached.timestamp;
    
    // Check TTL
    if (age > cached.ttl) {
      this.cache.delete(requestId);
      return null;
    }
    
    // Return cached result
    return {
      data: cached.data,
      source: 'cache',
      latency: 0,
      timestamp: cached.timestamp
    };
  }
  
  private cacheResult(requestId: string, result: RequestResult<any>, ttl: number): void {
    this.cache.set(requestId, {
      data: result.data,
      timestamp: result.timestamp,
      ttl
    });
  }
  
  // ✅ Error handling with intelligent fallbacks
  private handleRequestError<T>(requestId: string, error: any): Observable<RequestResult<T>> {
    // Try to return stale cached data as fallback
    const cached = this.cache.get(requestId);
    
    if (cached) {
      console.warn(`Request ${requestId} failed, returning stale cache data:`, error.message);
      
      return of({
        data: cached.data,
        source: 'fallback',
        latency: 0,
        timestamp: cached.timestamp
      });
    }
    
    // No fallback available
    return throwError(() => error);
  }
  
  // ✅ Request dependency management
  executeRequestWithDependencies<T>(
    requestId: string,
    config: RequestConfig
  ): Observable<RequestResult<T>> {
    
    if (!config.dependencies || config.dependencies.length === 0) {
      return this.executeRequest<T>(requestId, config);
    }
    
    // Execute dependencies first
    const dependencyRequests = config.dependencies.map(depId => {
      const depConfig = this.requestQueue.get(depId);
      return depConfig ? this.executeRequest(depId, depConfig) : EMPTY;
    });
    
    return combineLatest(dependencyRequests).pipe(
      switchMap(() => this.executeRequest<T>(requestId, config))
    );
  }
  
  // ✅ Batch request optimization
  executeBatchRequests<T>(requests: Array<{ id: string; config: RequestConfig }>): Observable<Map<string, RequestResult<T>>> {
    
    // Group requests by priority and domain
    const groups = this.groupRequestsForBatching(requests);
    
    // Execute groups with controlled concurrency
    return from(groups).pipe(
      mergeMap(group => this.executeBatchGroup<T>(group), 2), // Max 2 concurrent groups
      reduce((acc, groupResults) => {
        groupResults.forEach((result, id) => acc.set(id, result));
        return acc;
      }, new Map<string, RequestResult<T>>())
    );
  }
  
  private groupRequestsForBatching(
    requests: Array<{ id: string; config: RequestConfig }>
  ): Array<Array<{ id: string; config: RequestConfig }>> {
    
    // Sort by priority
    const sortedRequests = requests.sort((a, b) => b.config.priority - a.config.priority);
    
    // Group into batches of optimal size
    const batchSize = Math.min(this.adaptiveConfig.maxConcurrentRequests, 5);
    const groups: Array<Array<{ id: string; config: RequestConfig }>> = [];
    
    for (let i = 0; i < sortedRequests.length; i += batchSize) {
      groups.push(sortedRequests.slice(i, i + batchSize));
    }
    
    return groups;
  }
  
  private executeBatchGroup<T>(
    group: Array<{ id: string; config: RequestConfig }>
  ): Observable<Map<string, RequestResult<T>>> {
    
    const groupRequests = group.map(({ id, config }) => 
      this.executeRequest<T>(id, config).pipe(
        map(result => ({ id, result })),
        catchError(error => of({ id, error }))
      )
    );
    
    return combineLatest(groupRequests).pipe(
      map(results => {
        const resultMap = new Map<string, RequestResult<T>>();
        
        results.forEach(({ id, result, error }) => {
          if (result) {
            resultMap.set(id, result);
          } else {
            console.error(`Batch request ${id} failed:`, error);
          }
        });
        
        return resultMap;
      })
    );
  }
  
  // ✅ Adaptive optimization based on performance metrics
  private startAdaptiveOptimization(): void {
    setInterval(() => {
      this.optimizeConfiguration();
    }, 60000); // Optimize every minute
  }
  
  private optimizeConfiguration(): void {
    // Analyze request patterns and adjust configuration
    const allMetrics = Array.from(this.requestMetrics.values());
    
    if (allMetrics.length === 0) return;
    
    const averageSuccessRate = allMetrics.reduce((sum, m) => sum + m.successRate, 0) / allMetrics.length;
    const averageLatency = allMetrics.reduce((sum, m) => sum + m.averageLatency, 0) / allMetrics.length;
    
    // Adjust concurrent request limit based on performance
    if (averageSuccessRate > 0.95 && averageLatency < 2000) {
      this.adaptiveConfig.maxConcurrentRequests = Math.min(
        this.adaptiveConfig.maxConcurrentRequests + 1, 
        10
      );
    } else if (averageSuccessRate < 0.8 || averageLatency > 5000) {
      this.adaptiveConfig.maxConcurrentRequests = Math.max(
        this.adaptiveConfig.maxConcurrentRequests - 1, 
        2
      );
    }
    
    console.log(`Adaptive optimization: concurrent requests = ${this.adaptiveConfig.maxConcurrentRequests}`);
  }
  
  // ✅ Metrics and monitoring
  private updateRequestMetrics(requestId: string, latency: number, success: boolean): void {
    if (!this.requestMetrics.has(requestId)) {
      this.requestMetrics.set(requestId, {
        totalRequests: 0,
        successfulRequests: 0,
        averageLatency: 0,
        successRate: 0
      });
    }
    
    const metrics = this.requestMetrics.get(requestId)!;
    metrics.totalRequests++;
    
    if (success) {
      metrics.successfulRequests++;
    }
    
    metrics.averageLatency = (metrics.averageLatency * (metrics.totalRequests - 1) + latency) / metrics.totalRequests;
    metrics.successRate = metrics.successfulRequests / metrics.totalRequests;
  }
  
  getRequestMetrics(): Map<string, RequestMetrics> {
    return new Map(this.requestMetrics);
  }
  
  // ✅ Helper methods
  private createHttpRequest<T>(config: RequestConfig): Observable<T> {
    // Simulate HTTP request
    return new Observable<T>(subscriber => {
      const timeout = setTimeout(() => {
        if (Math.random() > 0.9) { // 10% failure rate
          subscriber.error(new Error('Network error'));
        } else {
          subscriber.next({ data: 'Success' } as T);
          subscriber.complete();
        }
      }, Math.random() * config.timeout * 0.5);
      
      return () => clearTimeout(timeout);
    });
  }
}

interface RequestMetrics {
  totalRequests: number;
  successfulRequests: number;
  averageLatency: number;
  successRate: number;
}

// INTERVIEW DISCUSSION POINTS:
// - How would you implement request prioritization?
// - What metrics would be most valuable for optimization?
// - How would you handle rate limiting across multiple APIs?
// - What additional caching strategies would improve performance?
```

### **🏅 Tier 3 Company Challenges (Startups, Mid-size Companies)**

#### **🎯 CHALLENGE 1: "Event-Driven Notification System"**
```typescript
// INTERVIEWER: "Build a notification system that handles multiple channels and user preferences"
// TIME LIMIT: 30 minutes
// EVALUATION: Event handling, user preferences, delivery optimization

interface NotificationChannel {
  type: 'email' | 'sms' | 'push' | 'in-app';
  enabled: boolean;
  config: any;
}

interface UserPreferences {
  userId: string;
  channels: NotificationChannel[];
  quietHours: { start: string; end: string };
  frequency: 'immediate' | 'batched' | 'daily' | 'weekly';
}

interface NotificationEvent {
  id: string;
  type: string;
  userId: string;
  priority: 'low' | 'medium' | 'high' | 'urgent';
  data: any;
  timestamp: number;
  expiresAt?: number;
}

class EventDrivenNotificationSystem {
  
  private userPreferences: Map<string, UserPreferences> = new Map();
  private pendingNotifications: Map<string, NotificationEvent[]> = new Map();
  private channelProviders: Map<string, any> = new Map();
  
  private eventSubject = new Subject<NotificationEvent>();
  private deliverySubject = new Subject<{ userId: string; channel: string; success: boolean }>();
  
  constructor() {
    this.setupEventProcessing();
    this.startBatchProcessing();
  }
  
  // ✅ Event ingestion with intelligent routing
  emitNotification(event: NotificationEvent): Observable<boolean> {
    return new Observable<boolean>(subscriber => {
      try {
        // Validate event
        if (!this.validateEvent(event)) {
          subscriber.next(false);
          subscriber.complete();
          return;
        }
        
        // Emit to processing pipeline
        this.eventSubject.next(event);
        
        subscriber.next(true);
        subscriber.complete();
        
      } catch (error) {
        subscriber.error(error);
      }
    });
  }
  
  // ✅ Event processing pipeline
  private setupEventProcessing(): void {
    this.eventSubject.pipe(
      // Group by user for batching
      groupBy(event => event.userId),
      
      // Process each user's events
      mergeMap(userEvents$ => 
        userEvents$.pipe(
          // Buffer based on user preferences
          this.createUserSpecificBuffering(),
          
          // Process notification batches
          mergeMap(events => this.processNotificationBatch(events))
        )
      )
    ).subscribe({
      next: result => console.log('Notification processed:', result),
      error: error => console.error('Processing error:', error)
    });
  }
  
  // ✅ User-specific buffering strategy
  private createUserSpecificBuffering() {
    return (source: Observable<NotificationEvent>) => 
      source.pipe(
        bufferTime(5000), // 5 second buffer window
        filter(events => events.length > 0),
        map(events => this.optimizeBatchForUser(events))
      );
  }
  
  private optimizeBatchForUser(events: NotificationEvent[]): NotificationEvent[] {
    if (events.length === 0) return events;
    
    const userId = events[0].userId;
    const preferences = this.userPreferences.get(userId);
    
    if (!preferences) return events;
    
    // Filter based on user preferences
    switch (preferences.frequency) {
      case 'immediate':
        // Send urgent and high priority immediately, batch others
        return events.filter(e => e.priority === 'urgent' || e.priority === 'high');
      
      case 'batched':
        // Send all events in this batch
        return events;
      
      case 'daily':
      case 'weekly':
        // Store for later batch processing
        this.storePendingNotifications(userId, events);
        return [];
      
      default:
        return events;
    }
  }
  
  // ✅ Multi-channel delivery with fallback
  private async processNotificationBatch(events: NotificationEvent[]): Promise<any> {
    if (events.length === 0) return;
    
    const userId = events[0].userId;
    const preferences = this.userPreferences.get(userId);
    
    if (!preferences) {
      console.warn(`No preferences found for user ${userId}`);
      return;
    }
    
    // Check quiet hours
    if (this.isQuietHours(preferences)) {
      // Store for later delivery unless urgent
      const urgentEvents = events.filter(e => e.priority === 'urgent');
      if (urgentEvents.length === 0) {
        this.storePendingNotifications(userId, events);
        return;
      }
      events = urgentEvents;
    }
    
    // Deliver through enabled channels
    const enabledChannels = preferences.channels.filter(c => c.enabled);
    
    for (const channel of enabledChannels) {
      try {
        await this.deliverToChannel(userId, events, channel);
        this.deliverySubject.next({ userId, channel: channel.type, success: true });
        
        // If successful delivery on primary channel, might skip others based on preference
        if (this.shouldSkipRemainingChannels(channel, events)) {
          break;
        }
        
      } catch (error) {
        console.error(`Failed to deliver to ${channel.type} for user ${userId}:`, error);
        this.deliverySubject.next({ userId, channel: channel.type, success: false });
      }
    }
  }
  
  // ✅ Channel-specific delivery
  private async deliverToChannel(
    userId: string, 
    events: NotificationEvent[], 
    channel: NotificationChannel
  ): Promise<void> {
    
    const provider = this.channelProviders.get(channel.type);
    
    if (!provider) {
      throw new Error(`No provider found for channel ${channel.type}`);
    }
    
    // Format notifications for channel
    const formattedNotifications = events.map(event => 
      this.formatNotificationForChannel(event, channel)
    );
    
    // Deliver based on channel type
    switch (channel.type) {
      case 'email':
        await this.deliverEmail(userId, formattedNotifications, channel.config);
        break;
      
      case 'sms':
        await this.deliverSMS(userId, formattedNotifications, channel.config);
        break;
      
      case 'push':
        await this.deliverPush(userId, formattedNotifications, channel.config);
        break;
      
      case 'in-app':
        await this.deliverInApp(userId, formattedNotifications, channel.config);
        break;
      
      default:
        throw new Error(`Unsupported channel type: ${channel.type}`);
    }
  }
  
  // ✅ Batch processing for scheduled notifications
  private startBatchProcessing(): void {
    // Daily batch processing
    this.scheduleBatchDelivery('daily', '09:00');
    
    // Weekly batch processing
    this.scheduleBatchDelivery('weekly', 'Monday 09:00');
  }
  
  private scheduleBatchDelivery(frequency: string, time: string): void {
    // Simplified scheduling - in real implementation would use cron-like scheduling
    const interval = frequency === 'daily' ? 24 * 60 * 60 * 1000 : 7 * 24 * 60 * 60 * 1000;
    
    setInterval(() => {
      this.processPendingNotifications(frequency);
    }, interval);
  }
  
  private async processPendingNotifications(frequency: string): Promise<void> {
    for (const [userId, notifications] of this.pendingNotifications) {
      const preferences = this.userPreferences.get(userId);
      
      if (preferences && preferences.frequency === frequency) {
        await this.processNotificationBatch(notifications);
        this.pendingNotifications.delete(userId);
      }
    }
  }
  
  // ✅ User preference management
  updateUserPreferences(preferences: UserPreferences): Observable<boolean> {
    return new Observable<boolean>(subscriber => {
      try {
        this.userPreferences.set(preferences.userId, preferences);
        subscriber.next(true);
        subscriber.complete();
      } catch (error) {
        subscriber.error(error);
      }
    });
  }
  
  getUserPreferences(userId: string): Observable<UserPreferences | null> {
    return of(this.userPreferences.get(userId) || null);
  }
  
  // ✅ Analytics and monitoring
  getDeliveryMetrics(): Observable<any> {
    return this.deliverySubject.pipe(
      bufferTime(60000), // 1 minute windows
      map(deliveries => {
        const total = deliveries.length;
        const successful = deliveries.filter(d => d.success).length;
        const byChannel = deliveries.reduce((acc, d) => {
          acc[d.channel] = (acc[d.channel] || 0) + 1;
          return acc;
        }, {} as any);
        
        return {
          total,
          successful,
          successRate: total > 0 ? successful / total : 0,
          byChannel
        };
      })
    );
  }
  
  // ✅ Helper methods
  private validateEvent(event: NotificationEvent): boolean {
    return !!(event.id && event.type && event.userId && event.data);
  }
  
  private isQuietHours(preferences: UserPreferences): boolean {
    if (!preferences.quietHours) return false;
    
    const now = new Date();
    const currentTime = now.getHours() * 100 + now.getMinutes();
    
    const startTime = this.parseTime(preferences.quietHours.start);
    const endTime = this.parseTime(preferences.quietHours.end);
    
    if (startTime < endTime) {
      return currentTime >= startTime && currentTime <= endTime;
    } else {
      // Quiet hours span midnight
      return currentTime >= startTime || currentTime <= endTime;
    }
  }
  
  private parseTime(timeString: string): number {
    const [hours, minutes] = timeString.split(':').map(Number);
    return hours * 100 + minutes;
  }
  
  private storePendingNotifications(userId: string, events: NotificationEvent[]): void {
    if (!this.pendingNotifications.has(userId)) {
      this.pendingNotifications.set(userId, []);
    }
    
    this.pendingNotifications.get(userId)!.push(...events);
  }
  
  private shouldSkipRemainingChannels(channel: NotificationChannel, events: NotificationEvent[]): boolean {
    // Skip other channels for low priority notifications if email/push was successful
    const hasLowPriority = events.some(e => e.priority === 'low');
    return hasLowPriority && (channel.type === 'email' || channel.type === 'push');
  }
  
  private formatNotificationForChannel(event: NotificationEvent, channel: NotificationChannel): any {
    // Format notification based on channel requirements
    return {
      id: event.id,
      message: this.generateMessage(event, channel.type),
      data: event.data,
      priority: event.priority
    };
  }
  
  private generateMessage(event: NotificationEvent, channelType: string): string {
    // Generate channel-appropriate message
    const baseMessage = event.data.message || `New ${event.type} notification`;
    
    switch (channelType) {
      case 'sms':
        return baseMessage.substring(0, 160); // SMS character limit
      case 'push':
        return baseMessage.substring(0, 100); // Push notification limit
      default:
        return baseMessage;
    }
  }
  
  // ✅ Channel delivery implementations
  private async deliverEmail(userId: string, notifications: any[], config: any): Promise<void> {
    // Simulate email delivery
    await this.delay(200);
    console.log(`Email sent to user ${userId}: ${notifications.length} notifications`);
  }
  
  private async deliverSMS(userId: string, notifications: any[], config: any): Promise<void> {
    // Simulate SMS delivery
    await this.delay(500);
    console.log(`SMS sent to user ${userId}: ${notifications.length} notifications`);
  }
  
  private async deliverPush(userId: string, notifications: any[], config: any): Promise<void> {
    // Simulate push notification delivery
    await this.delay(100);
    console.log(`Push notification sent to user ${userId}: ${notifications.length} notifications`);
  }
  
  private async deliverInApp(userId: string, notifications: any[], config: any): Promise<void> {
    // Simulate in-app notification delivery
    await this.delay(50);
    console.log(`In-app notification sent to user ${userId}: ${notifications.length} notifications`);
  }
  
  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}

// INTERVIEW DISCUSSION POINTS:
// - How would you handle notification templates and localization?
// - What optimizations would you add for high-volume scenarios?
// - How would you implement notification history and analytics?
// - What additional channels would you consider for the future?
```

---

## 📝 **PRACTICAL ASYNC EXERCISES**

### **🎯 Exercise 1: Build Your Own Circuit Breaker**
```typescript
// Task: Implement a production-ready circuit breaker with metrics
// Expected completion time: 45 minutes

interface CircuitBreakerConfig {
  failureThreshold: number;
  resetTimeout: number;
  monitoringWindow: number;
  fallbackResponse?: any;
}

class CircuitBreaker {
  // Your implementation here
  
  async execute<T>(operation: () => Promise<T>): Promise<T> {
    // Implement circuit breaker logic
    throw new Error('Implementation required');
  }
}

// Test cases provided
describe('CircuitBreaker', () => {
  it('should open circuit after consecutive failures', async () => {
    // Test implementation
  });
  
  it('should provide fallback when circuit is open', async () => {
    // Test implementation
  });
  
  it('should transition to half-open state after timeout', async () => {
    // Test implementation
  });
});
```

### **🎯 Exercise 2: Async Data Pipeline**
```typescript
// Task: Create a data processing pipeline with backpressure handling
// Expected completion time: 60 minutes

interface PipelineStage<T, R> {
  name: string;
  process: (input: T) => Promise<R>;
  maxConcurrency?: number;
  retryPolicy?: RetryPolicy;
}

class AsyncDataPipeline<T> {
  private stages: PipelineStage<any, any>[] = [];
  
  addStage<R>(stage: PipelineStage<T, R>): AsyncDataPipeline<R> {
    // Your implementation here
    return this as any;
  }
  
  process(input: Observable<T>): Observable<any> {
    // Implement pipeline processing with backpressure
    throw new Error('Implementation required');
  }
}

// Usage example
const pipeline = new AsyncDataPipeline<string>()
  .addStage({
    name: 'validate',
    process: async (data) => validateData(data),
    maxConcurrency: 5
  })
  .addStage({
    name: 'transform',
    process: async (data) => transformData(data),
    maxConcurrency: 3
  })
  .addStage({
    name: 'persist',
    process: async (data) => persistData(data),
    maxConcurrency: 2
  });

// Test with backpressure
const input$ = from(generateLargeDataset());
pipeline.process(input$).subscribe(result => console.log(result));
```

### **🎯 Exercise 3: Multi-Source Cache with Sync**
```typescript
// Task: Build a cache that synchronizes with multiple data sources
// Expected completion time: 50 minutes

interface CacheEntry<T> {
  value: T;
  timestamp: number;
  source: string;
  ttl: number;
}

interface SyncStrategy {
  type: 'pull' | 'push' | 'hybrid';
  interval?: number;
  conflictResolution: 'timestamp' | 'source-priority' | 'merge';
}

class MultiSourceCache<T> {
  private cache = new Map<string, CacheEntry<T>>();
  private sources = new Map<string, DataSource<T>>();
  
  addSource(source: DataSource<T>, strategy: SyncStrategy): void {
    // Implement source registration and sync setup
    throw new Error('Implementation required');
  }
  
  get(key: string, maxAge?: number): Observable<T | null> {
    // Implement intelligent cache retrieval with auto-refresh
    throw new Error('Implementation required');
  }
  
  set(key: string, value: T, source: string): Observable<boolean> {
    // Implement cache update with conflict resolution
    throw new Error('Implementation required');
  }
  
  private resolveConflict(existing: CacheEntry<T>, incoming: CacheEntry<T>): CacheEntry<T> {
    // Implement conflict resolution logic
    throw new Error('Implementation required');
  }
}

// Test scenarios
const cache = new MultiSourceCache<UserData>();

cache.addSource(databaseSource, { 
  type: 'pull', 
  interval: 30000, 
  conflictResolution: 'timestamp' 
});

cache.addSource(apiSource, { 
  type: 'hybrid', 
  interval: 10000, 
  conflictResolution: 'source-priority' 
});
```

### **🎯 Exercise 4: Reactive Form Validator**
```typescript
// Task: Create an async form validator with debouncing and caching
// Expected completion time: 40 minutes

interface ValidationRule<T> {
  name: string;
  validator: (value: T) => Observable<ValidationResult>;
  debounceTime?: number;
  dependencies?: string[];
}

interface ValidationResult {
  valid: boolean;
  errors?: string[];
  warnings?: string[];
}

class ReactiveFormValidator {
  private validationCache = new Map<string, ValidationResult>();
  private validationRules = new Map<string, ValidationRule<any>>();
  
  addRule<T>(fieldName: string, rule: ValidationRule<T>): void {
    // Implement rule registration
    throw new Error('Implementation required');
  }
  
  validateField(fieldName: string, value: any): Observable<ValidationResult> {
    // Implement field validation with debouncing and caching
    throw new Error('Implementation required');
  }
  
  validateForm(formData: any): Observable<Map<string, ValidationResult>> {
    // Implement full form validation with dependency handling
    throw new Error('Implementation required');
  }
  
  private createValidationStream(fieldName: string): Observable<any> {
    // Create debounced validation stream
    throw new Error('Implementation required');
  }
}

// Usage example
const validator = new ReactiveFormValidator();

validator.addRule('email', {
  name: 'email-unique',
  validator: (email) => this.checkEmailUnique(email),
  debounceTime: 500
});

validator.addRule('password', {
  name: 'password-strength',
  validator: (password) => this.checkPasswordStrength(password),
  debounceTime: 300
});

validator.addRule('confirmPassword', {
  name: 'password-match',
  validator: (confirmPassword) => this.checkPasswordMatch(confirmPassword),
  dependencies: ['password'],
  debounceTime: 200
});
```

---

## 🎓 **CHAPTER SUMMARY**

### **🔑 Key Concepts Mastered**

1. **Promise vs Observable Decision Framework**
   - Single vs multiple values
   - Cancellation requirements
   - Composition needs
   - Angular integration patterns

2. **Advanced Promise Patterns**
   - Concurrency control strategies
   - Retry mechanisms with exponential backoff
   - Timeout and cancellation handling
   - Promise composition patterns

3. **Observable Creation Patterns**
   - Custom observables for complex scenarios
   - WebSocket integration with reconnection
   - Custom operators for business logic
   - Memory management and cleanup

4. **Angular-Specific Async Patterns**
   - Component lifecycle integration
   - Reactive form validation
   - Real-time data synchronization
   - Error boundary implementation

5. **Enterprise Async Architecture**
   - Circuit breaker patterns
   - Event-driven systems
   - Backpressure handling
   - Performance optimization strategies

### **🚀 Interview Readiness Checklist**

- [ ] **Decision Making**: Can explain when to use Promises vs Observables
- [ ] **Error Handling**: Understands comprehensive error recovery strategies
- [ ] **Performance**: Can implement backpressure and optimization patterns
- [ ] **Angular Integration**: Knows async patterns for components and forms
- [ ] **Enterprise Patterns**: Can design resilient async architectures
- [ ] **RxJS Mastery**: Comfortable with custom operators and composition
- [ ] **Real-world Applications**: Can solve company-tier async challenges

### **🔄 What's Next?**

You've now mastered async programming patterns essential for Angular development. The next chapter will build on these foundations to explore advanced TypeScript patterns and their application in Angular applications.

**Next Chapter: 03-04 TypeScript Advanced Patterns**
- Generic programming patterns
- Decorator patterns and metadata
- Type-safe API design
- Performance optimization techniques

---

*"Mastering async programming is like conducting an orchestra - every async operation must be perfectly timed and orchestrated to create beautiful, responsive applications."* 🎼

