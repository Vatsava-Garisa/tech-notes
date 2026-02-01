
# What?
*A bundler is a build tool that takes many files (TS/JS/modules/assets) and produces one or few output files optimized for execution or distribution.*
- **Input:** 200 TS/JS files + node_modules
- **Output:** 1-5 JS files (plus maps).
---
# Why?
## Problem - 1
*Browsers were terrible at loading modules.*
Early browsers:
	- No CommonJS.
	- No ESM.
	- No `require`.
	- One `<script>` at a time.
	- Network requests were expensive.
```
<script src="./a.js"></script>
<script src="./b.js"></script>
<script src="./c.js"></script>

/* Too slow, order-dependent, error-prone */
```
- **Bundlers:**
	- Following dependency graph.
	- Combining everything into one file.
	- Guaranteeing execution order.

## Problem - 2
*node_modules explosion.*
- Modern apps:
	- Thousands of tiny modules.
	- Deep dependency trees.
 - Even today:
	 - Loading 2000 files individually is slow. (especially in browsers)
 - **Bundlers:**
	 - Inline dependencies.
	 - Deduplicate code.
	 - Remove unused exports. (tree-shaking)

## Problem - 3
- Browsers and Node cannot run:
	- TypeScript
	- JSX
	- CSS imports
	- JSON imports
	- Images, fonts, etc.
- **Bundlers:**
	- Transform these into runnable JS.
	- Or inline them.
---
# Core
*Bundlers build a dependency graph, then rewrite it.*
- Steps
	1. Start at entry file. (`src/index.ts`)
	2. Follows all `import`s.
	3. Build full graph.
	4. Transform code.
	5. Emit optimized output.
- This is why static ESM imports matter so much.
- Bundlers resolve and rewrite the module graph at build time, so Node (or browser) no longer has to resolve modules at runtime.
	- Node's ESM loader rules don't apply anymore.
	- The bundler already produced valid, final JS.
	- Node just executes the result.
- Bundlers shift responsibility from runtime (Node) to build-time (bundler). Whoever resolves the modules controls the rules.
---
# Consequences
### Without bundlers (pure Node ESM)
- Use `.js` extension.
- Match Node resolution exactly.
- Respect sync vs async loading.
- Follow `"type": "module"`.
```
Node
 └─ resolves imports
 └─ loads files
 └─ enforces ESM rules
```
### With bundlers
- Node/browser becomes less strict because:
	- Bundler rewrites imports.
	- Bundler inline dependencies.
	- Node no longer resolves modules at runtime.
```
Bundler
 └─ resolves imports
 └─ flattens graph
 └─ rewrites code
Node
 └─ just executes output
```
---
# Bundlers bending rules
### Example - 1: No `.js` extension
```
import "./utils";

/* Node ESM hates this. */
```
- Bundlers allow it.
	- Bundler resolved at build time.
	- Emit valid JS internally.

### Example - 2: Mixing CJS and ESM freely
```
import express from "express";
```
- Understands both formats.
- Wraps CommonJS automatically.
- No runtime interop pain.

### Example - 3: Tree-shaking
- Use static ESM graph.
- Remove unused exports.
- Impossible with runtime-only CommonJS.
---
# Bundler examples
## esbuild
- Extremely fast bundler + transpiler.
- Written in Go.
- Low-level tool.
- Traits:
	- Blazing fast
	- Opinionated
	- Minimal config
	- Excellent ESM support
- Best for:
	- Libraries
	- CLIs
	- Backend services
	- Dev tooling

## tsup
- A wrapper around esbuild.
- Designed for TypeScript.
- Traits:
	- Zero/low config
	- `.d.ts` generation
	- Dual output (CJS + ESM)
	- Tree-shaking by default
```
tsup src/index.ts --format esm,cjs
```
- For Node 18+ libraries, tsup is usually the right answer.

## webpack
- Extremely powerful.
- Extremely configurable.
- Historically browser-focused.
- Strengths:
	- Complex asset pipelines.
	- Legacy browser support.
	- Advanced code splitting.
- Weaknesses:
	- Heavy config
	- Slower
	- Overkill for Node backends
- Rarely needed for pure Node.js.
- Still common in frontend stacks.
---
# When you NEED
- You push a library.
- You want dual CJS + ESM output.
- You want tree-shaking.
- You want fast startup. (fewer files)
- You want to hide internal structure.
---
