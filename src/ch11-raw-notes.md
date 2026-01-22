# Raw Notes

1. The "always pass a function" rule only applies to state setters when you need the previous state value.

When setting state (`setState`):

- Pass a **function** when the new state depends on the previous state
- Pass a **value** when the new state doesn't depend on the previous state

2. The `.map()` method provides two parameters automatically:
   - First parameter: the current item in the array
   - Second parameter: the index/position of that item
   
   ```jsx
   array.map((item, index) => ...)
   //         ^^^^  ^^^^^ - both provided by .map()
   ```
   
   The `index` is useful for setting the `key` prop in React lists. However, using `index` as a key isn't ideal if you'll be reordering, filtering, or deleting items. For simple lists where items are only added to the end, `index` works fine.

3. **Common mistake: `.trim` vs `.trim()`**

   Always remember to include parentheses `()` when calling methods:

   ```js
   // ✗ WRONG - checks the length of the function itself
   if (title.trim.length === 0) {
       alert("Title is required!");
   }
   // This always fails because .trim without () is just a reference to the function
   // Functions have a .length property (number of parameters), not the trimmed string
   
   // ✓ CORRECT - calls trim() and checks the trimmed string length
   if (title.trim().length === 0) {
       alert("Title is required!");
   }
   ```

   **The rule:** Method calls need `()`. Without parentheses, you're referencing the function itself, not executing it.
