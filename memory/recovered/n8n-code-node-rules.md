# n8n Code Node — ES5 Compatibility Rules

**Applies to:** n8n Code node (JavaScript mode)  
**Last Updated:** 2026-06-04  
**Status:** Active

## ✅ ALLOWED

```javascript
// var declarations only
var x = 1;

// Regular functions
function myFunc() { return 1; }

// String concatenation with +
var html = "<div>" + content + "</div>";

// Basic for loops
for (var i = 0; i < items.length; i++) { }

// Array.isArray check
if (Array.isArray(items)) { }

// JSON methods
JSON.stringify(obj);
JSON.parse(str);

// Object property access
var val = $json.property;
var fallback = $json.prop || "default";
```

## ❌ FORBIDDEN

```javascript
// Template literals (backticks)
var html = `<div>${content}</div>`;  // ❌

// Spread operator
var arr = [...items];  // ❌
var obj = { ...data };  // ❌

// Arrow functions
items.map(item => item.name);  // ❌

// let/const
let x = 1;  // ❌
const y = 2;  // ❌

// Destructuring
var { name, price } = item;  // ❌

// Array methods in callbacks (sometimes fail)
items.forEach(function(item) { });  // ❌ use for loop instead
items.map(function(item) { });  // ❌ use for loop instead

// Escaped quotes in strings
var str = "He said \"hello\"";  // ❌ use single quotes or different approach

// $items() cross-references
$items("OtherNode")[0].json;  // ❌ breaks execution lineage
```

## ✅ CORRECT PATTERN

```javascript
var items = $json.order_items || [];
var html = "<table>";

for (var i = 0; i < items.length; i++) {
  var item = items[i];
  html += "<tr>";
  html += "<td>" + item.name + "</td>";
  html += "</tr>";
}

html += "</table>";

return [{
  json: { email_html: html }
}];
```

## ⚠️ HTML IN STRINGS

### ❌ Wrong (escapes get literal):
```javascript
var div = "&lt;div style=&quot;color:red&quot;&gt;";  // renders as literal text
```

### ✅ Correct (real HTML tags):
```javascript
var div = "<div style='color:red'>";  // renders as HTML
```

## 🔗 Related
- [MEMORY.md](/MEMORY.md) — System decisions
- `memory/2026-06-04-n8n-email-fix.md` — Full session log