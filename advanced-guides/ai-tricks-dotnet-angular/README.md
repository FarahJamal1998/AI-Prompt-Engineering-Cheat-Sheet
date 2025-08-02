# 🟦🔴 AI Tricks for .NET & Angular

*Your complete cheat sheet for AI-powered development in .NET and Angular*

---

## 🎯 Quick Start

This guide provides AI prompt patterns specifically designed for .NET and Angular development. Each pattern includes real prompts and sample completions.

## 📋 Table of Contents

- [🟦 .NET AI Patterns](#-net-ai-patterns)
- [🔴 Angular AI Patterns](#-angular-ai-patterns)
- [Advanced Patterns](#advanced-patterns)
- [Best Practices](#best-practices)

---

## 🟦 .NET AI Patterns

### Code Generation

#### Entity Framework Models
```csharp
// Prompt: "Create an Entity Framework model for a Product entity with relationships"
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal Price { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }
    public bool IsActive { get; set; }
    
    public ICollection<OrderItem> OrderItems { get; set; }
    public ICollection<ProductImage> Images { get; set; }
}
```

#### Repository Pattern
```csharp
// Prompt: "Create a generic repository interface and implementation"
public interface IRepository<T> where T : class
{
    Task<T> GetByIdAsync(int id);
    Task<IEnumerable<T>> GetAllAsync();
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
}

public class Repository<T> : IRepository<T> where T : class
{
    private readonly DbContext _context;
    private readonly DbSet<T> _dbSet;

    public Repository(DbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }

    public async Task<T> GetByIdAsync(int id)
    {
        return await _dbSet.FindAsync(id);
    }

    public async Task<IEnumerable<T>> GetAllAsync()
    {
        return await _dbSet.ToListAsync();
    }

    public async Task<T> AddAsync(T entity)
    {
        await _dbSet.AddAsync(entity);
        await _context.SaveChangesAsync();
        return entity;
    }

    public async Task UpdateAsync(T entity)
    {
        _dbSet.Update(entity);
        await _context.SaveChangesAsync();
    }

    public async Task DeleteAsync(int id)
    {
        var entity = await GetByIdAsync(id);
        if (entity != null)
        {
            _dbSet.Remove(entity);
            await _context.SaveChangesAsync();
        }
    }
}
```

#### API Controllers
```csharp
// Prompt: "Create a REST API controller for Product entity with CRUD operations"
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IRepository<Product> _productRepository;
    private readonly ILogger<ProductsController> _logger;

    public ProductsController(IRepository<Product> productRepository, ILogger<ProductsController> logger)
    {
        _productRepository = productRepository;
        _logger = logger;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<ProductDto>>> GetProducts()
    {
        try
        {
            var products = await _productRepository.GetAllAsync();
            var productDtos = products.Select(p => new ProductDto
            {
                Id = p.Id,
                Name = p.Name,
                Description = p.Description,
                Price = p.Price,
                CategoryId = p.CategoryId
            });
            return Ok(productDtos);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving products");
            return StatusCode(500, "Internal server error");
        }
    }

    [HttpPost]
    public async Task<ActionResult<ProductDto>> CreateProduct(CreateProductDto createProductDto)
    {
        if (!ModelState.IsValid)
            return BadRequest(ModelState);

        var product = new Product
        {
            Name = createProductDto.Name,
            Description = createProductDto.Description,
            Price = createProductDto.Price,
            CategoryId = createProductDto.CategoryId,
            CreatedAt = DateTime.UtcNow,
            IsActive = true
        };

        await _productRepository.AddAsync(product);

        var productDto = new ProductDto
        {
            Id = product.Id,
            Name = product.Name,
            Description = product.Description,
            Price = product.Price,
            CategoryId = product.CategoryId
        };

        return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, productDto);
    }
}
```

### EF Core Optimization

#### Optimized Queries
```csharp
// Prompt: "Optimize this Entity Framework query for performance"
// Before: Inefficient query
var products = await context.Products
    .Include(p => p.Category)
    .Include(p => p.OrderItems)
    .ThenInclude(oi => oi.Order)
    .ToListAsync();

// After: Optimized query
var products = await context.Products
    .AsNoTracking()
    .Include(p => p.Category)
    .Where(p => p.IsActive)
    .Select(p => new ProductDto
    {
        Id = p.Id,
        Name = p.Name,
        Description = p.Description,
        Price = p.Price,
        CategoryName = p.Category.Name,
        OrderCount = p.OrderItems.Count
    })
    .ToListAsync();
```

### DTO Mapping

#### AutoMapper Configuration
```csharp
// Prompt: "Create AutoMapper profiles for Product entity"
public class ProductProfile : Profile
{
    public ProductProfile()
    {
        CreateMap<Product, ProductDto>()
            .ForMember(dest => dest.CategoryName, opt => opt.MapFrom(src => src.Category.Name))
            .ForMember(dest => dest.OrderCount, opt => opt.MapFrom(src => src.OrderItems.Count));

        CreateMap<CreateProductDto, Product>()
            .ForMember(dest => dest.CreatedAt, opt => opt.MapFrom(src => DateTime.UtcNow))
            .ForMember(dest => dest.IsActive, opt => opt.MapFrom(src => true));

        CreateMap<UpdateProductDto, Product>()
            .ForMember(dest => dest.UpdatedAt, opt => opt.MapFrom(src => DateTime.UtcNow));
    }
}
```

### Validation

#### FluentValidation
```csharp
// Prompt: "Create FluentValidation rules for Product DTOs"
public class CreateProductDtoValidator : AbstractValidator<CreateProductDto>
{
    public CreateProductDtoValidator()
    {
        RuleFor(x => x.Name)
            .NotEmpty().WithMessage("Product name is required")
            .MaximumLength(100).WithMessage("Product name cannot exceed 100 characters");

        RuleFor(x => x.Description)
            .MaximumLength(500).WithMessage("Description cannot exceed 500 characters");

        RuleFor(x => x.Price)
            .GreaterThan(0).WithMessage("Price must be greater than 0")
            .LessThan(10000).WithMessage("Price cannot exceed 10,000");

        RuleFor(x => x.CategoryId)
            .GreaterThan(0).WithMessage("Valid category ID is required");
    }
}
```

---

## 🔴 Angular AI Patterns

### Component Generation

#### Smart Component
```typescript
// Prompt: "Create a smart Angular component for product management with TypeScript"
import { Component, OnInit, OnDestroy } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Subject, takeUntil } from 'rxjs';
import { ProductService } from '../../services/product.service';
import { Product } from '../../models/product.model';

@Component({
  selector: 'app-product-management',
  templateUrl: './product-management.component.html',
  styleUrls: ['./product-management.component.scss']
})
export class ProductManagementComponent implements OnInit, OnDestroy {
  products: Product[] = [];
  productForm: FormGroup;
  isLoading = false;
  errorMessage = '';
  private destroy$ = new Subject<void>();

  constructor(
    private productService: ProductService,
    private fb: FormBuilder
  ) {
    this.initForm();
  }

  ngOnInit(): void {
    this.loadProducts();
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  private initForm(): void {
    this.productForm = this.fb.group({
      name: ['', [Validators.required, Validators.maxLength(100)]],
      description: ['', [Validators.maxLength(500)]],
      price: ['', [Validators.required, Validators.min(0), Validators.max(10000)]],
      categoryId: ['', [Validators.required]]
    });
  }

  private loadProducts(): void {
    this.isLoading = true;
    this.productService.getProducts()
      .pipe(takeUntil(this.destroy$))
      .subscribe({
        next: (products) => {
          this.products = products;
          this.isLoading = false;
        },
        error: (error) => {
          this.errorMessage = 'Failed to load products';
          this.isLoading = false;
          console.error('Error loading products:', error);
        }
      });
  }

  onSubmit(): void {
    if (this.productForm.valid) {
      this.isLoading = true;
      const product: Partial<Product> = this.productForm.value;
      
      this.productService.createProduct(product)
        .pipe(takeUntil(this.destroy$))
        .subscribe({
          next: (newProduct) => {
            this.products.push(newProduct);
            this.productForm.reset();
            this.isLoading = false;
          },
          error: (error) => {
            this.errorMessage = 'Failed to create product';
            this.isLoading = false;
            console.error('Error creating product:', error);
          }
        });
    }
  }
}
```

### Reactive Forms

#### Complex Form with Validation
```typescript
// Prompt: "Create a complex reactive form with custom validators"
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, FormArray, Validators, AbstractControl } from '@angular/forms';

@Component({
  selector: 'app-complex-form',
  templateUrl: './complex-form.component.html'
})
export class ComplexFormComponent implements OnInit {
  userForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit(): void {
    this.initForm();
  }

  private initForm(): void {
    this.userForm = this.fb.group({
      personalInfo: this.fb.group({
        firstName: ['', [Validators.required, Validators.minLength(2)]],
        lastName: ['', [Validators.required, Validators.minLength(2)]],
        email: ['', [Validators.required, Validators.email]],
        phone: ['', [Validators.pattern(/^\+?[\d\s-()]+$/)]],
        dateOfBirth: ['', [Validators.required, this.ageValidator(18)]]
      }),
      address: this.fb.group({
        street: ['', Validators.required],
        city: ['', Validators.required],
        state: ['', Validators.required],
        zipCode: ['', [Validators.required, Validators.pattern(/^\d{5}(-\d{4})?$/)]]
      }),
      preferences: this.fb.group({
        newsletter: [false],
        notifications: [true],
        theme: ['light', Validators.required]
      }),
      skills: this.fb.array([])
    });
  }

  get skillsArray(): FormArray {
    return this.userForm.get('skills') as FormArray;
  }

  addSkill(): void {
    const skillGroup = this.fb.group({
      name: ['', Validators.required],
      level: ['beginner', Validators.required],
      yearsOfExperience: [0, [Validators.min(0), Validators.max(50)]]
    });
    this.skillsArray.push(skillGroup);
  }

  removeSkill(index: number): void {
    this.skillsArray.removeAt(index);
  }

  private ageValidator(minAge: number) {
    return (control: AbstractControl): { [key: string]: any } | null => {
      if (!control.value) return null;
      
      const birthDate = new Date(control.value);
      const today = new Date();
      const age = today.getFullYear() - birthDate.getFullYear();
      const monthDiff = today.getMonth() - birthDate.getMonth();
      
      if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
      }
      
      return age >= minAge ? null : { 'minAge': { required: minAge, actual: age } };
    };
  }

  onSubmit(): void {
    if (this.userForm.valid) {
      console.log('Form submitted:', this.userForm.value);
    } else {
      this.markFormGroupTouched(this.userForm);
    }
  }

  private markFormGroupTouched(formGroup: FormGroup): void {
    Object.keys(formGroup.controls).forEach(key => {
      const control = formGroup.get(key);
      if (control instanceof FormGroup) {
        this.markFormGroupTouched(control);
      } else {
        control?.markAsTouched();
      }
    });
  }
}
```

### State Management

#### NgRx Store
```typescript
// Prompt: "Create NgRx store for product management"
// Actions
import { createAction, props } from '@ngrx/store';
import { Product } from '../models/product.model';

export const loadProducts = createAction('[Product] Load Products');
export const loadProductsSuccess = createAction(
  '[Product] Load Products Success',
  props<{ products: Product[] }>()
);
export const loadProductsFailure = createAction(
  '[Product] Load Products Failure',
  props<{ error: string }>()
);

export const createProduct = createAction(
  '[Product] Create Product',
  props<{ product: Partial<Product> }>()
);
export const createProductSuccess = createAction(
  '[Product] Create Product Success',
  props<{ product: Product }>()
);
export const createProductFailure = createAction(
  '[Product] Create Product Failure',
  props<{ error: string }>()
);

// State
export interface ProductState {
  products: Product[];
  loading: boolean;
  error: string | null;
}

export const initialState: ProductState = {
  products: [],
  loading: false,
  error: null
};

// Reducer
import { createReducer, on } from '@ngrx/store';
import * as ProductActions from './product.actions';

export const productReducer = createReducer(
  initialState,
  on(ProductActions.loadProducts, state => ({
    ...state,
    loading: true,
    error: null
  })),
  on(ProductActions.loadProductsSuccess, (state, { products }) => ({
    ...state,
    products,
    loading: false
  })),
  on(ProductActions.loadProductsFailure, (state, { error }) => ({
    ...state,
    error,
    loading: false
  })),
  on(ProductActions.createProductSuccess, (state, { product }) => ({
    ...state,
    products: [...state.products, product],
    loading: false
  }))
);
```

### Services

#### HTTP Service with Interceptors
```typescript
// Prompt: "Create a comprehensive HTTP service with interceptors"
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders, HttpParams, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError, BehaviorSubject } from 'rxjs';
import { catchError, retry, tap } from 'rxjs/operators';
import { environment } from '../../environments/environment';

export interface ApiResponse<T> {
  data: T;
  message: string;
  success: boolean;
}

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private baseUrl = environment.apiUrl;
  private loadingSubject = new BehaviorSubject<boolean>(false);
  public loading$ = this.loadingSubject.asObservable();

  constructor(private http: HttpClient) {}

  private createHeaders(): HttpHeaders {
    const token = localStorage.getItem('auth_token');
    return new HttpHeaders({
      'Content-Type': 'application/json',
      ...(token && { Authorization: `Bearer ${token}` })
    });
  }

  private handleError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'An error occurred';
    
    if (error.error instanceof ErrorEvent) {
      errorMessage = `Error: ${error.error.message}`;
    } else {
      errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }
    
    console.error('API Error:', error);
    return throwError(() => new Error(errorMessage));
  }

  get<T>(endpoint: string, params?: any): Observable<T> {
    const url = `${this.baseUrl}${endpoint}`;
    const httpParams = new HttpParams({ fromObject: params || {} });
    
    this.loadingSubject.next(true);
    
    return this.http.get<T>(url, { 
      headers: this.createHeaders(),
      params: httpParams 
    }).pipe(
      retry(1),
      tap(() => this.loadingSubject.next(false)),
      catchError(this.handleError)
    );
  }

  post<T>(endpoint: string, data: any): Observable<T> {
    const url = `${this.baseUrl}${endpoint}`;
    
    this.loadingSubject.next(true);
    
    return this.http.post<T>(url, data, { 
      headers: this.createHeaders() 
    }).pipe(
      tap(() => this.loadingSubject.next(false)),
      catchError(this.handleError)
    );
  }

  put<T>(endpoint: string, data: any): Observable<T> {
    const url = `${this.baseUrl}${endpoint}`;
    
    this.loadingSubject.next(true);
    
    return this.http.put<T>(url, data, { 
      headers: this.createHeaders() 
    }).pipe(
      tap(() => this.loadingSubject.next(false)),
      catchError(this.handleError)
    );
  }

  delete<T>(endpoint: string): Observable<T> {
    const url = `${this.baseUrl}${endpoint}`;
    
    this.loadingSubject.next(true);
    
    return this.http.delete<T>(url, { 
      headers: this.createHeaders() 
    }).pipe(
      tap(() => this.loadingSubject.next(false)),
      catchError(this.handleError)
    );
  }
}
```

---

## 🚀 Advanced Patterns

### Custom Decorators
```typescript
// Prompt: "Create custom decorators for Angular components"
export function LogMethod() {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    
    descriptor.value = function (...args: any[]) {
      console.log(`Calling ${propertyKey} with:`, args);
      const result = originalMethod.apply(this, args);
      console.log(`${propertyKey} returned:`, result);
      return result;
    };
    
    return descriptor;
  };
}

export function Debounce(delay: number) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    let timeoutId: any;
    
    descriptor.value = function (...args: any[]) {
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        originalMethod.apply(this, args);
      }, delay);
    };
    
    return descriptor;
  };
}
```

### Custom Pipes
```typescript
// Prompt: "Create custom pipes for data transformation"
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'formatCurrency'
})
export class FormatCurrencyPipe implements PipeTransform {
  transform(value: number, currency: string = 'USD', locale: string = 'en-US'): string {
    return new Intl.NumberFormat(locale, {
      style: 'currency',
      currency: currency
    }).format(value);
  }
}

@Pipe({
  name: 'timeAgo'
})
export class TimeAgoPipe implements PipeTransform {
  transform(value: Date | string): string {
    const date = new Date(value);
    const now = new Date();
    const diffInSeconds = Math.floor((now.getTime() - date.getTime()) / 1000);
    
    if (diffInSeconds < 60) {
      return 'just now';
    } else if (diffInSeconds < 3600) {
      const minutes = Math.floor(diffInSeconds / 60);
      return `${minutes} minute${minutes > 1 ? 's' : ''} ago`;
    } else if (diffInSeconds < 86400) {
      const hours = Math.floor(diffInSeconds / 3600);
      return `${hours} hour${hours > 1 ? 's' : ''} ago`;
    } else {
      const days = Math.floor(diffInSeconds / 86400);
      return `${days} day${days > 1 ? 's' : ''} ago`;
    }
  }
}
```

---

## 💡 Best Practices

### 1. Error Handling
```typescript
// Prompt: "Create comprehensive error handling patterns"
export class ErrorHandlerService {
  handleError(error: any): void {
    if (error instanceof HttpErrorResponse) {
      this.handleHttpError(error);
    } else {
      this.handleGenericError(error);
    }
  }

  private handleHttpError(error: HttpErrorResponse): void {
    switch (error.status) {
      case 400:
        console.error('Bad Request:', error.error);
        break;
      case 401:
        this.authService.logout();
        break;
      case 403:
        console.error('Forbidden:', error.error);
        break;
      case 404:
        console.error('Not Found:', error.error);
        break;
      case 500:
        console.error('Server Error:', error.error);
        break;
      default:
        console.error('Unknown Error:', error);
    }
  }

  private handleGenericError(error: any): void {
    console.error('Generic Error:', error);
  }
}
```

### 2. Performance Optimization
```typescript
// Prompt: "Create performance optimization patterns"
export class PerformanceService {
  private cache = new Map<string, any>();

  memoize<T>(key: string, fn: () => T): T {
    if (this.cache.has(key)) {
      return this.cache.get(key);
    }
    
    const result = fn();
    this.cache.set(key, result);
    return result;
  }

  debounce<T extends (...args: any[]) => any>(fn: T, delay: number): T {
    let timeoutId: any;
    return ((...args: any[]) => {
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => fn(...args), delay);
    }) as T;
  }

  throttle<T extends (...args: any[]) => any>(fn: T, delay: number): T {
    let lastCall = 0;
    return ((...args: any[]) => {
      const now = Date.now();
      if (now - lastCall >= delay) {
        lastCall = now;
        fn(...args);
      }
    }) as T;
  }
}
```

---

## 📚 Additional Resources

- [.NET Documentation](https://docs.microsoft.com/en-us/dotnet/)
- [Angular Documentation](https://angular.io/docs)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [NgRx Documentation](https://ngrx.io/docs)
- [Angular Material](https://material.angular.io/)

---

*Master AI-powered development in .NET and Angular! 🚀*

*Created by Farah Jamal - .NET & Angular AI Expert* 