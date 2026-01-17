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

## Deep Dive: Key Topics

### Bootstrap: What and Why?

**Bootstrap** is a CSS framework that provides pre-built, tested components and a responsive grid system. Instead of building everything from scratch, you use Bootstrap classes to quickly add buttons, forms, navbars, and more.

**Why use it?**
- **Speed**: Pre-styled components mean less CSS to write
- **Consistency**: All components follow the same design system
- **Responsiveness**: Built-in support for mobile, tablet, and desktop
- **Community**: Extensive documentation and thousands of ready-made components

**How to use Bootstrap**:
1. Include Bootstrap via CDN (content delivery network) in your HTML `<head>`
2. Use Bootstrap classes like `btn`, `container`, `row`, `col` in your HTML
3. Customize with your own CSS when needed

### The Bootstrap Grid System

Bootstrap uses a **12-column grid** to organize page layouts. Think of it as invisible columns that guide where content goes.

```html
<div class="container">
  <div class="row">
    <div class="col-md-6">Half width on medium+ screens</div>
    <div class="col-md-6">Half width on medium+ screens</div>
  </div>
</div>
```

Key terms:
- **Container**: Wraps your grid (`container` or `container-fluid`)
- **Row**: Groups columns horizontally
- **Col**: Individual column (can span 1–12 columns)
- **Breakpoints**: `sm` (≥576px), `md` (≥768px), `lg` (≥992px), `xl` (≥1200px)

Example: `col-md-8 offset-md-2` means "8 columns wide on medium screens, centered with 2-column offset."

### Responsive Breakpoints

Bootstrap lets you specify different layouts for different screen sizes:

```html
<div class="col-12 col-md-6 col-lg-4">
  This is 12 columns on mobile, 6 on tablets, 4 on desktops
</div>
```

**Why?** On a phone, you want full-width content. On a desktop, you can fit 3 items side-by-side.

### CSS Box Model: Margin, Padding, Border

Every element in CSS is surrounded by invisible space. Understanding this is crucial:

```
┌─────────────────────────────────────┐
│         Margin (outside)            │
│  ┌─────────────────────────────┐    │
│  │ Border                      │    │
│  │ ┌───────────────────────┐   │    │
│  │ │ Padding (inside)      │   │    │
│  │ │ ┌─────────────────┐   │   │    │
│  │ │ │ Content         │   │   │    │
│  │ │ └─────────────────┘   │   │    │
│  │ └───────────────────────┘   │    │
│  └─────────────────────────────┘    │
└─────────────────────────────────────┘
```

- **Margin**: Space *outside* the element (pushes other elements away)
- **Border**: Line around the element
- **Padding**: Space *inside* the element (pushes content away from edge)
- **Content**: The actual text, images, etc.

Example:
```css
button {
  margin: 10px;      /* 10px space outside */
  padding: 12px 20px; /* 12px top/bottom, 20px left/right inside */
  border: 2px solid blue;
}
```

### Flexbox: Easy Layouts

**Flexbox** is a modern CSS layout method that makes it easy to align and distribute space among items.

```html
<div style="display: flex; gap: 20px;">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

Key flexbox concepts:
- **`display: flex`**: Activates flexbox on a container
- **`gap`**: Space between items
- **`justify-content`**: Align items horizontally (`flex-start`, `center`, `space-between`)
- **`align-items`**: Align items vertically (`flex-start`, `center`, `flex-end`)

Example: Center items horizontally and vertically:
```css
.flex-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
}
```

Bootstrap uses flexbox internally, so many Bootstrap classes (like `row`) automatically use flexbox.

### Media Queries: Responsive CSS

**Media queries** let you apply different CSS based on screen size:

```css
/* Default: Mobile-first */
body {
  font-size: 14px;
}

/* Tablets and larger */
@media (min-width: 768px) {
  body {
    font-size: 16px;
  }
}

/* Desktops */
@media (min-width: 1200px) {
  body {
    font-size: 18px;
  }
}
```

**Mobile-first approach**: Start with mobile styles, then add larger-screen styles with media queries. This ensures your site works on phones first.

### CSS Specificity and Custom Overrides

Sometimes you need to override Bootstrap's default styles. CSS applies the most *specific* rule.

**Specificity (from lowest to highest)**:
1. Element selectors: `button` (lowest specificity)
2. Class selectors: `.btn`
3. ID selectors: `#my-button`
4. Inline styles: `style="color: red;"` (highest specificity)

**To override Bootstrap**, use a class selector (same specificity) defined *after* Bootstrap, or be more specific:

```css
/* Bootstrap defines */
.btn-primary {
  background-color: #007bff;
}

/* You can override with same specificity (must come after Bootstrap in CSS) */
.btn-primary {
  background-color: #0056b3; /* Darker blue */
}

/* Or use CSS variables (recommended) */
:root {
  --bs-primary: #0056b3;
}
```

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

1. **Read**: [Bootstrap Getting Started](https://getbootstrap.com/docs/5.3/getting-started/introduction/) — Learn what Bootstrap is and how to include it in a project
2. **Read**: [Bootstrap Grid System](https://getbootstrap.com/docs/5.3/layout/grid/) — Understand the 12-column responsive grid
3. **Read**: [Bootstrap Components](https://getbootstrap.com/docs/5.3/components/buttons/) — Explore buttons, forms, cards, and other components
4. **Read**: [MDN — CSS Basics](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps)
5. **Read**: [MDN — Box Model](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model)
6. **Read**: [MDN — Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)
7. **Read**: [MDN — Responsive Design](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Responsive_Design)
8. **Practice**: Implement a mobile-friendly layout where the form and task list stack vertically on small screens

**Optional Challenge**:

- Create a dark mode toggle using CSS variables and custom CSS

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| Bootstrap Getting Started | [Bootstrap Introduction](https://getbootstrap.com/docs/5.3/getting-started/introduction/) | Overview and setup |
| Bootstrap Grid | [Bootstrap Grid System](https://getbootstrap.com/docs/5.3/layout/grid/) | 12-column responsive layout |
| Bootstrap Components | [Bootstrap Buttons](https://getbootstrap.com/docs/5.3/components/buttons/) | Pre-built UI components |
| Bootstrap Utilities | [Bootstrap Utilities](https://getbootstrap.com/docs/5.3/utilities/api/) | Spacing, colors, display helpers |
| Bootstrap Forms | [Bootstrap Forms](https://getbootstrap.com/docs/5.3/forms/overview/) | Form styling and validation |
| Bootstrap Docs | [Bootstrap 5.3](https://getbootstrap.com/docs/5.3/) | Complete reference |
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
