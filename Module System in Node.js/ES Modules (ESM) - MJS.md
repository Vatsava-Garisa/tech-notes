
## Notes
- ES Modules are the official JS module system, defined by the ECMA Script specification. (not by Node.js)
- Compile time loading.
- Asynchronous.
- Static.
- `import` is a language keyword.
- ESM has no implicit wrapper function. No `__dirname` / `__filename`
```
import { fileURLToPath } from 'url';
import path from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
```
- ESM must know all dependencies before execution.
- Advantages:
	1. Tree-shaking.
		- Unused code can be removed.
	2. Better tooling.
		- IDE autocomplete.
		- Dead code detection.
		- Smarter and faster builds.
	3. Async loading.
		- Works in browsers.
		- Works in Node.
	4. Deterministic execution order.
- Node ESM requires explicit file extensions.
```
import foo from "./foo"; // ❎

import foo from "./foo.js"; // ✅
```
- Execution/Processing steps:
	1.  Linking phase
		- Resolve all imports.
		- Load them in parallel.
		- Create module graph.
		- No code runs yet.
    2. Evaluation phase
	    - Executes modules topologically.
	    - Exactly once.
- In ESM, the exports are live-bindings.
```
└── 📁js
    ├── index.js
    └── utils.js


index.js
--------
import count from "./utils.js";

console.log(count); // 10


utils.js
--------
export let count = 10;

count++;
console.log(`Value of count in utils.js: ${count}`); // 11


/*
	Importers see updates automatically.
	Before any code runs, JavaScript already knows the file index.js depends on utils.js.
	Dependency graph is complete upfront.
*/
```
- This is why **circular dependencies behave better** in ESM. 
	- All modules are linked first.
	- Exports are **live bindings**, not snapshots.
- ESM describes the module graph first, then runs code.  
- CommonJS runs code to discover the module graph.

## Multiple imports example
- Default.
```
└── 📁js
    ├── index.mjs
    └── utils.mjs
    
index.mjs
---------
import { greetHello } from "./utils.mjs";

console.log(greetHello);
greetHello("Sree");


utils.mjs
---------
export function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}
```
---
## Single import example
- If there is only one thing we want to import from the file we can follow this syntax.
```
└── 📁js
    ├── index.mjs
    └── utils.mjs


index.mjs
---------
import greetHello from "./utils.mjs";

console.log(greetHello);
greetHello("Sree");


utils.mjs
---------
function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}

export default greetHello;
```
---
## Miscellaneous - 1
- Custom Named Imports.
```
└── 📁js
    ├── index.mjs
    └── utils.mjs


index.mjs
---------
import { greetHello as customName } from "./utils.mjs";

console.log(customName);
customName("Sree");


utils.mjs
---------
export function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}
```
---
## Miscellaneous - 2
- Custom Named Imports.
```
└── 📁js
    ├── index.mjs
    └── utils.mjs


index.mjs
---------
import customName from "./utils.mjs";

console.log(customName);
customName("Sree");


utils.mjs
---------
function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}

export default greetHello;
```
---
## Importing CommonJS from ESM
- Node treats `module.exports` as the default export.
- Named imports usually fail.
```
import pkg from 'express'; // ✅

import { Router } from 'express'; // often ❎
```
---
## Importing ESM from CommonJS (restricted)
- ESM loading is **async**
- `require()` is **sync**.
```
const x = require('./esm-file.js'); // doesn't work ❎

// works ✅
(async () => {
  const x = await import('./esm-file.js');
})();
```
---
