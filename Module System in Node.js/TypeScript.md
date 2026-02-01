s
- TypeScript does not have its own module system.
- TypeScript's Job:
	- *Translate my TypeScript `import/export` into the correct JavaScript syntax for the runtime.*
- TS always uses ESM syntax in source code.
```
import fs from "fs";

export function foo() { };
```
- It can emit either **CommonJS** or **ESM** output based on `tsconfig.json`.
	- `module`: Controls what JS syntax is emitted.
	- `moduleResolution`: Controls how imports are resolved during type-checking.

---
### `module`
- How output JavaScript looks.
```
TS:
---
import fs from "fs";


/* "module": "CommonJS" */
const fs = require("fs");


/* "module": "ES2022" */
import fs from "fs";
```
==**NOTE:**==
- If Node treats file as:
	- ESM -> `module` must be `ES2022`/`ESNext`.
	- CommonJS -> `module` must be `CommonJS`.
- Mismatch causes runtime crash.
---
### `moduleResolution`
- Tells TS how to _pretend to be Node.js_ when it tries to find imported files during type-checking.
	- Can TypeScript find this module?
	- What type definitions belong to it?
- This does not affect emitted JS.
```
import { greetHello } from "./utils";

/*
	./utils.ts ?
	./utils/index.ts ?
	./utils.js ?
*/
```
- **Old behavior:** `"moduleResolution": "Node"`
	- Matches CommonJS style resolution.
	- Legacy.
	- Assumes extension-less imports are OK.
	- Does not enforce ESM rules.
	- May allow code that Node ESM will reject.
	- This causes, *Works in TS, crashes in Node*.
- **New behavior:** `"moduleResolution": "NodeNext"`
	- Enforces Node ESM rules.
	- Requires explicit extensions.
	- Understands:
		- `"type": "module"`
		- `.cjs` an `.mjs`
```
└── 📁ts
    ├── index.ts
    └── utils.ts

index.ts
--------
import "./utils"; // ❎

index.ts
--------
import "./utils.js"; // ✅
```
==**NOTE:**==
- **Node 18+ best practice**: `"moduleResolution": "NodeNext"`.
- Without it:
	- TS may say ✅.
	- Node may crash ❎.
- Because:
	- TS thinks a file is CommonJS.
	- Node treats it as ESM (or vice versa).
- `NodeNext` aligns TS with Node's rules.
- **TS resolves imports as if it were Node running the output JS, not the TS source.**
---
### `esModuleInterop`
#### Context:
- Most popular Node libraries are CommonJS. (express, lodash, etc.)
```
module.expots = function express() {...};
```
- But in TS/ESM you want to write:
```
import express from "express";
```
- This only works if `express` is ESM and exports like:
```
export default express;
```
- So TypeScript provides compatibility helpers.
#### Info:
- When `"esModuleInterop": true`:
	- TS treats `module.exports` as a default export.
	- Emits compatibility code for CommonJS.
---
### `allowSyntheticDefaultImports`
- This is type-checking only.
```
// allows this
import x from "cjs-lib";
```
- Does not change emitted JS.
- Can still break at runtime if misused.
- Prefer `esModuleInterop`.
- Avoid relying on this alone.
---
### ==**Recommended Node 18+ `tsconfig.json`**==
```
tsconfig.json
-------------
{
	"compilerOptions": {
		"target": "ES2022",
		"module": "ES2022",
		"moduleResolution": "NodeNext",
		"strict": true,
		"esModuleInterop": true,
		"forceConsistentCasingInFileNames": true,
		"skipLibCheck": true
	}
}

package.json
------------
{
	"type": "module"
}
```
---
### ==**Common Mistakes**==
- ❌ Mixing `require` and `import` in same file.
- ❌ Using `"module": "CommonJS"` with `"type": "module"`.
- ❌ Omitting `.js` extension in ESM TS.
- ❌ Using `Node` resolution in modern Node.
- ❌ Relying on `allowSyntheticDefaultImports` alone.

