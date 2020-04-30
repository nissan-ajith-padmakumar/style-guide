# Special Characters

**Special escape sequences**

For any character that has a special escape sequence \(`\'`, `\"`, `\\`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`\), that sequence is used rather than the corresponding numeric escape \(e.g `\x0a`, `\u000a`, or `\u{a}`\). Legacy octal escapes are never used.

**Non-ASCII characters**

For the remaining non-ASCII characters, either the actual Unicode character \(e.g. `∞`\) or the equivalent hex or Unicode escape \(e.g. `\u221e`\) is used, depending only on which makes the code **easier to read and understand**.

> Tip: In the Unicode escape case, and occasionally even when actual Unicode characters are used, an explanatory comment can be very helpful.

```javascript
/* Best: perfectly clear even without a comment. */
const units = 'μs';

/* Allowed: but unncessary as μ is a printable character. */
const units = '\u03bcs'; // 'μs'

/* Good: use escapes for non-printable characters with a comment for clarity. */
return '\ufeff' + content;  // Prepend a byte order mark.
```

```javascript
/* Poor: the reader has no idea what character this is. */
const units = '\u03bcs';
```



