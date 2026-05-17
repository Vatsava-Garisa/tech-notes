
# Core Idea 
Think of an Angular application like a Lego structure
- Each Lego block = a component
- You build small, independent blocks
- Then assemble them into a complete application

**A component in Angular is the fundamental building block of the UI. Every visible part of your app - buttons, forms, headers, lists - is created using components.**

---
# What?
A component is a self-contained unit that controls:
1. HTML (Template) → What the user sees
2. TypeScript (Class/Logic) → How it behaves
3. CSS (Styles) → How it looks

**A component is a class decorated with `@Component` that defines a view and its behavior.**

- `selector` → Custom HTML tag. Defines how component is used in HTML.
- `templateUrl` → Defines UI. Inline template or external HTML file
- `styleUrls` → CSS
- `class` → Logic & data

```
import { Component } from '@angular/core';

@Component({
	selector: 'app-user',
	templateUrl: './user.component.html',
	styleUrls: ['./user.component.css']
})
export class UserComponent {
	name: string = "";
}
```

- Angular starts with a root component (`AppComponent`)
- It renders inside `index.html`
- Everything in the app is nested inside this root component

```
AppComponent (root)
 ├── HeaderComponent
 ├── SidebarComponent
 └── ProductListComponent
       ├── ProductCardComponent
       ├── ProductCardComponent
```

**NOTE** 
- Newer Angular versions allow components without modules.
- This simplifies architecture and reduces boilerplate

---
# Why?
### Modularity 
Instead of one huge file, split UI into small pieces
- Example:
	- Header Component
	- Sidebar Component
	- Product Card Component
### Reusability 
Write once, use anywhere.
```
<app-user></app-user>
```
### Maintainability 
Fix or update one component without breaking others.
### Separation of Concerns
- UI → Template
- Logic → TS
- Styling → CSS
### Scalability 
Large application (like dashboards, e-commerce) become manageable.
### Encapsulation
Each component has its own styles. Prevents CSS conflicts.

---
# Communication 
### Parent → Child
- Using `@Input()`

```
@Input() title: string;
```

### Child → Parent 
- Using `@output()`

```
@Output() clicked = new EventEmitter();
```


