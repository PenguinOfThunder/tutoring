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
