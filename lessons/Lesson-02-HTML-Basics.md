# Lesson 2: HTML Basics

## Objective

Build semantic page structure for the task/notes web app using semantic HTML elements and form markup.

---

## In-Class Materials

### Topics Covered

1. **HTML Document Structure**:
   - `<!DOCTYPE>`, `<html>`, `<head>`, `<body>`
   - Meta tags (charset, viewport, description)
2. **Semantic HTML Elements**:
   - Headers, footers, nav, main, section, article
   - Why semantics matter for accessibility and SEO
3. **Forms and Input Types**:
   - `<form>`, `<input>`, `<textarea>`, `<button>`
   - Labels, fieldsets, and form organization
4. **Interactive Elements**:
   - `<details>` and `<summary>` for expandable content
   - Building collapsible sections without JavaScript
5. **Building the Task App UI**:
   - Header with title and navigation
   - Task input form
   - Task list placeholders

### Key Concepts

- **Semantic HTML**: Using the right tags improves accessibility and SEO
- **Accessibility**: Form labels and ARIA attributes for screen readers
- **Progressive Enhancement**: HTML structure works before CSS and JavaScript

---

## Understanding Semantic HTML and Accessibility

### What Does "Semantic" Mean?

**Semantic HTML** means using HTML elements that accurately describe their content and purpose. Instead of relying on generic `<div>` and `<span>` tags, semantic HTML uses elements like `<nav>`, `<header>`, `<main>`, `<article>`, `<button>`, and `<form>` — each with a specific meaning.

For example:
- A generic approach: `<div id="navigation"><a href="/home">Home</a></div>`
- A semantic approach: `<nav><a href="/home">Home</a></nav>`

The semantic version is clearer in intent. The `<nav>` element explicitly says "this is navigation."

### How Semantic HTML Helps Accessibility

Accessibility (often abbreviated as a11y) means ensuring your web content is usable by **everyone**, including people with disabilities. Semantic HTML is foundational to accessibility because:

1. **Screen Readers Understand Structure**: 
   - Screen reader software (like NVDA, JAWS, or VoiceOver) relies on HTML structure to help blind and low-vision users navigate your page.
   - A `<button>` element is announced as a button; a `<div class="button">` is just text.
   - A `<form>` element signals that inputs are grouped together logically.

2. **Keyboard Navigation Works**:
   - Semantic elements like `<button>`, `<a>`, and `<form>` are keyboard-navigable by default.
   - Screen reader users rely on keyboard navigation because they can't use a mouse.
   - A `<div>` won't be keyboard-accessible without extra JavaScript.

3. **Assistive Technology Integration**:
   - Screen readers announce the purpose of elements (e.g., "navigation", "heading", "search form").
   - This helps users understand the page structure quickly without having to listen to every word.

4. **Browser and Device Support**:
   - Semantic elements work consistently across browsers and devices.
   - They provide built-in keyboard handling and ARIA roles automatically.

### Real-World Example

A page using **non-semantic** markup:
```html
<div id="header">
  <div id="title">My Tasks</div>
  <div id="nav">
    <a href="#all">All</a>
    <a href="#completed">Completed</a>
  </div>
</div>
```

A blind user with a screen reader hears: "div... div... div... link all... link completed..."  
They have no idea this section is a header or that it contains navigation.

The same page using **semantic** markup:
```html
<header>
  <h1>My Tasks</h1>
  <nav>
    <a href="#all">All</a>
    <a href="#completed">Completed</a>
  </nav>
</header>
```

A blind user with a screen reader hears: "banner... heading level 1 my tasks... navigation... link all... link completed..."  
They immediately understand the page structure.

### Semantic Elements You'll Use

- `<header>`: Page or section header
- `<nav>`: Navigation links
- `<main>`: Primary content area
- `<section>`: Thematic grouping of content
- `<article>`: Self-contained content (like a blog post)
- `<footer>`: Footer information
- `<button>`: Interactive button (not `<div class="btn">`)
- `<form>`: Form container
- `<label>`: Text label for form inputs
- `<h1>–<h6>`: Headings (convey structure)

---

## In-Class Exercise (15–20 minutes)

**Task: Build the semantic HTML structure for the task app**

1. Create `frontend/index.html` with the basic structure:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>Task Manager</title>
   </head>
   <body>
     <header>
       <h1>My Tasks</h1>
       <nav>
         <ul>
           <li><a href="#all">All</a></li>
           <li><a href="#completed">Completed</a></li>
         </ul>
       </nav>
     </header>
     
     <main>
       <section>
         <h2>Add a Task</h2>
         <form id="task-form">
           <label for="task-input">Task Title</label>
           <input type="text" id="task-input" name="title" required>
           
           <label for="task-desc">Description (optional)</label>
           <textarea id="task-desc" name="description"></textarea>
           
           <button type="submit">Add Task</button>
         </form>
       </section>
       
       <section>
         <h2>Your Tasks</h2>
         <ul id="task-list">
           <li>Sample task (placeholder)</li>
         </ul>
       </section>
       
       <section>
         <h2>Help & FAQ</h2>
         <details>
           <summary>How do I add a task?</summary>
           <p>Fill in the task title and optional description in the form above, then click "Add Task".</p>
         </details>
         <details>
           <summary>Can I edit tasks?</summary>
           <p>Yes! You can click on a task to edit it or mark it as completed.</p>
         </details>
       </section>
     </main>
     
     <footer>
       <p>&copy; 2026 Task Manager. Built for learning.</p>
     </footer>
   </body>
   </html>
   ```

2. Validate the HTML using the [W3C HTML Validator](https://validator.w3.org/)
3. Check that form inputs are properly labeled
4. Commit your work:

   ```bash
   git add frontend/index.html
   git commit -m "Add semantic HTML structure for task app"
   ```

---

## Previewing Your HTML in VSCode and Your Browser

After you've created your HTML file, you'll want to see how it looks in a browser. Here are the best ways to preview your work:

### Option 1: Using Live Server Extension (Recommended)

**Live Server** is a VSCode extension that automatically reloads your browser when you save changes.

1. **Install Live Server**:
   - Open the Extensions view in VSCode (`Ctrl+Shift+X` or `Cmd+Shift+X`)
   - Search for "Live Server" (by Ritwick Dey)
   - Click Install

2. **Start Live Server**:
   - Right-click on your `index.html` file in the file explorer
   - Select "Open with Live Server"
   - Your default browser will open automatically, typically at `http://localhost:5500`

3. **Edit and Reload**:
   - Make changes to your HTML file and save (`Ctrl+S` or `Cmd+S`)
   - The browser automatically reloads to show your changes
   - This is much faster than manually refreshing

### Option 2: Open HTML File Directly

If you don't want to install an extension, you can open the file in your browser manually:

1. **Right-click** on `frontend/index.html` in VSCode
2. Select **"Reveal in File Explorer"** (or **"Open in Finder"** on macOS)
3. **Double-click** the file to open it in your default browser
4. To see changes, save the file and **refresh your browser** (`Ctrl+R` or `Cmd+R`)

**Note**: This method doesn't auto-reload, so you'll need to refresh manually after each change.

### Option 3: Use VSCode's Preview

Some versions of VSCode include a built-in preview feature:

1. **Right-click** on `index.html`
2. Look for **"Preview"** or **"Open Preview"** option
3. A preview pane may open directly in VSCode

**Note**: This doesn't always work for all features, so Live Server is usually better.

### Tips for Testing Your HTML

- **Check the Browser Console**: Press `F12` or `Ctrl+Shift+I` to open Developer Tools and look for any errors
- **Validate Your HTML**: Copy your HTML to [W3C HTML Validator](https://validator.w3.org/) to catch markup errors
- **Test Form Inputs**: Try filling in your form inputs to make sure they work as expected
- **Test on Mobile**: Use Developer Tools (`F12` → click the device icon) to preview your page on different screen sizes

---

## Homework Assignment

**Reading**: HTML Fundamentals and Accessibility Basics

**Tasks**:

1. **Read**: [MDN — HTML Basics](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics)
2. **Read**: [MDN — Semantic HTML](https://developer.mozilla.org/en-US/docs/Glossary/Semantics)
3. **Read**: [WebAIM — Form Accessibility](https://webaim.org/articles/form-accessibility/)
4. **Practice**: Add an "About" page to your app (`frontend/about.html`) with semantic sections and headings

**Optional Challenges**:

- Enhance the "Help" section with more FAQ items using `<details>` and `<summary>` elements
- Style the `<details>` and `<summary>` elements with custom CSS in Lesson 3
- Use the built-in narrator software in your OS to read your webpage, as a blind person might do

---

## Homework Reference Materials

| Topic | Link | Notes |
| ------- | ------ | ------- |
| HTML Basics | [MDN — HTML Basics](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics) | Foundational HTML structure |
| Semantic HTML | [MDN — Semantic HTML](https://developer.mozilla.org/en-US/docs/Glossary/Semantics) | Best practices for meaning |
| HTML Forms | [MDN — HTML Forms](https://developer.mozilla.org/en-US/docs/Learn/Forms) | Comprehensive forms guide |
| Details Element | [MDN — details element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details) | Expandable disclosure widgets |
| Summary Element | [MDN — summary element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/summary) | Toggle label for details |
| Accessibility | [WebAIM — Form Accessibility](https://webaim.org/articles/form-accessibility/) | Making forms accessible |
| W3C Validator | [W3C HTML Validator](https://validator.w3.org/) | Validate your HTML |

---

## Key Takeaways

✓ Created semantic HTML structure for the app  
✓ Built a form with proper labels and inputs  
✓ Understood the importance of accessibility  
✓ Validated HTML and fixed markup issues  

---

## Next Lesson Preview

**Lesson 3 — Styling with Bootstrap**: We'll add visual polish and responsiveness using Bootstrap classes and utilities.
