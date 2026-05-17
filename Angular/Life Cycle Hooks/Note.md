
Angular Component:
1. Gets created
2. Received inputs
3. Gets rendered
4. Updates when data changes
5. Eventually gets destroyed

Angular internally orchestrates all of this through its change detection mechanism.

**Lifecycle hooks are interception points that allow you to inject logic at specific stages of this lifecycle.**

Without hooks:
- You wouldn't know when the inputs are ready.
- You couldn't safely access the DOM
- You'd leak memory
- You'd struggle with async coordination

**What?** 
----------
**Lifecycle hooks are predefined callback methods that Angular calls at specific moments in a component or directive's lifecycle.**

They fall into 3 conceptual phases:

|Phase|Purpose|
|---|---|
|Initialization|Setup logic when component is created|
|Change Detection|React to data/input changes|
|Rendering / DOM|Work with the rendered view|
|Destruction|Cleanup resources|

**Complete Lifecycle Flow** 
----------
1. `constructor()`
2. `ngOnChanges()` (if input exists)
3. `ngOnInit()`
4. `ngDoCheck()`
5. `ngAfterContentInit()`
6. `ngAfterContentChecked()`
7. `ngAfterViewInit()`
8. `ngAfterViewChecked()`
9. Repeats: `ngOnChanges` → `ngDoCheck` → `ngAfterContentChecked` → `ngAfterViewChecked`
10. `ngOnDestroy()`
