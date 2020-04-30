# Template strings

When programmatically building up strings, use template strings instead of concatenation. 

{% hint style="warning" %}
Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.
{% endhint %}

```javascript
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// bad
function sayHi(name) {
  return `How are you, ${ name }?`;
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

