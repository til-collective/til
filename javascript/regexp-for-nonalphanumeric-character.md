A short equivalent for `[^a-zA-Z0-9]` would be `[\W_]`
```javascript
text.replace(/[\W_]+/g," ");
```
`\W` is the negation of shorthand `\w` for `[A-Za-z0-9_]` word characters (including the underscore)
