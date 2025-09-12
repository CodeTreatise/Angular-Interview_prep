# Forms and Validation

## Overview

Angular forms are a critical component for collecting and validating user input. With **85% interview frequency**, forms and validation represent one of the most tested topics in Angular interviews. This chapter covers both template-driven and reactive forms, modern validation strategies, and advanced patterns essential for professional Angular development.

### Why Forms Matter in Angular Interviews

**Business Impact:**
- Forms are the primary way users interact with applications
- Poor form UX can lead to 67% abandonment rates in e-commerce
- Form validation prevents 80% of data corruption issues
- Proper form architecture scales with application complexity

**Technical Complexity:**
- Forms bridge user interaction with business logic
- They require understanding of reactive programming (RxJS)
- Form state management affects entire application architecture
- Validation strategies impact performance and user experience

**Interview Focus Areas:**
- **Architecture decisions**: When to choose reactive vs template-driven
- **Performance optimization**: How to handle large forms efficiently
- **User experience**: Creating intuitive validation feedback
- **Error handling**: Managing complex validation scenarios

## Table of Contents

1. [Forms Fundamentals](#forms-fundamentals)
2. [Template-Driven Forms](#template-driven-forms)
3. [Reactive Forms](#reactive-forms)
4. [Form Validation](#form-validation)
5. [Custom Validators](#custom-validators)
6. [Advanced Form Patterns](#advanced-form-patterns)
7. [Performance Optimization](#performance-optimization)
8. [Interview Scenarios](#interview-scenarios)

---

## Forms Fundamentals

### Understanding the Two Paradigms

**Why Angular Offers Two Form Approaches:**
Angular provides two distinct form approaches because different use cases require different architectural patterns:

1. **Template-Driven**: Follows Angular's declarative template philosophy
2. **Reactive**: Embraces functional reactive programming principles

**The Decision Framework:**

```typescript
// Decision Tree for Form Selection
const chooseFormApproach = (requirements: FormRequirements) => {
  if (requirements.complexity === 'simple' && 
      requirements.testing === 'minimal' &&
      requirements.dynamicFields === false) {
    return 'template-driven';
  }
  
  if (requirements.complexity === 'complex' ||
      requirements.testing === 'extensive' ||
      requirements.dynamicFields === true ||
      requirements.crossFieldValidation === true) {
    return 'reactive';
  }
};
```

### When Template-Driven Forms Excel

**Use Cases:**
- **Contact forms**: Simple, static field structure
- **Login forms**: Minimal validation, straightforward flow
- **Feedback forms**: Basic text inputs with simple validation
- **Prototype development**: Quick form setup for testing concepts

**Why Template-Driven Works Here:**
```typescript
// The simplicity advantage
@Component({
  template: `
    <!-- Zero boilerplate - just bind and validate -->
    <form #contactForm="ngForm" (ngSubmit)="onSubmit(contactForm)">
      <input name="email" ngModel required email>
      <button [disabled]="contactForm.invalid">Submit</button>
    </form>
  `
})
export class ContactFormComponent {
  // Minimal component code required
  onSubmit(form: NgForm) {
    if (form.valid) {
      console.log('Form data:', form.value);
    }
  }
}
```

### When Reactive Forms Dominate

**Use Cases:**
- **Registration forms**: Complex validation, multiple steps
- **E-commerce checkout**: Dynamic fields based on shipping/billing
- **Data entry applications**: Complex business rules
- **Form builders**: Meta-forms that create other forms

**Why Reactive Forms Win:**
```typescript
// The control and testability advantage
@Component({
  selector: 'app-registration'
})
export class RegistrationComponent {
  // Explicit form structure - easy to test and reason about
  registrationForm = this.fb.group({
    personal: this.fb.group({
      firstName: ['', [Validators.required, Validators.minLength(2)]],
      lastName: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.email]]
    }),
    preferences: this.fb.group({
      newsletter: [false],
      notifications: [true]
    })
  });

  constructor(private fb: FormBuilder) {}

  // Easy to unit test
  validateEmail(): boolean {
    return this.registrationForm.get('personal.email')?.valid ?? false;
  }
}
```

### Forms Fundamentals Comparison

| Aspect | Template-Driven | Reactive | Why This Matters |
|--------|----------------|----------|------------------|
| **Setup** | Minimal | More setup required | Template-driven faster for simple cases |
| **Data Model** | Implicit | Explicit | Reactive better for complex state management |
| **Testing** | Harder | Easier | Reactive forms are more testable |
| **Validation** | Template-based | Code-based | Code-based validation is more flexible |
| **Performance** | Good for simple forms | Better for complex forms | Reactive forms optimize change detection |
| **Scalability** | Limited | Highly scalable | Reactive forms handle complexity better |

### How Forms Work Under the Hood

**Template-Driven Form Lifecycle:**
1. Angular creates implicit FormControl instances
2. NgModel directive binds template to form controls
3. Form validation happens in template expressions
4. Change detection triggers on every input event

**Reactive Form Lifecycle:**
1. Developer explicitly creates FormControl instances
2. FormControlName directive binds template to controls
3. Form validation happens in component logic
4. Change detection optimized through OnPush strategy

---

## Template-Driven Forms

### Why Template-Driven Forms Exist

**Philosophy Behind Template-Driven:**
Template-driven forms follow Angular's declarative approach where the template drives the form logic. This approach mirrors how developers think about HTML forms naturally.

**When Template-Driven Forms Shine:**
- **Rapid development**: Zero boilerplate for simple forms
- **HTML-first mindset**: Developers familiar with HTML forms
- **Two-way binding**: Natural fit with ngModel directive
- **Simple validation**: Built-in validators work seamlessly

### How Template-Driven Forms Work

**The Magic Behind NgModel:**
```typescript
// What happens when you write [(ngModel)]="user.name"
// 1. Angular creates a FormControl behind the scenes
// 2. Sets up two-way data binding
// 3. Registers the control with the parent NgForm
// 4. Applies validation attributes as validators

// This template code:
<input [(ngModel)]="user.name" required minlength="2">

// Is equivalent to this reactive form setup:
new FormControl(this.user.name, [Validators.required, Validators.minLength(2)])
```

### Template-Driven Implementation Strategy

**Step-by-Step Approach:**
1. **Import FormsModule**: Enable template-driven features
2. **Create form template**: Use ngForm and ngModel
3. **Add validation**: Use HTML5 validation attributes
4. **Handle submission**: Access form state through template reference
5. **Display errors**: Use form state properties for user feedback
```

### Complete Template-Driven Example with Explanations

```typescript
// app.module.ts - First, enable template-driven forms
import { FormsModule } from '@angular/forms';

@NgModule({
  imports: [FormsModule], // This enables ngModel and ngForm directives
  // ...
})
export class AppModule { }
```

```typescript
// user-form.component.ts
import { Component } from '@angular/core';

interface User {
  name: string;
  email: string;
  age: number;
}

@Component({
  selector: 'app-user-form',
  template: `
    <!-- 
      #userForm="ngForm" creates a template reference to NgForm directive
      NgForm automatically tracks all ngModel controls within the form
    -->
    <form #userForm="ngForm" (ngSubmit)="onSubmit(userForm)">
      <div>
        <label for="name">Name:</label>
        <!-- 
          [(ngModel)]="user.name" - Two-way binding
          #name="ngModel" - Template reference to this specific control
          required, minlength - HTML5 validation attributes converted to Angular validators
        -->
        <input 
          type="text" 
          id="name" 
          name="name" 
          [(ngModel)]="user.name"
          #name="ngModel"
          required
          minlength="2"
          [class.error]="name.invalid && name.touched">
        
        <!-- 
          Error handling strategy:
          - Show errors only when field is invalid AND touched
          - Use specific error properties for targeted messages
        -->
        <div *ngIf="name.invalid && name.touched" class="error-message">
          <div *ngIf="name.errors?.['required']">Name is required</div>
          <div *ngIf="name.errors?.['minlength']">Name must be at least 2 characters</div>
        </div>
      </div>

      <div>
        <label for="email">Email:</label>
        <input 
          type="email" 
          id="email" 
          name="email" 
          [(ngModel)]="user.email"
          #email="ngModel"
          required
          email
          [class.error]="email.invalid && email.touched">
        <div *ngIf="email.invalid && email.touched" class="error-message">
          <div *ngIf="email.errors?.['required']">Email is required</div>
          <div *ngIf="email.errors?.['email']">Please enter a valid email</div>
        </div>
      </div>

      <!-- 
        Form submission:
        - Button disabled when form is invalid
        - Pass entire form reference to component method
      -->
      <button type="submit" [disabled]="userForm.invalid">Submit</button>
    </form>

    <!-- Debug panel - useful during development -->
    <div class="debug-panel" *ngIf="showDebug">
      <h4>Form Debug Info:</h4>
      <p>Form Valid: {{ userForm.valid }}</p>
      <p>Form Value: {{ userForm.value | json }}</p>
      <p>Form Errors: {{ userForm.errors | json }}</p>
    </div>
  `
})
export class UserFormComponent {
  // Data model - simple POJO
  user: User = {
    name: '',
    email: '',
    age: 0
  };

  showDebug = false; // Toggle for development

  /**
   * Form submission handler
   * @param form - NgForm instance with all form state
   */
  onSubmit(form: NgForm) {
    if (form.valid) {
      console.log('Form submitted successfully:', this.user);
      // Here you would typically call a service to save the data
      this.saveUser(this.user);
    } else {
      console.log('Form is invalid, marking all fields as touched');
      // Mark all fields as touched to show validation errors
      this.markAllFieldsAsTouched(form);
    }
  }

  /**
   * Helper method to mark all form fields as touched
   * This ensures validation errors are visible to the user
   */
  private markAllFieldsAsTouched(form: NgForm) {
    Object.keys(form.controls).forEach(key => {
      form.controls[key].markAsTouched();
    });
  }

  private saveUser(user: User) {
    // Service call would go here
    console.log('Saving user:', user);
  }
}
```

### Understanding Template-Driven Validation States

**Why These States Matter:**
Understanding form control states is crucial for creating good user experience. Users shouldn't see error messages immediately when they start typing.

```typescript
// Template reference variables expose these properties
interface NgModelState {
  // Validation states
  valid: boolean;      // Control passes all validators
  invalid: boolean;    // Control fails at least one validator
  pending: boolean;    // Async validation in progress
  disabled: boolean;   // Control is disabled
  
  // Interaction states  
  pristine: boolean;   // User hasn't modified the value
  dirty: boolean;      // User has modified the value
  touched: boolean;    // User has blurred the control (left the field)
  untouched: boolean;  // User hasn't blurred the control
  
  // Data access
  errors: ValidationErrors | null;  // Object containing validation errors
  value: any;          // Current control value
}
```

**Best Practice Validation Display Strategy:**
```html
<!-- Show errors only when field is invalid AND touched -->
<div *ngIf="nameField.invalid && nameField.touched">
  <!-- This prevents showing errors before user interacts with field -->
</div>

<!-- Alternative: Show errors when invalid AND dirty -->
<div *ngIf="nameField.invalid && nameField.dirty">
  <!-- This shows errors as soon as user starts typing -->
</div>

<!-- For submit scenarios: Show errors when form submitted OR field touched -->
<div *ngIf="(formSubmitted || nameField.touched) && nameField.invalid">
  <!-- This covers both interaction and submission scenarios -->
</div>
```
```

---

## Reactive Forms

### Why Reactive Forms Were Created

**The Problem with Template-Driven Forms:**
As applications grow complex, template-driven forms reveal limitations:
- **Testing difficulty**: Form logic embedded in templates is hard to unit test
- **Complex validation**: Cross-field validation becomes cumbersome
- **Performance issues**: Every input triggers change detection
- **Scalability concerns**: Large forms become unmanageable

**The Reactive Solution:**
Reactive forms solve these problems by moving form logic to the component class:
- **Explicit control**: Direct access to form state and behavior
- **Immutable updates**: Form state changes create new state objects
- **Reactive programming**: Built on RxJS observables for powerful data flow
- **Type safety**: Better TypeScript support for form controls

### How Reactive Forms Work

**The Reactive Philosophy:**
```typescript
// Instead of template driving form state:
<input [(ngModel)]="user.name" required>

// Component class controls form state:
const nameControl = new FormControl('', Validators.required);
// Template just displays the state:
<input [formControl]="nameControl">
```

**Key Architectural Benefits:**
1. **Testability**: Form logic is in testable component methods
2. **Immutability**: Form state changes are predictable
3. **Reactive streams**: Form changes are observable streams
4. **Type safety**: Better intellisense and compile-time checking

### Setting Up Reactive Forms

**Module Import Strategy:**
```typescript
// app.module.ts - Enable reactive forms
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    ReactiveFormsModule  // Note: Different from FormsModule for template-driven
  ],
  // ...
})
export class AppModule { }

// For feature modules, import in each module that needs reactive forms
@NgModule({
  imports: [ReactiveFormsModule],
  declarations: [MyFormComponent]
})
export class FeatureModule { }
```
```

### Form Controls and Form Groups

```typescript
// reactive-form.component.ts
import { Component, OnInit } from '@angular/core';
import { 
  FormBuilder, 
  FormGroup, 
  FormControl, 
  Validators,
  AbstractControl 
} from '@angular/forms';

@Component({
  selector: 'app-reactive-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <div>
        <label for="name">Name:</label>
        <input 
          type="text" 
          id="name" 
          formControlName="name"
          [class.error]="isFieldInvalid('name')">
        <div>
          <div>
            @if (isFieldInvalid('name')) {
              <div class="error-message">
                @if (userForm.get('name')?.errors?.['required']) {
                  <div>Name is required</div>
                }
                @if (userForm.get('name')?.errors?.['minlength']) {
                  <div>Name must be at least 2 characters</div>
                }
              </div>
            }
          </div>
        </div>
      </div>

      <div>
        <label for="email">Email:</label>
        <input 
          type="email" 
          id="email" 
          formControlName="email"
          [class.error]="isFieldInvalid('email')">
        <div>
          @if (isFieldInvalid('email')) {
            <div class="error-message">
              @if (userForm.get('email')?.errors?.['required']) {
                <div>Email is required</div>
              }
              @if (userForm.get('email')?.errors?.['email']) {
                <div>Please enter a valid email</div>
              }
            </div>
          }
        </div>
      </div>

      <div formGroupName="address">
        <h3>Address</h3>
        <div>
          <label for="street">Street:</label>
          <input 
            type="text" 
            id="street" 
            formControlName="street"
            [class.error]="isNestedFieldInvalid('address', 'street')">
        </div>
        
        <div>
          <label for="city">City:</label>
          <input 
            type="text" 
            id="city" 
            formControlName="city"
            [class.error]="isNestedFieldInvalid('address', 'city')">
        </div>
      </div>

      <button type="submit" [disabled]="userForm.invalid">Submit</button>
    </form>

    <!-- Form Debug Info -->
    <div class="debug-info">
      <h4>Form State:</h4>
      <p>Valid: {{ userForm.valid }}</p>
      <p>Value: {{ userForm.value | json }}</p>
      <p>Errors: {{ userForm.errors | json }}</p>
    </div>
  `
})
export class ReactiveFormComponent implements OnInit {
  userForm: FormGroup;

  constructor(private fb: FormBuilder) {
    this.userForm = this.fb.group({
      name: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.email]],
      age: [null, [Validators.required, Validators.min(1), Validators.max(120)]],
      address: this.fb.group({
        street: ['', Validators.required],
        city: ['', Validators.required],
        zipCode: ['', [Validators.required, Validators.pattern(/^\d{5}$/)]]
      })
    });
  }

  ngOnInit() {
    // Subscribe to form value changes
    this.userForm.valueChanges.subscribe(value => {
      console.log('Form value changed:', value);
    });

    // Subscribe to specific field changes
    this.userForm.get('name')?.valueChanges.subscribe(name => {
      console.log('Name changed:', name);
    });
  }

  onSubmit() {
    if (this.userForm.valid) {
      console.log('Form submitted:', this.userForm.value);
    } else {
      this.markFormGroupTouched();
    }
  }

  isFieldInvalid(fieldName: string): boolean {
    const field = this.userForm.get(fieldName);
    return !!(field && field.invalid && field.touched);
  }

  isNestedFieldInvalid(groupName: string, fieldName: string): boolean {
    const field = this.userForm.get(`${groupName}.${fieldName}`);
    return !!(field && field.invalid && field.touched);
  }

  private markFormGroupTouched() {
    Object.keys(this.userForm.controls).forEach(key => {
      const control = this.userForm.get(key);
      control?.markAsTouched();

      if (control instanceof FormGroup) {
        this.markFormGroupTouched();
      }
    });
  }
}
```

### FormBuilder vs Direct Instantiation

```typescript
// Method 1: Using FormBuilder (Recommended)
constructor(private fb: FormBuilder) {
  this.userForm = this.fb.group({
    name: ['', Validators.required],
    email: ['', [Validators.required, Validators.email]],
    preferences: this.fb.group({
      newsletter: [false],
      notifications: [true]
    })
  });
}

// Method 2: Direct instantiation
constructor() {
  this.userForm = new FormGroup({
    name: new FormControl('', Validators.required),
    email: new FormControl('', [Validators.required, Validators.email]),
    preferences: new FormGroup({
      newsletter: new FormControl(false),
      notifications: new FormControl(true)
    })
  });
}
```

---

## Form Validation

### Why Validation Matters

**Business Impact of Proper Validation:**
- **Data integrity**: Prevents corrupt data from entering your system
- **User experience**: Provides immediate feedback to users
- **Security**: First line of defense against malicious input
- **Cost reduction**: Catches errors before expensive server processing

**Types of Validation Needs:**
1. **Format validation**: Email format, phone numbers, postal codes
2. **Business rules**: Age restrictions, unique usernames, password policies
3. **Cross-field validation**: Password confirmation, date ranges
4. **Async validation**: Server-side uniqueness checks, external API validation

### How Angular Validation Works

**The Validator Function Pattern:**
```typescript
// All validators follow this pattern:
type ValidatorFn = (control: AbstractControl) => ValidationErrors | null;

// Return null if valid, error object if invalid
const requiredValidator: ValidatorFn = (control: AbstractControl) => {
  return control.value ? null : { required: true };
};
```

**Validation Execution Flow:**
1. **User input**: User types or changes form control value
2. **Validator execution**: Angular runs all validators for that control
3. **Error aggregation**: All validation errors are collected into errors object
4. **Status update**: Control status updated (valid/invalid/pending)
5. **Change propagation**: Parent form group updates its status
6. **UI update**: Template reacts to status changes

### When to Use Built-in vs Custom Validators

**Built-in Validators - Use When:**
- Standard validation patterns (required, email, min/max)
- Quick prototyping and common use cases
- HTML5 validation equivalents needed

**Custom Validators - Use When:**
- Business-specific validation rules
- Complex validation logic
- Cross-field validation required
- Integration with external services needed
```

### Built-in Validators Deep Dive

**Why Angular Provides Built-in Validators:**
Built-in validators cover 80% of common validation scenarios and provide consistent behavior across applications.

```typescript
import { Validators } from '@angular/forms';

// Built-in validator usage with explanations
const validationStrategies = {
  // Essential validators for data integrity
  required: Validators.required,           // Prevents null/undefined/empty string
  email: Validators.email,                 // RFC 5322 email format validation
  
  // Length constraints for text inputs
  minLength: Validators.minLength(3),      // Minimum character count (good for usernames)
  maxLength: Validators.maxLength(50),     // Maximum character count (prevents spam)
  
  // Numeric range validation
  min: Validators.min(1),                  // Minimum numeric value
  max: Validators.max(100),                // Maximum numeric value
  
  // Pattern matching for custom formats
  pattern: Validators.pattern(/^[a-zA-Z]+$/), // Regex pattern (letters only)
  
  // Combining multiple validators for comprehensive validation
  strongPassword: [
    Validators.required,
    Validators.minLength(8),
    Validators.pattern(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/)
  ]
};

// Real-world validator combinations
const realWorldValidators = {
  // Username validation
  username: [
    Validators.required,
    Validators.minLength(3),
    Validators.maxLength(20),
    Validators.pattern(/^[a-zA-Z0-9_]+$/) // Alphanumeric + underscore only
  ],
  
  // Age validation
  age: [
    Validators.required,
    Validators.min(13),    // Minimum age for account creation
    Validators.max(120)    // Reasonable maximum age
  ],
  
  // Phone number validation (US format)
  phone: [
    Validators.required,
    Validators.pattern(/^\(\d{3}\) \d{3}-\d{4}$/) // (555) 123-4567 format
  ]
};
```

### Strategic Error Handling Component

**Why Create a Reusable Error Handler:**
- **Consistency**: Same error messages across the application
- **Maintainability**: Update error messages in one place
- **Internationalization**: Easy to add i18n support
- **User experience**: Professional, helpful error messages

```typescript
// error-handler.component.ts
@Component({
  selector: 'app-error-handler',
  template: `
    <div *ngIf="shouldShowErrors()" class="error-container">
      <div *ngFor="let error of getErrorMessages()" class="error-message">
        <i class="error-icon" aria-hidden="true">⚠️</i>
        {{ error }}
      </div>
    </div>
  `,
  styles: [`
    .error-container {
      margin-top: 0.25rem;
      font-size: 0.875rem;
    }
    .error-message {
      color: #dc2626;
      display: flex;
      align-items: center;
      gap: 0.25rem;
    }
    .error-icon {
      font-size: 0.75rem;
    }
  `]
})
export class ErrorHandlerComponent {
  @Input() control: AbstractControl | null = null;
  @Input() fieldName: string = '';
  @Input() showWhen: 'touched' | 'dirty' | 'submitted' = 'touched';

  /**
   * Determines when to show error messages based on interaction state
   */
  shouldShowErrors(): boolean {
    if (!this.control || !this.control.errors) return false;
    
    switch (this.showWhen) {
      case 'touched':
        return this.control.touched;
      case 'dirty':
        return this.control.dirty;
      case 'submitted':
        // Would need form submitted state passed down
        return true;
      default:
        return this.control.touched;
    }
  }

  /**
   * Error message mapping with context-aware messages
   */
  private errorMessages: { [key: string]: (params: any) => string } = {
    required: () => `${this.fieldName} is required`,
    email: () => 'Please enter a valid email address',
    minlength: (params) => 
      `${this.fieldName} must be at least ${params.requiredLength} characters (currently ${params.actualLength})`,
    maxlength: (params) => 
      `${this.fieldName} cannot exceed ${params.requiredLength} characters`,
    min: (params) => 
      `${this.fieldName} must be at least ${params.min}`,
    max: (params) => 
      `${this.fieldName} cannot exceed ${params.max}`,
    pattern: () => 
      `${this.fieldName} format is invalid`,
    // Custom validator error messages
    passwordStrength: () => 
      'Password must contain uppercase, lowercase, number, and special character',
    confirmPassword: () => 
      'Passwords do not match',
    emailExists: () => 
      'This email is already registered',
    usernameTaken: () => 
      'This username is not available'
  };

  /**
   * Generates user-friendly error messages
   */
  getErrorMessages(): string[] {
    if (!this.control?.errors) return [];

    return Object.keys(this.control.errors).map(errorKey => {
      const errorHandler = this.errorMessages[errorKey];
      return errorHandler 
        ? errorHandler(this.control!.errors![errorKey]) 
        : `${this.fieldName} is invalid`;
    });
  }
}

// Usage in parent component template:
/*
<input formControlName="email" placeholder="Email">
<app-error-handler 
  [control]="userForm.get('email')" 
  fieldName="Email"
  showWhen="touched">
</app-error-handler>
*/
```

**Error Handling Best Practices:**

1. **Progressive disclosure**: Show errors only when appropriate
2. **Helpful messaging**: Explain what's wrong and how to fix it
3. **Accessibility**: Include ARIA attributes for screen readers
4. **Visual hierarchy**: Use icons and colors to draw attention
5. **Contextual help**: Provide examples for complex formats
```

---

## Custom Validators

### Synchronous Custom Validators

```typescript
// custom-validators.ts
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export class CustomValidators {
  
  // Password strength validator
  static passwordStrength(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      const value = control.value;
      
      if (!value) return null;
      
      const hasNumber = /[0-9]/.test(value);
      const hasUpper = /[A-Z]/.test(value);
      const hasLower = /[a-z]/.test(value);
      const hasSpecial = /[#?!@$%^&*-]/.test(value);
      const isValidLength = value.length >= 8;
      
      const passwordValid = hasNumber && hasUpper && hasLower && hasSpecial && isValidLength;
      
      if (!passwordValid) {
        return {
          passwordStrength: {
            hasNumber,
            hasUpper,
            hasLower,
            hasSpecial,
            isValidLength
          }
        };
      }
      
      return null;
    };
  }

  // Confirm password validator
  static confirmPassword(passwordControlName: string): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      const password = control.parent?.get(passwordControlName);
      const confirmPassword = control.value;
      
      if (!password || !confirmPassword) return null;
      
      return password.value === confirmPassword ? null : { confirmPassword: true };
    };
  }

  // Age validator
  static ageValidator(minAge: number = 18): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      const birthDate = new Date(control.value);
      const today = new Date();
      const age = today.getFullYear() - birthDate.getFullYear();
      const monthDiff = today.getMonth() - birthDate.getMonth();
      
      if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
      }
      
      return age >= minAge ? null : { age: { actualAge: age, requiredAge: minAge } };
    };
  }

  // Forbidden name validator
  static forbiddenName(forbiddenNames: string[]): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      const value = control.value?.toLowerCase();
      const isForbidden = forbiddenNames.some(name => 
        name.toLowerCase() === value
      );
      
      return isForbidden ? { forbiddenName: { value: control.value } } : null;
    };
  }

  // Credit card validator
  static creditCard(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      const value = control.value?.replace(/\s/g, '');
      
      if (!value) return null;
      
      // Luhn algorithm implementation
      let sum = 0;
      let alternate = false;
      
      for (let i = value.length - 1; i >= 0; i--) {
        let n = parseInt(value.charAt(i), 10);
        
        if (alternate) {
          n *= 2;
          if (n > 9) {
            n = (n % 10) + 1;
          }
        }
        
        sum += n;
        alternate = !alternate;
      }
      
      return (sum % 10 === 0) ? null : { creditCard: true };
    };
  }
}
```

### Asynchronous Validators

```typescript
// async-validators.ts
import { Injectable } from '@angular/core';
import { AbstractControl, AsyncValidatorFn, ValidationErrors } from '@angular/forms';
import { Observable, of, timer } from 'rxjs';
import { map, switchMap, catchError } from 'rxjs/operators';
import { HttpClient } from '@angular/common/http';

@Injectable({ providedIn: 'root' })
export class AsyncValidators {
  
  constructor(private http: HttpClient) {}

  // Email uniqueness validator
  emailExists(): AsyncValidatorFn {
    return (control: AbstractControl): Observable<ValidationErrors | null> => {
      if (!control.value) {
        return of(null);
      }

      return timer(500).pipe(
        switchMap(() => 
          this.http.get<{exists: boolean}>(`/api/check-email/${control.value}`)
        ),
        map(result => result.exists ? { emailExists: true } : null),
        catchError(() => of(null))
      );
    };
  }

  // Username availability validator
  usernameAvailable(): AsyncValidatorFn {
    return (control: AbstractControl): Observable<ValidationErrors | null> => {
      if (!control.value || control.value.length < 3) {
        return of(null);
      }

      return timer(300).pipe(
        switchMap(() => 
          this.http.post<{available: boolean}>('/api/check-username', {
            username: control.value
          })
        ),
        map(result => result.available ? null : { usernameTaken: true }),
        catchError(() => of({ usernameCheckFailed: true }))
      );
    };
  }
}
```

### Using Custom Validators

```typescript
// registration-form.component.ts
@Component({
  selector: 'app-registration-form',
  template: `
    <form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">
      <div>
        <label>Username:</label>
        <input formControlName="username">
        <div *ngIf="isFieldInvalid('username')">
          <div *ngIf="registrationForm.get('username')?.errors?.['required']">
            Username is required
          </div>
          <div *ngIf="registrationForm.get('username')?.errors?.['usernameTaken']">
            Username is already taken
          </div>
          <div *ngIf="registrationForm.get('username')?.pending">
            Checking availability...
          </div>
        </div>
      </div>

      <div>
        <label>Password:</label>
        <input type="password" formControlName="password">
        <div *ngIf="isFieldInvalid('password')">
          <div *ngIf="registrationForm.get('password')?.errors?.['passwordStrength']" class="password-requirements">
            <p>Password must contain:</p>
            <ul>
              <li [class.valid]="passwordErrors?.hasNumber">At least one number</li>
              <li [class.valid]="passwordErrors?.hasUpper">At least one uppercase letter</li>
              <li [class.valid]="passwordErrors?.hasLower">At least one lowercase letter</li>
              <li [class.valid]="passwordErrors?.hasSpecial">At least one special character</li>
              <li [class.valid]="passwordErrors?.isValidLength">At least 8 characters long</li>
            </ul>
          </div>
        </div>
      </div>

      <div>
        <label>Confirm Password:</label>
        <input type="password" formControlName="confirmPassword">
        <div *ngIf="isFieldInvalid('confirmPassword')">
          <div *ngIf="registrationForm.get('confirmPassword')?.errors?.['confirmPassword']">
            Passwords do not match
          </div>
        </div>
      </div>

      <button type="submit" [disabled]="registrationForm.invalid">Register</button>
    </form>
  `
})
export class RegistrationFormComponent implements OnInit {
  registrationForm: FormGroup;

  constructor(
    private fb: FormBuilder,
    private asyncValidators: AsyncValidators
  ) {
    this.registrationForm = this.fb.group({
      username: ['', 
        [Validators.required, Validators.minLength(3)],
        [this.asyncValidators.usernameAvailable()]
      ],
      email: ['',
        [Validators.required, Validators.email],
        [this.asyncValidators.emailExists()]
      ],
      password: ['', [
        Validators.required,
        CustomValidators.passwordStrength()
      ]],
      confirmPassword: ['', [
        Validators.required,
        CustomValidators.confirmPassword('password')
      ]]
    });
  }

  get passwordErrors() {
    return this.registrationForm.get('password')?.errors?.['passwordStrength'];
  }

  isFieldInvalid(fieldName: string): boolean {
    const field = this.registrationForm.get(fieldName);
    return !!(field && field.invalid && field.touched);
  }

  onSubmit() {
    if (this.registrationForm.valid) {
      console.log('Registration data:', this.registrationForm.value);
    }
  }
}
```

---

## Advanced Form Patterns

### Dynamic Forms

```typescript
// dynamic-form.component.ts
interface FormField {
  type: 'text' | 'email' | 'number' | 'select' | 'checkbox';
  name: string;
  label: string;
  required: boolean;
  options?: { value: any; label: string }[];
  validators?: ValidatorFn[];
}

@Component({
  selector: 'app-dynamic-form',
  template: `
    <form [formGroup]="dynamicForm" (ngSubmit)="onSubmit()">
      <div *ngFor="let field of formFields" class="form-field">
        <label [for]="field.name">{{ field.label }}</label>
        
        <input 
          *ngIf="field.type === 'text' || field.type === 'email' || field.type === 'number'"
          [type]="field.type"
          [id]="field.name"
          [formControlName]="field.name"
          [class.error]="isFieldInvalid(field.name)">
        
        <select 
          *ngIf="field.type === 'select'"
          [id]="field.name"
          [formControlName]="field.name"
          [class.error]="isFieldInvalid(field.name)">
          <option value="">Select...</option>
          <option *ngFor="let option of field.options" [value]="option.value">
            {{ option.label }}
          </option>
        </select>
        
        <input 
          *ngIf="field.type === 'checkbox'"
          type="checkbox"
          [id]="field.name"
          [formControlName]="field.name">
        
        <div *ngIf="isFieldInvalid(field.name)" class="error-message">
          {{ getFieldError(field.name) }}
        </div>
      </div>
      
      <button type="submit" [disabled]="dynamicForm.invalid">Submit</button>
    </form>
  `
})
export class DynamicFormComponent implements OnInit {
  @Input() formFields: FormField[] = [];
  dynamicForm: FormGroup;

  constructor(private fb: FormBuilder) {
    this.dynamicForm = this.fb.group({});
  }

  ngOnInit() {
    this.buildForm();
  }

  private buildForm() {
    const formControls: { [key: string]: FormControl } = {};
    
    this.formFields.forEach(field => {
      const validators = field.validators || [];
      if (field.required) {
        validators.push(Validators.required);
      }
      
      formControls[field.name] = new FormControl('', validators);
    });
    
    this.dynamicForm = this.fb.group(formControls);
  }

  isFieldInvalid(fieldName: string): boolean {
    const field = this.dynamicForm.get(fieldName);
    return !!(field && field.invalid && field.touched);
  }

  getFieldError(fieldName: string): string {
    const field = this.dynamicForm.get(fieldName);
    if (field?.errors?.['required']) return 'This field is required';
    if (field?.errors?.['email']) return 'Please enter a valid email';
    return 'Invalid input';
  }

  onSubmit() {
    if (this.dynamicForm.valid) {
      console.log('Dynamic form data:', this.dynamicForm.value);
    }
  }
}
```

### Form Arrays

```typescript
// form-array.component.ts
@Component({
  selector: 'app-form-array',
  template: `
    <form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
      <div>
        <label>Name:</label>
        <input formControlName="name">
      </div>

      <div formArrayName="skills">
        <h3>Skills</h3>
        <div *ngFor="let skill of skillsArray.controls; let i = index" [formGroupName]="i">
          <input formControlName="name" placeholder="Skill name">
          <input formControlName="level" type="number" placeholder="Level (1-10)">
          <button type="button" (click)="removeSkill(i)">Remove</button>
        </div>
        <button type="button" (click)="addSkill()">Add Skill</button>
      </div>

      <div formArrayName="experiences">
        <h3>Work Experience</h3>
        <div *ngFor="let exp of experiencesArray.controls; let i = index" [formGroupName]="i">
          <input formControlName="company" placeholder="Company">
          <input formControlName="position" placeholder="Position">
          <input formControlName="startDate" type="date">
          <input formControlName="endDate" type="date">
          <button type="button" (click)="removeExperience(i)">Remove</button>
        </div>
        <button type="button" (click)="addExperience()">Add Experience</button>
      </div>

      <button type="submit" [disabled]="profileForm.invalid">Submit</button>
    </form>
  `
})
export class FormArrayComponent implements OnInit {
  profileForm: FormGroup;

  constructor(private fb: FormBuilder) {
    this.profileForm = this.fb.group({
      name: ['', Validators.required],
      skills: this.fb.array([]),
      experiences: this.fb.array([])
    });
  }

  ngOnInit() {
    this.addSkill();
    this.addExperience();
  }

  get skillsArray(): FormArray {
    return this.profileForm.get('skills') as FormArray;
  }

  get experiencesArray(): FormArray {
    return this.profileForm.get('experiences') as FormArray;
  }

  addSkill() {
    const skillGroup = this.fb.group({
      name: ['', Validators.required],
      level: [1, [Validators.required, Validators.min(1), Validators.max(10)]]
    });
    this.skillsArray.push(skillGroup);
  }

  removeSkill(index: number) {
    this.skillsArray.removeAt(index);
  }

  addExperience() {
    const expGroup = this.fb.group({
      company: ['', Validators.required],
      position: ['', Validators.required],
      startDate: ['', Validators.required],
      endDate: ['']
    });
    this.experiencesArray.push(expGroup);
  }

  removeExperience(index: number) {
    this.experiencesArray.removeAt(index);
  }

  onSubmit() {
    if (this.profileForm.valid) {
      console.log('Profile data:', this.profileForm.value);
    }
  }
}
```

---

## Performance Optimization

### OnPush Strategy with Forms

```typescript
// optimized-form.component.ts
@Component({
  selector: 'app-optimized-form',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <input formControlName="name" placeholder="Name">
      <input formControlName="email" placeholder="Email">
      
      <!-- Use OnPush-safe async pipe -->
      <div *ngIf="formStatus$ | async as status">
        Form Status: {{ status }}
      </div>
      
      <button type="submit" [disabled]="!formValid">Submit</button>
    </form>
  `
})
export class OptimizedFormComponent implements OnInit {
  userForm: FormGroup;
  formStatus$: Observable<string>;
  formValid = false;

  constructor(
    private fb: FormBuilder,
    private cdr: ChangeDetectorRef
  ) {
    this.userForm = this.fb.group({
      name: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]]
    });
  }

  ngOnInit() {
    // Create observables for OnPush compatibility
    this.formStatus$ = this.userForm.statusChanges.pipe(
      map(status => status === 'VALID' ? 'Valid' : 'Invalid')
    );

    // Subscribe to form validity changes
    this.userForm.statusChanges.subscribe(status => {
      this.formValid = status === 'VALID';
      this.cdr.markForCheck(); // Trigger change detection
    });
  }

  onSubmit() {
    if (this.userForm.valid) {
      console.log('Form submitted:', this.userForm.value);
    }
  }
}
```

### Debounced Validation

```typescript
// debounced-validation.component.ts
@Component({
  selector: 'app-debounced-validation',
  template: `
    <form [formGroup]="searchForm">
      <input 
        formControlName="searchTerm" 
        placeholder="Search..."
        (input)="onSearchInput()">
      
      <div *ngIf="searchForm.get('searchTerm')?.pending">
        Searching...
      </div>
      
      <div *ngIf="searchResults$ | async as results">
        <div *ngFor="let result of results">{{ result.name }}</div>
      </div>
    </form>
  `
})
export class DebouncedValidationComponent implements OnInit {
  searchForm: FormGroup;
  searchResults$: Observable<any[]>;

  constructor(
    private fb: FormBuilder,
    private searchService: SearchService
  ) {
    this.searchForm = this.fb.group({
      searchTerm: ['']
    });
  }

  ngOnInit() {
    // Debounced search with RxJS
    this.searchResults$ = this.searchForm.get('searchTerm')!.valueChanges.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      filter(term => term.length >= 2),
      switchMap(term => this.searchService.search(term)),
      catchError(() => of([]))
    );
  }

  onSearchInput() {
    // Additional logic if needed
  }
}
```

### Form Caching Strategy

```typescript
// form-cache.service.ts
@Injectable({ providedIn: 'root' })
export class FormCacheService {
  private cache = new Map<string, any>();

  saveFormData(formId: string, data: any): void {
    this.cache.set(formId, data);
    // Also save to localStorage for persistence
    localStorage.setItem(`form_${formId}`, JSON.stringify(data));
  }

  getFormData(formId: string): any {
    // Try memory cache first
    if (this.cache.has(formId)) {
      return this.cache.get(formId);
    }

    // Fallback to localStorage
    const stored = localStorage.getItem(`form_${formId}`);
    if (stored) {
      const data = JSON.parse(stored);
      this.cache.set(formId, data);
      return data;
    }

    return null;
  }

  clearFormData(formId: string): void {
    this.cache.delete(formId);
    localStorage.removeItem(`form_${formId}`);
  }
}

// cached-form.component.ts
@Component({
  selector: 'app-cached-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <input formControlName="name" placeholder="Name">
      <input formControlName="email" placeholder="Email">
      <textarea formControlName="message" placeholder="Message"></textarea>
      
      <button type="submit">Submit</button>
      <button type="button" (click)="clearCache()">Clear Cache</button>
    </form>
  `
})
export class CachedFormComponent implements OnInit, OnDestroy {
  userForm: FormGroup;
  private formId = 'user-contact-form';
  private subscription: Subscription = new Subscription();

  constructor(
    private fb: FormBuilder,
    private formCache: FormCacheService
  ) {
    this.userForm = this.fb.group({
      name: [''],
      email: [''],
      message: ['']
    });
  }

  ngOnInit() {
    // Load cached data
    const cachedData = this.formCache.getFormData(this.formId);
    if (cachedData) {
      this.userForm.patchValue(cachedData);
    }

    // Auto-save form data
    this.subscription.add(
      this.userForm.valueChanges.pipe(
        debounceTime(1000),
        distinctUntilChanged((prev, curr) => JSON.stringify(prev) === JSON.stringify(curr))
      ).subscribe(value => {
        this.formCache.saveFormData(this.formId, value);
      })
    );
  }

  ngOnDestroy() {
    this.subscription.unsubscribe();
  }

  onSubmit() {
    if (this.userForm.valid) {
      console.log('Form submitted:', this.userForm.value);
      this.formCache.clearFormData(this.formId);
    }
  }

  clearCache() {
    this.formCache.clearFormData(this.formId);
    this.userForm.reset();
  }
}
```

---

## Interview Scenarios

### Tier 1 Companies (Google, Meta, Amazon)

**Scenario 1: Complex Form Validation**
```typescript
// Question: "Implement a registration form with real-time password validation, 
// email uniqueness check, and username availability validation."

@Component({
  selector: 'app-advanced-registration',
  template: `
    <form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">
      <!-- Implementation with all validations -->
    </form>
  `
})
export class AdvancedRegistrationComponent {
  registrationForm: FormGroup;

  constructor(
    private fb: FormBuilder,
    private userService: UserService
  ) {
    this.registrationForm = this.fb.group({
      username: ['', 
        [Validators.required, Validators.minLength(3)],
        [this.usernameValidator.bind(this)]
      ],
      email: ['',
        [Validators.required, Validators.email],
        [this.emailValidator.bind(this)]
      ],
      password: ['', [
        Validators.required,
        this.passwordStrengthValidator
      ]],
      confirmPassword: ['', [
        Validators.required,
        this.confirmPasswordValidator
      ]]
    });
  }

  // Custom async validators
  usernameValidator(control: AbstractControl): Observable<ValidationErrors | null> {
    if (!control.value) return of(null);
    
    return timer(300).pipe(
      switchMap(() => this.userService.checkUsername(control.value)),
      map(exists => exists ? { usernameTaken: true } : null)
    );
  }

  emailValidator(control: AbstractControl): Observable<ValidationErrors | null> {
    if (!control.value) return of(null);
    
    return timer(500).pipe(
      switchMap(() => this.userService.checkEmail(control.value)),
      map(exists => exists ? { emailExists: true } : null)
    );
  }

  passwordStrengthValidator(control: AbstractControl): ValidationErrors | null {
    const value = control.value;
    if (!value) return null;

    const requirements = {
      length: value.length >= 8,
      uppercase: /[A-Z]/.test(value),
      lowercase: /[a-z]/.test(value),
      number: /\d/.test(value),
      special: /[!@#$%^&*(),.?":{}|<>]/.test(value)
    };

    const isValid = Object.values(requirements).every(req => req);
    return isValid ? null : { passwordStrength: requirements };
  }

  confirmPasswordValidator(control: AbstractControl): ValidationErrors | null {
    const password = control.parent?.get('password');
    return password && control.value === password.value ? null : { mismatch: true };
  }
}
```

**Scenario 2: Dynamic Form Generation**
```typescript
// Question: "Create a form builder that generates forms from JSON configuration"

interface FormConfig {
  fields: {
    name: string;
    type: 'text' | 'email' | 'number' | 'select' | 'checkbox';
    label: string;
    required: boolean;
    validators?: any[];
    options?: { value: any; label: string }[];
  }[];
}

@Injectable({ providedIn: 'root' })
export class DynamicFormBuilder {
  constructor(private fb: FormBuilder) {}

  buildForm(config: FormConfig): FormGroup {
    const formControls: { [key: string]: FormControl } = {};

    config.fields.forEach(field => {
      const validators = this.buildValidators(field);
      formControls[field.name] = new FormControl('', validators);
    });

    return this.fb.group(formControls);
  }

  private buildValidators(field: any): ValidatorFn[] {
    const validators: ValidatorFn[] = [];

    if (field.required) validators.push(Validators.required);
    if (field.type === 'email') validators.push(Validators.email);
    if (field.validators) {
      field.validators.forEach((v: any) => {
        switch (v.type) {
          case 'minLength':
            validators.push(Validators.minLength(v.value));
            break;
          case 'maxLength':
            validators.push(Validators.maxLength(v.value));
            break;
          case 'pattern':
            validators.push(Validators.pattern(v.value));
            break;
        }
      });
    }

    return validators;
  }
}
```

### Tier 2 Companies (Microsoft, Netflix, Uber)

**Scenario 1: Multi-Step Form Wizard**
```typescript
// Question: "Design a multi-step form wizard with validation and state persistence"

@Component({
  selector: 'app-wizard-form',
  template: `
    <div class="wizard-container">
      <div class="step-indicator">
        @for (step of steps; track step.id) {
          <div class="step" [class.active]="currentStep === step.id" 
               [class.completed]="step.id < currentStep">
            {{ step.title }}
          </div>
        }
      </div>

      <form [formGroup]="wizardForm" (ngSubmit)="onSubmit()">
        @switch (currentStep) {
          @case (1) {
            <!-- Personal Information Step -->
            <div class="step-content">
              <h3>Personal Information</h3>
              <input formControlName="firstName" placeholder="First Name">
              <input formControlName="lastName" placeholder="Last Name">
              <input formControlName="email" placeholder="Email">
            </div>
          }
          @case (2) {
            <!-- Address Information Step -->
            <div class="step-content">
              <h3>Address Information</h3>
              <input formControlName="street" placeholder="Street">
              <input formControlName="city" placeholder="City">
              <input formControlName="zipCode" placeholder="ZIP Code">
            </div>
          }
          @case (3) {
            <!-- Review Step -->
            <div class="step-content">
              <h3>Review Your Information</h3>
              <div class="review-section">
                <h4>Personal Details:</h4>
                <p>{{ wizardForm.get('firstName')?.value }} {{ wizardForm.get('lastName')?.value }}</p>
                <p>{{ wizardForm.get('email')?.value }}</p>
              </div>
              <div class="review-section">
                <h4>Address:</h4>
                <p>{{ wizardForm.get('street')?.value }}</p>
                <p>{{ wizardForm.get('city')?.value }}, {{ wizardForm.get('zipCode')?.value }}</p>
              </div>
            </div>
          }
        }

        <div class="navigation-buttons">
          @if (currentStep > 1) {
            <button type="button" (click)="previousStep()">Previous</button>
          }
          @if (currentStep < steps.length) {
            <button type="button" [disabled]="!canProceed()" (click)="nextStep()">Next</button>
          }
          @if (currentStep === steps.length) {
            <button type="submit" [disabled]="wizardForm.invalid">Submit</button>
          }
        </div>
      </form>
    </div>
  `
})
export class WizardFormComponent implements OnInit {
  currentStep = 1;
  steps = [
    { id: 1, title: 'Personal Info' },
    { id: 2, title: 'Address' },
    { id: 3, title: 'Review' }
  ];

  wizardForm: FormGroup;

  constructor(
    private fb: FormBuilder,
    private wizardService: WizardStateService
  ) {
    this.wizardForm = this.fb.group({
      // Step 1 fields
      firstName: ['', [Validators.required, Validators.minLength(2)]],
      lastName: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.email]],
      
      // Step 2 fields
      street: ['', Validators.required],
      city: ['', Validators.required],
      zipCode: ['', [Validators.required, Validators.pattern(/^\d{5}$/)]]
    });
  }

  ngOnInit() {
    // Load persisted state
    const savedData = this.wizardService.getWizardData();
    if (savedData) {
      this.wizardForm.patchValue(savedData.formData);
      this.currentStep = savedData.currentStep;
    }

    // Auto-save on changes
    this.wizardForm.valueChanges.pipe(
      debounceTime(500),
      distinctUntilChanged()
    ).subscribe(value => {
      this.wizardService.saveWizardData({
        formData: value,
        currentStep: this.currentStep
      });
    });
  }

  canProceed(): boolean {
    switch (this.currentStep) {
      case 1:
        return this.wizardForm.get('firstName')?.valid &&
               this.wizardForm.get('lastName')?.valid &&
               this.wizardForm.get('email')?.valid;
      case 2:
        return this.wizardForm.get('street')?.valid &&
               this.wizardForm.get('city')?.valid &&
               this.wizardForm.get('zipCode')?.valid;
      default:
        return true;
    }
  }

  nextStep() {
    if (this.canProceed() && this.currentStep < this.steps.length) {
      this.currentStep++;
      this.wizardService.saveWizardData({
        formData: this.wizardForm.value,
        currentStep: this.currentStep
      });
    }
  }

  previousStep() {
    if (this.currentStep > 1) {
      this.currentStep--;
    }
  }

  onSubmit() {
    if (this.wizardForm.valid) {
      console.log('Wizard completed:', this.wizardForm.value);
      this.wizardService.clearWizardData();
    }
  }
}

@Injectable({ providedIn: 'root' })
export class WizardStateService {
  private readonly STORAGE_KEY = 'wizard_form_data';

  saveWizardData(data: any): void {
    localStorage.setItem(this.STORAGE_KEY, JSON.stringify(data));
  }

  getWizardData(): any {
    const data = localStorage.getItem(this.STORAGE_KEY);
    return data ? JSON.parse(data) : null;
  }

  clearWizardData(): void {
    localStorage.removeItem(this.STORAGE_KEY);
  }
}
```

**Scenario 2: Form Performance Optimization**
```typescript
// Question: "How would you optimize a form with 100+ fields for performance?"

@Component({
  selector: 'app-large-form',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <form [formGroup]="largeForm" (ngSubmit)="onSubmit()">
      @for (section of formSections; track section.id) {
        <div class="form-section" [class.collapsed]="!section.expanded">
          <h3 (click)="toggleSection(section.id)">
            {{ section.title }}
            <span class="toggle-icon">{{ section.expanded ? '−' : '+' }}</span>
          </h3>
          
          @if (section.expanded) {
            <div class="section-content">
              @for (field of section.fields; track field.name) {
                <div class="form-field">
                  <label>{{ field.label }}</label>
                  <input [formControlName]="field.name" [type]="field.type">
                  @if (getFieldError(field.name)) {
                    <div class="error">{{ getFieldError(field.name) }}</div>
                  }
                </div>
              }
            </div>
          }
        </div>
      }
      
      <button type="submit" [disabled]="largeForm.invalid">Submit</button>
    </form>
  `
})
export class LargeFormComponent implements OnInit {
  largeForm: FormGroup;
  formSections: FormSection[] = [];

  constructor(
    private fb: FormBuilder,
    private cdr: ChangeDetectorRef
  ) {}

  ngOnInit() {
    this.buildForm();
    this.optimizeFormSubscriptions();
  }

  private buildForm() {
    const formControls: { [key: string]: FormControl } = {};
    
    // Create form controls efficiently
    this.formSections.forEach(section => {
      section.fields.forEach(field => {
        formControls[field.name] = new FormControl('', field.validators || []);
      });
    });

    this.largeForm = this.fb.group(formControls);
  }

  private optimizeFormSubscriptions() {
    // Only subscribe to dirty controls to reduce change detection
    this.largeForm.valueChanges.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      takeUntil(this.destroy$)
    ).subscribe(() => {
      // Only trigger change detection when necessary
      this.cdr.markForCheck();
    });
  }

  toggleSection(sectionId: string) {
    const section = this.formSections.find(s => s.id === sectionId);
    if (section) {
      section.expanded = !section.expanded;
      this.cdr.markForCheck();
    }
  }

  getFieldError(fieldName: string): string | null {
    const control = this.largeForm.get(fieldName);
    if (control?.errors && control.touched) {
      return Object.keys(control.errors)[0]; // Return first error
    }
    return null;
  }

  onSubmit() {
    if (this.largeForm.valid) {
      console.log('Large form submitted:', this.largeForm.value);
    }
  }
}
```

### Tier 3 Companies (Startups, Mid-size)

**Scenario 1: Basic Form Implementation**
```typescript
// Question: "Create a contact form with validation and error handling"

@Component({
  selector: 'app-contact-form',
  template: `
    <form [formGroup]="contactForm" (ngSubmit)="onSubmit()">
      <div class="form-group">
        <label for="name">Name *</label>
        <input 
          id="name" 
          type="text" 
          formControlName="name"
          [class.error]="isFieldInvalid('name')">
        @if (isFieldInvalid('name')) {
          <div class="error-message">
            @if (contactForm.get('name')?.errors?.['required']) {
              <span>Name is required</span>
            }
            @if (contactForm.get('name')?.errors?.['minlength']) {
              <span>Name must be at least 2 characters</span>
            }
          </div>
        }
      </div>

      <div class="form-group">
        <label for="email">Email *</label>
        <input 
          id="email" 
          type="email" 
          formControlName="email"
          [class.error]="isFieldInvalid('email')">
        @if (isFieldInvalid('email')) {
          <div class="error-message">
            @if (contactForm.get('email')?.errors?.['required']) {
              <span>Email is required</span>
            }
            @if (contactForm.get('email')?.errors?.['email']) {
              <span>Please enter a valid email</span>
            }
          </div>
        }
      </div>

      <div class="form-group">
        <label for="message">Message *</label>
        <textarea 
          id="message" 
          formControlName="message"
          rows="4"
          [class.error]="isFieldInvalid('message')">
        </textarea>
        @if (isFieldInvalid('message')) {
          <div class="error-message">
            @if (contactForm.get('message')?.errors?.['required']) {
              <span>Message is required</span>
            }
            @if (contactForm.get('message')?.errors?.['minlength']) {
              <span>Message must be at least 10 characters</span>
            }
          </div>
        }
      </div>

      <div class="form-actions">
        <button 
          type="submit" 
          [disabled]="contactForm.invalid || isSubmitting"
          class="submit-btn">
          @if (isSubmitting) {
            <span>Sending...</span>
          } @else {
            <span>Send Message</span>
          }
        </button>
      </div>
    </form>

    @if (submitSuccess) {
      <div class="success-message">
        Message sent successfully! We'll get back to you soon.
      </div>
    }
  `,
  styles: [`
    .form-group {
      margin-bottom: 1rem;
    }
    
    .error {
      border-color: #dc2626;
    }
    
    .error-message {
      color: #dc2626;
      font-size: 0.875rem;
      margin-top: 0.25rem;
    }
    
    .success-message {
      background-color: #10b981;
      color: white;
      padding: 1rem;
      border-radius: 0.375rem;
      margin-top: 1rem;
    }
  `]
})
export class ContactFormComponent {
  contactForm: FormGroup;
  isSubmitting = false;
  submitSuccess = false;

  constructor(
    private fb: FormBuilder,
    private contactService: ContactService
  ) {
    this.contactForm = this.fb.group({
      name: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.email]],
      message: ['', [Validators.required, Validators.minLength(10)]]
    });
  }

  isFieldInvalid(fieldName: string): boolean {
    const field = this.contactForm.get(fieldName);
    return !!(field && field.invalid && field.touched);
  }

  async onSubmit() {
    if (this.contactForm.valid) {
      this.isSubmitting = true;
      
      try {
        await this.contactService.sendMessage(this.contactForm.value);
        this.submitSuccess = true;
        this.contactForm.reset();
      } catch (error) {
        console.error('Error sending message:', error);
        // Handle error appropriately
      } finally {
        this.isSubmitting = false;
      }
    }
  }
}
```

**Scenario 2: Form Arrays for Dynamic Content**
```typescript
// Question: "Implement a skills form where users can add/remove skills dynamically"

@Component({
  selector: 'app-skills-form',
  template: `
    <form [formGroup]="skillsForm" (ngSubmit)="onSubmit()">
      <h3>Your Skills</h3>
      
      <div formArrayName="skills">
        @for (skill of skillsArray.controls; track $index; let i = $index) {
          <div class="skill-item" [formGroupName]="i">
            <input 
              formControlName="name" 
              placeholder="Skill name"
              [class.error]="isSkillFieldInvalid(i, 'name')">
            
            <select formControlName="level">
              <option value="">Select level</option>
              <option value="beginner">Beginner</option>
              <option value="intermediate">Intermediate</option>
              <option value="advanced">Advanced</option>
              <option value="expert">Expert</option>
            </select>
            
            <button 
              type="button" 
              (click)="removeSkill(i)"
              [disabled]="skillsArray.length <= 1">
              Remove
            </button>
          </div>
        }
      </div>
      
      <button type="button" (click)="addSkill()">Add Skill</button>
      <button type="submit" [disabled]="skillsForm.invalid">Save Skills</button>
    </form>
  `,
  styles: [`
    .skill-item {
      display: flex;
      gap: 0.5rem;
      margin-bottom: 0.5rem;
      align-items: center;
    }
    
    .skill-item input, .skill-item select {
      flex: 1;
    }
  `]
})
export class SkillsFormComponent implements OnInit {
  skillsForm: FormGroup;

  constructor(private fb: FormBuilder) {
    this.skillsForm = this.fb.group({
      skills: this.fb.array([])
    });
  }

  ngOnInit() {
    // Start with one skill field
    this.addSkill();
  }

  get skillsArray(): FormArray {
    return this.skillsForm.get('skills') as FormArray;
  }

  createSkillGroup(): FormGroup {
    return this.fb.group({
      name: ['', [Validators.required, Validators.minLength(2)]],
      level: ['', Validators.required]
    });
  }

  addSkill() {
    this.skillsArray.push(this.createSkillGroup());
  }

  removeSkill(index: number) {
    if (this.skillsArray.length > 1) {
      this.skillsArray.removeAt(index);
    }
  }

  isSkillFieldInvalid(skillIndex: number, fieldName: string): boolean {
    const control = this.skillsArray.at(skillIndex).get(fieldName);
    return !!(control && control.invalid && control.touched);
  }

  onSubmit() {
    if (this.skillsForm.valid) {
      console.log('Skills submitted:', this.skillsForm.value);
    } else {
      // Mark all fields as touched to show validation errors
      this.skillsArray.controls.forEach(control => {
        control.markAllAsTouched();
      });
    }
  }
}
```

### Interview Preparation Tips by Company Tier

**Tier 1 (FAANG) - Focus Areas:**
- Advanced reactive patterns with RxJS
- Performance optimization strategies
- Complex validation scenarios
- Testing strategies for forms
- Accessibility considerations

**Tier 2 (Tech Companies) - Focus Areas:**
- Multi-step form wizards
- State management across components
- Custom validator implementation
- Error handling patterns
- Form caching strategies

**Tier 3 (Startups/Mid-size) - Focus Areas:**
- Basic form implementation
- Template-driven vs reactive choice
- Simple validation patterns
- FormArray for dynamic content

**Common Questions:**

1. **Basic Form Implementation**: Template-driven vs Reactive forms
2. **Form Validation**: Built-in validators and basic custom validators
3. **Form Arrays**: Adding/removing dynamic form elements

**Key Points to Mention in Interviews:**
- Always choose reactive forms for complex scenarios
- Use OnPush strategy for performance when possible
- Implement proper error handling with user-friendly messages
- Consider accessibility (a11y) requirements
- Use TypeScript interfaces for type safety
- Validate on both client and server side
- Provide progressive validation feedback to users

---

## Best Practices Summary

### Do's
- ✅ Use reactive forms for complex scenarios
- ✅ Implement proper error handling with user-friendly messages
- ✅ Use OnPush change detection strategy when possible
- ✅ Debounce expensive operations (API calls, complex validations)
- ✅ Use TypeScript interfaces for form data
- ✅ Implement accessibility features (ARIA labels, error announcements)
- ✅ Cache form data for better UX
- ✅ Use FormBuilder for cleaner code

### Don'ts
- ❌ Don't mix template-driven and reactive forms
- ❌ Don't forget to unsubscribe from observables
- ❌ Don't perform heavy operations in validators
- ❌ Don't ignore form state management in complex apps
- ❌ Don't skip validation on the backend
- ❌ Don't use nested FormGroups unnecessarily
- ❌ Don't forget about cross-field validation

---

## Summary

Forms and validation are fundamental to Angular applications and represent one of the highest-frequency interview topics. Key takeaways:

1. **Choose the right approach**: Reactive forms for complex scenarios, template-driven for simple ones
2. **Master validation**: Both built-in and custom validators, including async validation
3. **Optimize performance**: Use OnPush, debouncing, and smart caching strategies
4. **Handle edge cases**: Cross-field validation, dynamic forms, and error states
5. **Think about UX**: Progressive validation, clear error messages, and accessibility

Understanding these concepts thoroughly will prepare you for forms-related questions in interviews across all company tiers.
```
