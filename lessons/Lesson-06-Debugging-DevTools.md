# Lesson 5: Debugging & DevTools

## Objective

Use browser DevTools effectively to debug code, inspect the DOM, and profile performance; establish a debugging workflow.

---

## In-Class Materials

### Topics Covered

1. **Browser DevTools Essentials**:
   - Opening DevTools (F12 / Cmd+Option+I)
   - Tabs: Elements, Console, Sources, Network, Performance
2. **Debugging Techniques**:
   - Setting breakpoints and stepping through code
   - Inspecting variables and the call stack
   - Using `console.log()`, `console.table()`, and `console.error()`
3. **Inspecting and Modifying the DOM**:
   - Selecting elements in the Elements tab
   - Live editing HTML and CSS
   - Checking computed styles
4. **Performance Profiling**:
   - Recording performance profiles
   - Identifying slow operations
   - Measuring render times
5. **Network Panel**:
   - Viewing HTTP requests and responses
   - Checking status codes and headers
   - Simulating slow connections

### Key Concepts

- **Breakpoints**: Pause execution at specific lines to inspect state
- **Call Stack**: Understanding the sequence of function calls
- **Performance**: Identifying and fixing slow code
- **Iterative Debugging**: Methodically narrow down bugs

---

## Deep Dive: Key Topics

### Browser DevTools: Your Debugging Superpower

**DevTools** is a built-in browser tool that lets you inspect your code, debug issues, and analyze performance. It's essential for web development.

**Opening DevTools:**
- Windows/Linux: Press `F12` or `Ctrl+Shift+I`
- macOS: Press `Cmd+Option+I` or `Cmd+Option+J` (opens Console)
- Or right-click any element and select "Inspect" or "Inspect Element"

**Main Tabs:**
1. **Elements** — Inspect and modify HTML/CSS
2. **Console** — Log messages and run JavaScript commands
3. **Sources** — Debug JavaScript with breakpoints
4. **Network** — View HTTP requests and responses
5. **Performance** — Profile and measure performance

### The Elements Tab: Inspecting the DOM

The **Elements** tab shows your page's HTML structure (the DOM) and lets you modify it live:

```
<main class="container">
  <section>
    <h2>Your Tasks</h2>
    <ul id="task-list" class="list-group">
      <li class="list-group-item">Buy milk</li>
      <li class="list-group-item">Walk dog</li>
    </ul>
  </section>
</main>
```

**What you can do:**
- Click any element to see its HTML and CSS
- Right-click an element on the page → "Inspect" to jump to it in DevTools
- **Edit HTML**: Double-click text or attributes to modify them live
- **Edit CSS**: Find the style rules on the right and modify properties
- View **Computed Styles** to see final calculated CSS values (useful for debugging why a style isn't applying)

**Important**: DOM changes in DevTools are temporary — they disappear on page refresh.

### The Console Tab: Quick Debugging

The **Console** is a JavaScript REPL (Read-Eval-Print Loop) where you can run commands and see output:

```javascript
// Check if variables exist
tasks
// Output: (3) [{…}, {…}, {…}]

// Inspect a specific object
tasks[0]
// Output: {id: 1, title: "Buy milk", description: "...", completed: false, ...}

// Display in table format (great for arrays of objects)
console.table(tasks)
// Shows tasks in a formatted table

// Get information about array length
tasks.length
// Output: 3

// Modify data (temporary - only in memory)
tasks[0].title = "Buy cheese"
tasks[0].title
// Output: "Buy cheese"

// Call functions
addTask("New task", "Description")

// Log with different severity levels
console.log("Info message")        // Normal
console.warn("Warning message")    // Yellow
console.error("Error message")     // Red
console.table(array)               // Table format
```

The Console is invaluable for **quick exploration** and **temporary testing** without modifying your source code.

### The Sources Tab: Setting Breakpoints

**Breakpoints** pause your code execution so you can inspect the state at that moment. This is the most powerful debugging tool:

**Setting a Breakpoint:**
1. Open DevTools → "Sources" tab
2. Find your file (`app.js` or `app.ts`)
3. Click on a line number to set a breakpoint (a blue marker appears)
4. Trigger the code path (e.g., add a task)
5. Execution pauses at the breakpoint

**Using Breakpoints:**
```typescript
function addTask(title: string, description: string): void {
  const task: Task = {
    id: nextId++,        // ← Breakpoint here
    title,
    description,
    completed: false,
    createdAt: new Date()
  };
  tasks.push(task);
  renderTasks();
}
```

When paused at a breakpoint:
- **Variables Panel** (right side): See all variables in scope and their values
- **Call Stack**: See which functions called which (helps understand execution flow)
- **Stepping Controls**: 
  - **Step Over** (F10): Execute current line and move to next
  - **Step Into** (F11): Enter a function call to debug inside it
  - **Step Out** (Shift+F11): Exit current function
  - **Resume** (F8): Continue execution until next breakpoint

### The Call Stack: Understanding Function Calls

The **Call Stack** shows you the sequence of function calls that led to the current point:

```
renderTasks()
  └─ forEach() callback
      └─ appendChild()

// Call Stack shows:
# 1. appendChild (bottom - built-in, not your code)
# 2. (anonymous) - the forEach callback function
# 3. renderTasks - the function you're debugging
```

This helps you understand *where* you are in your code and *how* you got there.

### Console Logging Methods

Instead of using breakpoints, you can log variables to the Console:

```typescript
function addTask(title: string, description: string): void {
  console.log('addTask called with:', title, description); // Log inputs
  
  const task: Task = {
    id: nextId++,
    title,
    description,
    completed: false,
    createdAt: new Date()
  };
  
  console.table([task]); // Show new task in table format
  
  tasks.push(task);
  console.log('Total tasks:', tasks.length); // Log result
  
  renderTasks();
}
```

Open Console to see output. This leaves traces of execution without stopping code.

**Methods:**
- `console.log()` — General information
- `console.warn()` — Warnings (yellow)
- `console.error()` — Errors (red)
- `console.table()` — Display arrays/objects as tables
- `console.group()` / `console.groupEnd()` — Group related logs
- `console.time()` / `console.timeEnd()` — Measure elapsed time

### Inspecting and Modifying the DOM

The Elements tab is powerful for understanding *why* something looks wrong:

**Common Debugging Scenarios:**
1. **Element not visible?**
   - Check if it's in the HTML (Elements tab)
   - Check CSS `display`, `opacity`, `visibility` properties
   - Check if element is positioned off-screen

2. **Style not applying?**
   - Check Computed Styles to see what *is* applied
   - Look for CSS specificity issues (another rule overriding yours)
   - Check if CSS file is linked correctly

3. **Text content wrong?**
   - Inspect the element to see actual HTML
   - Check if JavaScript is modifying it correctly

**Live Editing:**
You can edit HTML and CSS directly in DevTools to test changes before updating your source code.

### The Network Tab: Inspecting HTTP Requests

The **Network** tab shows all HTTP requests and responses your page makes:

**What you can see:**
- Request URL, method (GET, POST), and status code
- Response headers and data
- Request size and time
- Preview of response (HTML, JSON, images, etc.)

**Common Status Codes:**
- `200` — Success
- `404` — Not Found
- `500` — Server Error
- `301/302` — Redirect

This is essential when working with backend APIs (which you'll do in Lesson 9).

### Performance Profiling: Finding Slow Code

The **Performance** tab records what happens during a time period and shows you:
- How long rendering took
- Which functions consumed the most time
- Frame rate and memory usage

**How to Profile:**
1. Open DevTools → "Performance" tab
2. Click the record button (red circle)
3. Interact with your page (add/delete tasks)
4. Stop recording
5. Analyze the timeline to find performance bottlenecks

For small projects, the main concern is making sure DOM updates don't cause jank (slow, stuttering animation).

---

## In-Class Exercise (15–20 minutes)

**Task: Debug and optimize the task app**

1. **Set a Breakpoint**:
   - Open DevTools (F12)
   - Go to the "Sources" tab
   - Click on `app.ts` (or the compiled `app.js`)
   - Click on the line number in the `renderTasks()` function to set a breakpoint
   - Add a task to trigger the breakpoint
   - Inspect the `tasks` array in the right panel

2. **Use Console for Debugging**:
   - Open the Console tab
   - Type: `console.table(tasks)` to view tasks in a table
   - Type: `tasks.length` to check the number of tasks
   - Type: `delete tasks[0]` and observe the effect

3. **Inspect the DOM**:
   - Go to the "Elements" tab
   - Right-click on a task item and select "Inspect"
   - Modify the HTML directly in the Elements panel (e.g., change task text)
   - Note: Changes disappear on refresh (not persistent)

4. **Profile Performance** (optional):
   - Go to the "Performance" tab
   - Click the record button
   - Add and delete several tasks quickly
   - Stop recording
   - Analyze the timeline to find slow operations

5. **Identify an Intentional Bug**:
   - Your instructor will introduce a subtle bug (e.g., tasks don't render after delete)
   - Use breakpoints and console logging to find the cause
   - Fix the bug and verify the solution

---

## Homework Assignment

**Reading & Practice**: Advanced DevTools Techniques

**Tasks**:

1. **Read**: [Chrome DevTools — Debugging Guide](https://developer.chrome.com/docs/devtools/js-problems/)
2. **Read**: [Chrome DevTools — Performance](https://developer.chrome.com/docs/devtools/performance/)
3. **Practice**: Refactor your `app.ts` into multiple modules:
   - `modules/task.ts` — Task interface and logic
   - `modules/ui.ts` — DOM rendering and event handling
   - `app.ts` — Main entry point
4. **Test**: Verify each module loads correctly in the browser

**Optional Challenge**:

- Add a performance measurement function that logs how long `renderTasks()` takes to execute

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Chrome DevTools | [Chrome DevTools Docs](https://developer.chrome.com/docs/devtools/) | Complete guide |
| Debugging JS | [Chrome DevTools — Debug JS](https://developer.chrome.com/docs/devtools/javascript/) | Breakpoints and stepping |
| Performance | [Chrome DevTools — Performance](https://developer.chrome.com/docs/devtools/performance/) | Profiling and optimization |
| Console API | [MDN — Console](https://developer.mozilla.org/en-US/docs/Web/API/Console) | Logging methods |
| Network Tab | [Chrome DevTools — Network](https://developer.chrome.com/docs/devtools/network/) | Inspecting requests |

---

## Key Takeaways

✓ Set breakpoints and stepped through code  
✓ Used console logging effectively  
✓ Inspected and modified the DOM in DevTools  
✓ Found and fixed a bug using debugger  
✓ Measured and analyzed performance  

---

## Next Lesson Preview

**Lesson 6 — Modern TypeScript**: We'll deepen our TypeScript knowledge with advanced features like interfaces, generics, and async/await.
