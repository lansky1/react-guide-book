# DOM Manipulation

## Selecting Elements

**`querySelector()`**: Returns the **first** element that matches a CSS selector

```javascript
document.querySelector(".class-name"); // First element with class
document.querySelector("#id"); // Element by ID
document.querySelector("div > p"); // First p inside a div
```

**`querySelectorAll()`**: Returns **all** matching elements as a NodeList

```javascript
document.querySelectorAll(".item"); // All elements with class 'item'
```

> **NodeList**: Array-like but not an array. Has `.forEach()` and `.length`, but lacks `.map()` / `.filter()`. Convert with `Array.from(nodeList)` or `[...nodeList]` if needed.

## Content Properties

**⚠️ XSS Warning:** Never insert user input using `innerHTML`.

- **`innerHTML`**: Use when you need to **read or write HTML tags**

  - Example: `div.innerHTML = '<strong>Bold</strong> text';`

- **`textContent`**: Use when you only need **plain text**. It's fast and safe (reads raw text directly)

  - Example: `div.textContent = 'Just plain text';`

- **`innerText`**: Use only if you need the **visible text** (what the user sees). Slower because it checks CSS, triggers layout, and skips hidden elements

- **`outerHTML`**: Use to **replace the whole element** with new HTML

  - Example: `div.outerHTML = '<p>The div is now a paragraph.</p>';`

- **`outerText`**: When you change it, it **replaces the entire element with just plain text**, destroying the original element

> **Simple rule:** `*HTML` properties = tags are **rendered**. `*Text` properties = tags are shown **as-is** (plain text).

## Security Considerations

> **⚠️ Security Warning:** If you use `innerHTML` with text from a user (like a comment), a hacker could write `<script>alert('hacked!')</script>`. Your webpage would run that script. This is called a **Cross-Site Scripting (XSS)** attack.
