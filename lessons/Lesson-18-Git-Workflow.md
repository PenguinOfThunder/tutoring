# Lesson 18: Git Workflow & Collaboration

## Objective

Master Git branching, pull requests, and merge conflict resolution; establish a collaborative workflow.

---

## In-Class Materials

### Topics Covered

1. **Git Branching Strategy**:
   - Main/production branch (stable)
   - Feature branches (isolated development)
   - Naming conventions (feature/*, bugfix/*, etc.)
2. **Pull Requests (PRs)**:
   - Creating a PR on GitHub
   - Code review process
   - Merge strategies (squash, rebase, merge commit)
3. **Merge Conflicts**:
   - When conflicts occur
   - Resolving conflicts manually
   - Tools for conflict resolution
4. **Commit Messages**:
   - Meaningful, descriptive messages
   - Imperative mood ("Add feature" not "Added feature")
   - Atomic commits (one logical change)
5. **Collaboration Best Practices**:
   - Code review etiquette
   - Rebasing vs merging
   - Keeping branches up-to-date

### Key Concepts

- **Feature Branch**: Isolates work so main branch stays stable
- **Pull Request**: Formal way to propose changes and get reviews
- **Merge Conflict**: When same code is edited in different branches
- **Atomic Commit**: Single logical change that can be reviewed independently

---

## In-Class Exercise (15–20 minutes)

**Task: Create a feature branch, make changes, and simulate a PR merge**

1. Create and switch to a feature branch:

   ```bash
   git checkout -b feature/add-priority-field
   ```

2. Make a change (add a Priority field to Task):

   ```bash
   # Edit Models/Task.cs
   ```

   Add the priority field:

   ```csharp
   public class Task
   {
       // ... existing fields ...
       
       [Range(1, 5, ErrorMessage = "Priority must be between 1 and 5")]
       public int Priority { get; set; } = 3; // Default medium priority
   }
   ```

3. Commit with a meaningful message:

   ```bash
   git add backend/
   git commit -m "Add priority field to Task model

   - Allows users to set task priority from 1-5
   - Default priority is 3 (medium)
   - Includes validation for valid range"
   ```

4. Create a second feature branch to simulate a conflict:

   ```bash
   git checkout main
   git checkout -b feature/add-due-date
   ```

5. Edit the same file:

   ```csharp
   public class Task
   {
       // ... existing fields ...
       
       [DataType(DataType.Date)]
       public DateTime? DueDate { get; set; }
   }
   ```

6. Commit this change:

   ```bash
   git add backend/
   git commit -m "Add due date field to Task model"
   ```

7. Try to merge both branches (simulate conflict):

   ```bash
   git checkout main
   git merge feature/add-priority-field
   git merge feature/add-due-date
   ```

8. Resolve the conflict:

   ```bash
   # Edit Models/Task.cs to include both fields
   # After resolving:
   git add backend/
   git commit -m "Merge feature/add-due-date (resolved conflicts)"
   ```

9. Clean up feature branches:

   ```bash
   git branch -d feature/add-priority-field feature/add-due-date
   ```

10. View commit history:

    ```bash
    git log --oneline --graph --all
    ```

11. Commit your work:

    ```bash
    git add .
    git commit -m "Complete task model with priority and due date fields"
    ```

---

## Homework Assignment

**Reading & Practice**: Git Workflow

**Tasks**:

1. **Read**: [GitHub — Understanding the GitHub Flow](https://guides.github.com/introduction/flow/)
2. **Read**: [Atlassian — Git Branching](https://www.atlassian.com/git/tutorials/using-branches)
3. **Read**: [Conventional Commits](https://www.conventionalcommits.org/)
4. **Practice**: Create 2–3 feature branches with different changes
5. **Practice**: Resolve a merge conflict manually

**Optional Challenge**:

- Use `git rebase` instead of `git merge`
- Create a GitHub PR (if repo is on GitHub) and get feedback
- Squash commits using interactive rebase

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| GitHub Flow | [GitHub Guide](https://guides.github.com/introduction/flow/) | PR workflow |
| Git Branching | [Atlassian — Branching](https://www.atlassian.com/git/tutorials/using-branches) | Strategy guide |
| Conventional Commits | [Conventional Commits](https://www.conventionalcommits.org/) | Message format |
| Merge Conflicts | [GitHub — Conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts) | Resolution guide |
| Git Rebase | [Atlassian — Rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase) | Alternative to merge |

---

## Key Takeaways

✓ Created and switched between feature branches  
✓ Wrote meaningful commit messages  
✓ Simulated a pull request workflow  
✓ Resolved merge conflicts manually  
✓ Merged feature branches into main  

---

## Next Lesson Preview

**Lesson 19 — Basic Testing for Backend**: We'll write unit and integration tests for the API using xUnit.
