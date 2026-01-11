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
