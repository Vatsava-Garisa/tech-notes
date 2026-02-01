
## Notes
- CommonJS or CJS is the original module system for the Node.js.
- Runtime loading.
- Synchronous.
- Dynamic.
- Every `.js` file in Node.js is wrapped like:
```
(function (exports, require, module, __filename, __dirname) {
	// Code here...
});

/*
	require: Function to load modules.
	exports: What the module exposes.
	__filename and __dirname.
*/
```
- Path resolution:
```
const foo = require("./foo");

/*
	Order:
		1. ./foo.js
		2. ./foo.json
		3. ./foo.node   (native addon)
		4. ./foo/package.json → main field
		5. ./foo/index.js
		6. ./foo/index.json
		7. ./foo/index.node
*/
```
- Execution/Processing steps:
	1. Resolves Path.
	2. Load file from disk.
	3. Execute it once.
	4. Cache the result. Stored in `required.cache`.
	5. Return `module.exports`.
- Supports dynamic imports.
```
if (env === 'prod') {
  require('./prod');
} else {
  require('./dev');
}


/*
	Node must execute code to discover dependencies.
	Tools must run your code to know what you depend on.
*/
```
- Resolves imports in the runtime.
- Stores `module.exports`, keyed by resolved filename. Variables just receive references to that cached object.
```
const foo = require("./foo.js");


/* 
	The variable `foo` holds a reference to the cached object.  
	If one importer mutates that object, the change is visible in every other file that imports it, because they all share the same reference.
*/
```
- In CommonJS, the exports are snapshots.
```
└── 📁js
    ├── index.js
    └── utils.js


index.js
--------
const { count } = require("./utils.js");

console.log(count); // 10


utils.js
--------
let count = 10;

module.exports.count = count;

count++;
console.log(`Value of count in utils.js: ${count}`); // 11
```
- Circular dependency.
```
└── 📁js
    ├── a.js
    └── b.js


a.js
----
const b = require('./b');

exports.a = 'A';

b.js
----
const a = require('./a');

exports.b = 'B';


/*
	Node returns partial evaluated exports.
	No deadlock.
	But values maybe "undefined" if accessed too early.
*/
```

## Multiple imports example
### Option - 1:
- The exported identifier must exactly match the function name defined in `utils.js`.
- Every import across all files should reference that same identifier consistently.
- If the function name changes, all corresponding import statements must be updated (most code editors support automatic refactoring for this).
- Once you overwrite `module.exports`, `exports.` is no longer linked.
```
└── 📁js
    ├── index.js
    └── utils.js


index.js
--------
const { greetHello } = require("./utils");
(or)
const { greetHello } = require("./utils.js");

console.log(greetHello);
greetHello("Sree");


utils.js
--------
function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}

module.exports = { greetHello };

(or)

utils.js
--------
exports.greetHello = function(name) {
    console.log(`Hello ${name}, howdy`);
};

(or)

utils.js
--------
function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}

exports.greetHello = greetHello;
```
---
### Option - 2:
- The exported name does not have to match the actual function name in `utils.js`.
- When `index.js` and `utils.js` are owned by different developers, the `index.js` maintainer doesn’t need to track internal function name or version changes in `utils.js`.
- Consumers only import the exposed export name, while the `utils.js` maintainer handles any underlying changes.
```
└── 📁js
    ├── index.js
    └── utils.js


index.js
--------
const { greetHello } = require("./utils");
(or)
const { greetHello } = require("./utils.js");

console.log(greetHello);
greetHello("Sree");


utils.js
--------
function greetHello_V1(name) {
    console.log(`Hello ${name}, howdy`);
}
function greetHello_V2(name) {
    console.log(`Hello ${name}, how are you doing`);
}

module.exports = {
    "greetHello": greetHello_V1
};
```

## Single import example
- If there is only one thing we want to import from the file we can follow this syntax.
```
└── 📁js
    ├── index.js
    └── utils.js


index.js
--------
const greetHello = require("./utils");
(or)
const greetHello = require("./utils.js");

console.log(greetHello);
greetHello("Sree");


utils.js
--------
function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}

module.exports = greetHello;
```
---
## Miscellaneous - 1
```
└── 📁js
    ├── index.js
    └── utils.js


index.js
-------
const greetHello = require("./utils");
(or)
const greetHello = require("./utils.js");

greetHello("Sree");
greetHello.greetBye("Sree");


utils.js
--------
function greetHello(name) {
    console.log(`Hello ${name}, howdy`);
}

function greetBye(name) {
    console.log(`Bye ${name}`);
}

module.exports = greetHello;
module.exports.greetBye = greetBye;
```
---
## Miscellaneous - 2
- Node.js automatically resolves the import path `./utils` to either `./utils.js` or `./utils/index.js`, depending on which file it encounters first during module resolution.
```
└── 📁js
    └── 📁utils
        ├── index.js
    └── index.js
      

index.js
--------
const { greetHello } = require("./utils");

console.log(greetHello);
greetHello("Sree");

utils/index.js
--------------
function greetHello(name) {
    console.log(`Hello ${name}, utils/index.js`);
} 

module.exports = {
    greetHello
};
```
---
