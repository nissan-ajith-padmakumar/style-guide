# ES Modules

## Imports

**Importing the same file multiple times**

Do not import the same file multiple times. This can make it hard to determine the aggregate imports of a file.

```javascript
// Imports have the same path, but since it doesn't align it can be hard to see.
import {short} from './long/path/to/a/file.js';
import {aLongNameThatBreaksAlignment} from './long/path/to/a/file.js';
```



#### Naming module imports

Module import names \(`import * as name`\) are `lowerCamelCase` names that are derived from the imported file name.

```javascript
import * as fileOne from '../file-one.js';
import * as fileTwo from '../file_two.js';
import * as fileThree from '../filethree.js';
```

```javascript
import * as libString from './lib/string.js';
import * as math from './math/math.js';
import * as vectorMath from './vector/math.js';
```



**Naming named imports**

In general symbols imported via the named import \(`import {name}`\) should keep the same name. Avoid aliasing imports \(`import {SomeThing as SomeOtherThing}`\). Prefer fixing name collisions by using a module import \(`import *`\) or renaming the exports themselves.

```javascript
import * as bigAnimals from './biganimals.js';
import * as domesticatedAnimals from './domesticatedanimals.js';

new bigAnimals.Cat();
new domesticatedAnimals.Cat();
```

If renaming a named import is needed then use components of the imported module's file name or path in the resulting alias.

```javascript
import {Cat as BigCat} from './biganimals.js';
import {Cat as DomesticatedCat} from './domesticatedanimals.js';

new BigCat();
new DomesticatedCat();
```



## **Exports**

Symbols are only exported if they are meant to be used outside the module. 

**Named vs default exports**

Use named exports in all code. You can apply the `export` keyword to a declaration, or use the `export {name};` syntax.

Do not use default exports. Importing modules must give a name to these values, which can lead to inconsistencies in naming across modules.

```javascript
// Do not use default exports:
export default class Foo { ... } // BAD!
```

```javascript
// Use named exports:
export class Foo { ... }
```

```javascript
// Alternate style named exports:
class Foo { ... }

export {Foo};
```



**Mutability of exports**

Exported variables must not be mutated outside of module initialization.

There are alternatives if mutation is needed, including exporting a constant reference to an object that has mutable fields or exporting accessor functions for mutable data.

```javascript
// Bad: both foo and mutateFoo are exported and mutated.
export let /** number */ foo = 0;

/**
 * Mutates foo.
 */
export function mutateFoo() {
  ++foo;
}

/**
 * @param {function(number): number} newMutateFoo
 */
export function setMutateFoo(newMutateFoo) {
  // Exported classes and functions can be mutated!
  mutateFoo = () => {
    foo = newMutateFoo(foo);
  };
}
```

```javascript
// Good: Rather than export the mutable variables foo and mutateFoo directly,
// instead make them module scoped and export a getter for foo and a wrapper for
// mutateFooFunc.
let /** number */ foo = 0;
let /** function(number): number */ mutateFooFunc = foo => foo + 1;

/** @return {number} */
export function getFoo() {
  return foo;
}

export function mutateFoo() {
  foo = mutateFooFunc(foo);
}

/** @param {function(number): number} mutateFoo */
export function setMutateFoo(mutateFoo) {
  mutateFooFunc = mutateFoo;
}
```



**Circular Dependencies in ES modules**

Do not create cycles between ES modules, even though the ECMAScript specification allows this. Note that it is possible to create cycles with both the `import` and `export` statements.

```javascript
// a.js
import './b.js';
```

```javascript
// b.js
import './a.js';

// `export from` can cause circular dependencies too!
export {x} from './c.js';
```

```javascript
// c.js
import './b.js';

export let x;
```

