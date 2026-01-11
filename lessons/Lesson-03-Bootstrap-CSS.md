# Lesson 3: Styling with Bootstrap (and CSS Fundamentals)

## Objective

Build a polished, responsive UI using Bootstrap components and utilities; reinforce CSS fundamentals (box model, flexbox, responsive design).

---

## In-Class Materials

### Topics Covered

1. **Bootstrap Introduction**:
   - What is Bootstrap and why use it
   - Bootstrap grid system (12 columns, responsive breakpoints)
   - Container, row, col classes
2. **Bootstrap Components**:
   - Buttons, forms, cards, alerts
   - Navbar for navigation
   - List groups
3. **CSS Fundamentals**:
   - Box model (margin, padding, border)
   - Flexbox basics
   - Media queries for responsive design
4. **Custom CSS Overrides**:
   - Specificity and CSS variables
   - Mobile-first approach

### Key Concepts

- **Responsive Design**: Different layouts for mobile, tablet, desktop
- **Component Reuse**: Bootstrap provides pre-built, tested components
- **CSS Box Model**: Understanding how spacing affects layout
- **Flexbox**: Flexible, modern layout method

---

## In-Class Exercise (15–20 minutes)

**Task: Add Bootstrap and style the task app UI**

1. Update `frontend/index.html` to include Bootstrap CDN:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>Task Manager</title>
     <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
     <link rel="stylesheet" href="styles.css">
   </head>
   <body>
     <header class="navbar navbar-expand-lg navbar-dark bg-dark">
       <div class="container-fluid">
         <span class="navbar-brand">📝 Task Manager</span>
         <nav class="navbar-nav ms-auto">
           <a class="nav-link" href="#all">All</a>
           <a class="nav-link" href="#completed">Completed</a>
         </nav>
       </div>
     </header>
     
     <main class="container mt-5">
       <div class="row">
         <div class="col-md-8 offset-md-2">
           <section class="mb-5">
             <h2>Add a Task</h2>
             <form id="task-form" class="needs-validation">
               <div class="mb-3">
                 <label for="task-input" class="form-label">Task Title</label>
                 <input type="text" class="form-control" id="task-input" name="title" required>
               </div>
               
               <div class="mb-3">
                 <label for="task-desc" class="form-label">Description (optional)</label>
                 <textarea class="form-control" id="task-desc" name="description" rows="3"></textarea>
               </div>
               
               <button type="submit" class="btn btn-primary">Add Task</button>
             </form>
           </section>
           
           <section>
             <h2>Your Tasks</h2>
             <ul id="task-list" class="list-group">
               <li class="list-group-item">Sample task (placeholder)</li>
             </ul>
           </section>
         </div>
       </div>
     </main>
     
     <footer class="bg-light text-center py-4 mt-5">
       <p class="mb-0">&copy; 2026 Task Manager. Built for learning.</p>
     </footer>
     
     <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
   </body>
   </html>
   ```

2. Create `frontend/styles.css` with custom overrides:

   ```css
   :root {
     --primary-color: #007bff;
     --secondary-color: #6c757d;
   }
   
   body {
     font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
     background-color: #f8f9fa;
   }
   
   header {
     box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
   }
   
   .list-group-item {
     border-left: 4px solid var(--primary-color);
     transition: background-color 0.2s;
   }
   
   .list-group-item:hover {
     background-color: #f0f0f0;
   }
   
   @media (max-width: 768px) {
     .container {
       padding: 0 1rem;
     }
   }
   ```

3. Test on mobile (use browser DevTools to resize)
4. Commit your changes:

   ```bash
   git add frontend/index.html frontend/styles.css
   git commit -m "Add Bootstrap styling and custom CSS overrides"
   ```

---

## Homework Assignment

**Reading**: CSS Fundamentals and Responsive Design

**Tasks**:

1. **Read**: [MDN — CSS Basics](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps)
2. **Read**: [MDN — Box Model](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model)
3. **Read**: [MDN — Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)
4. **Read**: [MDN — Responsive Design](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design)
5. **Practice**: Implement a mobile-friendly layout where the form and task list stack vertically on small screens

**Optional Challenge**:

- Create a dark mode toggle using CSS variables and custom CSS

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Bootstrap Docs | [Bootstrap 5.3](https://getbootstrap.com/docs/5.3/) | Components and grid |
| CSS Basics | [MDN — CSS Basics](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps) | Selectors, properties |
| Box Model | [MDN — Box Model](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model) | Margin, padding, border |
| Flexbox | [MDN — Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) | Modern layout method |
| Responsive Design | [MDN — Responsive Design](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design) | Mobile-first approach |

---

## Key Takeaways

✓ Used Bootstrap grid and components  
✓ Applied responsive design principles  
✓ Customized Bootstrap with CSS  
✓ Tested layout on multiple screen sizes  

---

## Next Lesson Preview

**Lesson 4 — TypeScript & DOM**: We'll add interactivity by manipulating the DOM with TypeScript.
