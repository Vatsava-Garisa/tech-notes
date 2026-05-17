
# Hooks
## 1. `constructor()` 
- Standard JS class constructor
- Not an Angular lifecycle hook technically

**Why** 
- Dependency Injection happens here

```
constructor(
	private userService: UserService,
	private productService: ProductService
) {
	console.log(`constructor calles...`)
}
```

**Use** 
- Inject services
- Initialize basic class variables (not logic depending on inputs)

**NOTE** 
- Avoid HTTP calls
- Avoid logic dependent on `@Input()`

## 2. `ngOnChanges()` 
- Triggered every time an `@Input()` property changes.

**Why** 
- React to external data updates

```
@Input() userId!: number;

ngOnChanges(changes: SimpleChanges) {
	if (changes['userId']) {
		console.log(`User changed: changes['userId'].currentValue`);
	}
}
```

**Use** 
- Reload data when parent changes input
- Recalculate derived values
- Parent passes `productId`, Child fetches product details on change

## 3. `ngOnInit()` 
- Called once after first `ngOnChanges()`
- This is the mot commonly used hook

**Why** 
- Component is fully initialized
- Inputs are available

```
ngOnInit() {
	this.loadUserData();
}
```

**Use** 
- API calls
- Initial data setup
- Form initialization

## 4. `ngDoCheck()` 
- Custom change detection hook

**Why** 
- Angular's default change detection might not catch deep mutations

```
ngDoCheck() {
	console.log(`Change detection rnning...`);
}
```

**Use** 
- Detect changes in mutable objects (arrays, nested objects)

**NOTE** 
- Runs VERY frequently → performance sensitive
- Avoid unless necessary

## 5. `ngAfterContentInit()` 
- Called after `ng-content` projection is initialized

 **Why** 
- Access projected content

```
ngAfterContentInit() {
  console.log('Content initialized');
}
```

**Use** 
- Custom UI components (like reusable wrappers)
- Access projected child content

## 6. `ngAfterContentChecked()` 
- Runs after every check of projected content

**Why** 
- Track updates in projected content

```
ngAfterContentChecked() {
	console.log(`Content checked`)
}
```

**Use** 
- Advanced component libraries

## 7. `ngAfterViewInit()` 
- Called after component's view (DOM) is initialized

**Why** 
- Safe access to DOM and `@ViewChild`

```
@ViewChild('inputRef') input!: ElementRef;

ngAfterViewInit() {
	this.input.nativeElement.focus();
}
```

**Use** 
- DOM manipulation
- Integration third-party libraries (charts, maps)

## 8. `ngAfterViewChecked()` 
- Called after view check

**Why** 
- React to DOM updates

```
ngAfterViewChecked() {
  console.log('View checked');
}
```

**Use**
- Debugging rendering behavior

**NOTE** 
- Performance heavy → avoid business logic

## 9. `ngOnDestroy()` 
- Called before component is destroyed
- Critical for memory leak prevention

**Why** 
- Cleanup resources

```
subscription!: Subscription;

ngOnInit() {
  this.subscription = this.service.getData().subscribe();
}

ngOnDestroy() {
  this.subscription.unsubscribe();
}
```

**Use** 
- Unsubscribe Observables
- Remove event listeners
- Clear intervals/timeouts

---
# Observations 
## Hooks are tied to Change Detection 
- Every hook (except constructor & destroy) is triggered by Angular's change detection cycle
## Frequency Matters 
- Misusing frequent hooks = performance issues

|Hook|Frequency|
|---|---|
|ngOnInit|Once|
|ngOnChanges|On input change|
|ngDoCheck|Very frequent|
|ngAfterViewChecked|Very frequent|
## Safe Zones 

|Task|Correct Hook|
|---|---|
|API call|ngOnInit|
|React to input change|ngOnChanges|
|DOM access|ngAfterViewInit|
|Cleanup|ngOnDestroy|

---
# NOTE 
In Angular 16+ (and continuing into Angular 18+):
- Signals and new APIs are reducing reliance on class hooks
- Alternatives included:
	- `effect()`
	- `afterRender()`
	- `DestroyRef`
However, lifecycle hooks are still fundamental and widely used, especially in enterprise codebases.

