# **Assumption:**
- Node.js app or library.
- TypeScript source.
- We want one bundled JS output.

```
└── 📁src
	├── index.ts
└── webpack.config.js


webpack.config.js
-----------------
const path = require("path");

module.exports = {
	mode: "production",
	
	target: "node",
	
	entry: "./src/index.ts",
	
	output: {
		path: path.resolve(__dirname, "dist"),
		filename: "bundle.js",
		clean: true
	},
	
	resolve: {
		extensions: [".ts", ".js"]
	},
	
	module: {
		rules: [
			{
				test: /\.ts$/,
				use: "ts-loader",
				exclude: /node_modules/
			}
		]
	},
	
	externals: {
		fs: "commonjs fs"
	}
};
```
---
# Options
## `mode`
*This affects optimization, not functionality.*
- Controls:
	- Minification
	- Dead-code elimination
	- Performance defaults
- Options:
	- `development`
	- `production`

## `target`
- Tells webpack:
	- This code will run in Node.
	- Do not polyfill browser APIs.
	- Keep `require`, `__dirname`, etc. valid
- If you omit this: 
	- Webpack assumes browser.

## `entry`
*This is the root of the dependency graph.*
- Start here.
- Follow every `import`/`require`.
- Build the full graph.

## `output`
- Put final output in `dist/`.
- Name it `bundle.js`.
- Delete old files on rebuild.

## `resolve`
*Module resolution bending.*
```
import "./utils";
```
- Allows even though no extension.
- Node ESM would reject this.
- Webpack resolved this at build time.

## `module.rules`
Webpack itself does not understand TS. Loaders teach it how.
- For `.ts` files
- Run them through TypeScript
- Turn them into JavaScript

## `externals`
- Tells webpack:
	- Do not bundle `fs`.
	- Leave `require("fs")` as-is.
- Why?
	- `fs` exists at runtime in Node.
	- Bundling it makes no sense.
	- Prevents massive bugs.
- For Node.js projects, built-ins should almost always be external.
---
# Steps
1. Build dependency graph.
2. Converts everything into a virtual module system.
3. Wraps modules like this:
```
(function (modules) {
	// custom require implementation
}) (/* bundled modules */)

// Node is not resolving modules anymore.
// Webpack's runtime is.
```
