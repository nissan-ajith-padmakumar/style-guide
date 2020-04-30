# Formatting

### Braces

**Braces are used for all control structures**

Braces are required for all control structures \(i.e. `if`, `else`, `for`, `do`, `while`, as well as any others\), even if the body contains only a single statement. The first statement of a non-empty block must begin on its own line.

Disallowed:

```javascript
if (someVeryLongCondition())
  doSomething();

for (let i = 0; i < foo.length; i++) bar(foo[i]);
```

**Exception**: A simple if statement that can fit entirely on a single line with no wrapping \(and that doesn’t have an else\) may be kept on a single line with no braces when it improves readability. This is the only case in which a control structure may omit braces and newlines.

```javascript
if (shortCondition()) foo();
```

**Nonempty blocks: K&R style**

Braces follow the Kernighan and Ritchie style \([Egyptian brackets](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html)\) for _nonempty_ blocks and block-like constructs:

* No line break before the opening brace.
* Line break after the opening brace.
* Line break before the closing brace.
* Line break after the closing brace _if_ that brace terminates a statement or the body of a function or class statement, or a class method. Specifically, there is _no_ line break after the brace if it is followed by `else`, `catch`, `while`, or a comma, semicolon, or right-parenthesis.

Example:

```javascript
class InnerClass {
  constructor() {}

  /** @param {number} foo */
  method(foo) {
    if (condition(foo)) {
      try {
        // Note: this might fail.
        something();
      } catch (err) {
        recover();
      }
    }
  }
}
```



**Empty blocks: may be concise**

An empty block or block-like construct _may_ be closed immediately after it is opened, with no characters, space, or line break in between \(i.e. `{}`\), **unless** it is a part of a _multi-block statement_ \(one that directly contains multiple blocks: `if`/`else` or `try`/`catch`/`finally`\).

Example:

```javascript
function doNothing() {}
```

Disallowed:

```javascript
if (condition) {
  // …
} else if (otherCondition) {} else {
  // …
}

try {
  // …
} catch (e) {}
```



### Block indentation: +2 space

Each time a new block or block-like construct is opened, the indent increases by two spaces. When the block ends, the indent returns to the previous indent level. The indent level applies to both code and comments throughout the block. .

**Array literals: optionally block-like**

Any array literal may optionally be formatted as if it were a “block-like construct.” For example, the following are all valid \(**not** an exhaustive list\):

```javascript
const a = [
  0,
  1,
  2,
];

const b =
    [0, 1, 2];
```

```javascript
const c = [0, 1, 2];

someMethod(foo, [
  0, 1, 2,
], bar);
```

Other combinations are allowed, particularly when emphasizing semantic groupings between elements, but should not be used only to reduce the vertical size of larger arrays.

**Object literals: optionally block-like**

Any object literal may optionally be formatted as if it were a “block-like construct.” 

```javascript
const a = {
  a: 0,
  b: 1,
};

const b =
    {a: 0, b: 1};
```

```javascript
const c = {a: 0, b: 1};

someMethod(foo, {
  a: 0, b: 1,
}, bar);
```

**Class literals**

Class literals \(whether declarations or expressions\) are indented as blocks. Do not add semicolons after methods, or after the closing brace of a class _declaration_ \(statements—such as assignments—that contain class _expressions_ are still terminated with a semicolon\). Use the `extends` keyword, but not the `@extends` JSDoc annotation unless the class extends a templatized type.

Example:

```javascript
class Foo {
  constructor() {
    /** @type {number} */
    this.x = 42;
  }

  /** @return {number} */
  method() {
    return this.x;
  }
}
Foo.Empty = class {};
```

```javascript
/** @extends {Foo<string>} */
foo.Bar = class extends Foo {
  /** @override */
  method() {
    return super.method() / 2;
  }
};

/** @interface */
class Frobnicator {
  /** @param {string} message */
  frobnicate(message) {}
}
```

**Function expressions**

When declaring an anonymous function in the list of arguments for a function call, the body of the function is indented two spaces more than the preceding indentation depth.

Example:

```javascript
prefix.something.reallyLongFunctionName('whatever', (a1, a2) => {
  // Indent the function body +2 relative to indentation depth
  // of the 'prefix' statement one line above.
  if (a1.equals(a2)) {
    someOtherLongFunctionName(a1);
  } else {
    andNowForSomethingCompletelyDifferent(a2.parrot);
  }
});

some.reallyLongFunctionCall(arg1, arg2, arg3)
    .thatsWrapped()
    .then((result) => {
      // Indent the function body +2 relative to the indentation depth
      // of the '.then()' call.
      if (result) {
        result.use();
      }
    });
```

**Switch statements**

As with any other block, the contents of a switch block are indented +2.

After a switch label, a newline appears, and the indentation level is increased +2, exactly as if a block were being opened. An explicit block may be used if required by lexical scoping. The following switch label returns to the previous indentation level, as if a block had been closed.

A blank line is optional between a `break` and the following case.

Example:

```javascript
switch (animal) {
  case Animal.BANDERSNATCH:
    handleBandersnatch();
    break;

  case Animal.JABBERWOCK:
    handleJabberwock();
    break;

  default:
    throw new Error('Unknown animal');
}
```

### Comments

Use `/** ... */` for multiline comments.

```javascript
// bad
// make() returns a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {

  // ...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}
```

Use `//` for single line comments. Place single line comments on a newline above the subject of the comment. Put an empty line before the comment unless it’s on the first line of a block.

```javascript
// bad
const active = true;  // is current tab

// good
// is current tab
const active = true;

// bad
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// good
function getType() {
  console.log('fetching type...');

  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// also good
function getType() {
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}
```

Start all comments with a space to make it easier to read

```javascript
// bad
//is current tab
const active = true;

// good
// is current tab
const active = true;

// bad
/**
 *make() returns a new element
 *based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}
```

Prefixing your comments with `FIXME` or `TODO` helps other developers quickly understand if you’re pointing out a problem that needs to be revisited, or if you’re suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable. The actions are `FIXME: -- need to figure this out` or `TODO: -- need to implement`.

Use `// FIXME:` to annotate problems.

```javascript
class Calculator extends Abacus {
  constructor() {
    super();

    // FIXME: shouldn’t use a global here
    total = 0;
  }
}
```

Use `// TODO:` to annotate solutions to problems.

```javascript
class Calculator extends Abacus {
  constructor() {
    super();

    // TODO: total should be configurable by an options param
    this.total = 0;
  }
}
```

