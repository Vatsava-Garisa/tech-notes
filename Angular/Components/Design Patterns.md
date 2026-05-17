
- In large-scale Angular applications, components are not just UI pieces, they are architectural units.
- Poor component design leads to tight coupling, unreadable code, and performance issues.
- Good design patterns enforce separation of concerns, reusability, an scalability.

## 1. Smart vs Dumb Components
**Split components based on responsibility.**

| Type                      | Responsibility                 |
| ------------------------- | ------------------------------ |
| **Smart (Container)**     | Handles data, API calls, state |
| **Dumb (Presentational)** | Only displays UI, emits events |
**Why** 
- Improves testability
- Enable reuse
- Keeps UI components simple

```
// Smart Component

@Component({
	selector: 'app-products',
	template: `
		<app-product-list 
			[products]='products' 
			(addToCart)='handleAdd($event)'>
		</app-product-list>
	`
})
export class ProductsComponent {
	products = [];
	
	ngOnInit() {
		// API Call
	}
	
	handleAdd(product: any) {
		// business logic
	}
}

// Dumb Component
@Component({
	selector: 'app-product-list',
	template: `
		<div *ngFor="let product of products">  
			<button (click)="add.emit(product)">Add</button>  
		</div>
	`
})
export class ProductListComponent{
	@Input() products: any[] = [];
	@Output() addToCart = new EventEmitter();
}
```
## 2. Presentational + State Management Pattern
**Move all state logic out of components into a centralized store.**

```
Component → Dispatch Action → Store → Reducer → New State → UI updates
```

**Why** 
- Predictable state
- Easier debugging
- Scales for large teams
## 3. Feature-Based Component Organization 
**Organize components by feature, not by type.**

**Why** 
- Better code locality
- Easier onboarding
- Scales with domain complexity

```
❌ Bad
components/
services/
models/

✅ Good
products/
	product-list/
	product-card/
	product.service.ts

users/
	user-profile/
```
## 4. Shared vs Core Components Pattern
**Core Module Concept** 
- Singleton services
- App-wide components (Navbar, Auth)
**Shared Module Concept** 
- Reusable UI components
- Pipes, directives

**Why** 
- Prevents duplication
- Keeps architecture clean

```
core/
	auth.service.ts
	navbar.component.ts
	
shared/
	button.component.ts
	card.component.ts
```
## 5. Compound Component Pattern 
**Multiple components work together as a single logical unit.**

**Why** 
- Clean API
- Flexible composition

```
<app-tabs>
	<app-tab title="Home"></app-tab>
	<app-tab title="Profile"></app-tab>
</app-tabs>
```
## 6. Higher-Order Component Pattern 
Angular doesn't have true HOCs like React, but we simulate using:
- Directives
- Wrapper components
- Services

**Why** 
- Reuse logic without duplication
- Keeps components clean

```
<app-loader [loading]="isLoading">
	<app-product-list></app-product-list>
</app-loader>
```
## 7. Renderless Component Pattern 
**Component contains logic only, no UI.**

**Why** 
- Maximum reuse of logic
- Clean separation

```
@Component({
	selector: 'app-auth-logic',
	template: `<ng-content></ng-content>`
})
export class AuthLogicComponent {
	isLoggedIn: boolean = true;
}

<app-auth-logic>
	<app-dashboard><app-dashboard>
</app-auth-logic>
```
## 8. Dynamic Component Pattern 
**Load components dynamically at runtime.**

**Use** 
- Modals
- Plugins
- CMS-driven UI

```
this.viewContainerRef.createComponent(MyComponent);
```
## 9. Facade Pattern 
**Introduce a facade layer between components and state/services.**

**Why** 
- Decouples UI from implementation
- Easier refactoring
- Cleaner components

```
constructor( private facade: ProductFacade ){}

product$ = this.facade.product$;

ngOnInit() {
	this.facade.loadProducts();
}
```
## 10. Micro Frontend Component Pattern 
**Break app into independent modules.**

**Why** 
- Independent deployments
- Team scalability

## 11. Input/Output + Immutable Data Pattern 
**Always push immutable data to components.**

**Why** 
- Works better with `OnPush`
- Avoids side effects

```
this.products = [...this.products, newProduct];
```



