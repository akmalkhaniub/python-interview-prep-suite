# Git Fluency & Advanced Workflows
**Focus:** Rebase vs. Merge, Git Bisect, Clean Pull Requests, Branching Strategy

---

## Section A: Advanced Git Commands

### Q1: Git Rebase vs. Git Merge
**Question:** When and why would you use `git rebase` instead of `git merge` in a collaborative team environment?

**Expected Answer:**
Both `git merge` and `git rebase` solve the same problem: integrating upstream changes from the `main` branch into a local feature branch. 

Using `git merge main` creates a new "Merge Commit" in the feature branch history. In a fast-moving team, repeatedly merging `main` into a long-lived feature branch clutters the Git history with dozens of meaningless merge commits, creating a "train tracks" visual mess that is incredibly difficult to read. 

I mandate the use of `git rebase main` for updating local branches. Rebasing fundamentally rewrites history. It takes the commits on the feature branch, sets them aside, updates the branch pointer to the absolute tip of `main`, and then sequentially reapplies the feature commits on top. This results in a perfectly clean, linear, straight-line Git history. However, the golden rule of rebasing is: **Never rebase public, shared commits.** Because rebasing rewrites history (changing commit SHAs), rebasing a branch that another developer has already pulled will cause catastrophic synchronization conflicts. Rebasing is strictly for cleaning up local, unmerged feature branches.

### Q2: Debugging with Git Bisect
**Question:** A subtle bug was introduced into the `main` branch 50 commits ago, and it's unclear which commit caused it. How do you use `git bisect` to find the root cause?

**Expected Answer:**
Manually checking out 50 individual commits to find a bug is an agonizing waste of engineering hours. `git bisect` automates this process using a binary search algorithm, finding the exact offending commit in roughly 5 or 6 steps.

I initiate the process with `git bisect start`. I flag the current broken state with `git bisect bad`. Then, I check out an older commit where I know the feature worked perfectly, and flag it with `git bisect good`. Git immediately checks out a commit exactly in the middle of that 50-commit range. I run my tests or refresh the browser. If the bug is present, I type `git bisect bad`; if it works, I type `git bisect good`. Git halves the remaining commits again and again. Within minutes, Git pinpoints the exact commit (and author) that introduced the regression, allowing me to view the specific diff and rapidly understand why the bug occurred.

---

## Section B: Team Collaboration & Pull Requests

### Q3: Structuring a Clean Pull Request
**Question:** What constitutes a "clean, reviewable pull request," and how do you structure your Git commits to facilitate this?

**Expected Answer:**
A massive Pull Request containing 50 changed files and a single commit message like `fixed stuff` is impossible to review effectively, practically guaranteeing that bugs will slip into production. A clean PR tells a coherent, linear story.

To facilitate this, I embrace **Atomic Commits**. Each commit should represent a single, logical change that leaves the codebase in a functioning state. For example: Commit 1 adds the database migration, Commit 2 updates the backend service logic, and Commit 3 implements the React UI. I utilize **Semantic Commit Messages** (e.g., `feat: add rota generation`, `fix: correct payroll calculation`) to provide immediate context. The PR description itself must explicitly state the "Why" behind the architectural choices, link to the relevant Jira ticket, and include screenshots or screen recordings of the UI changes to drastically reduce the cognitive load on the code reviewer.

### Q4: Handling Messy Feature Branches
**Question:** Over a week of development, your local feature branch has accumulated 20 messy "work in progress" (WIP) commits. How do you clean this up before submitting it for a code review?

**Expected Answer:**
Before opening a Pull Request, I am responsible for curating my Git history. Submitting 20 WIP commits (`wip`, `typo fix`, `trying again`) is highly unprofessional. I resolve this by performing an **Interactive Rebase** (`git rebase -i HEAD~20`).

The interactive rebase opens an editor allowing me to manipulate the past 20 commits. I use the `squash` (or `fixup`) command to melt all the minor, intermediary WIP commits down into a single, cohesive, atomic commit. I can also use the `reword` command to rewrite poor commit messages into detailed, professional descriptions. After crafting the perfect, linear story of my feature development, I `git push --force-with-lease` the rewritten branch to the remote repository and open the clean Pull Request for my peers.

### Q5: Git Tags and CI/CD Versioning
**Question:** Describe how you use Git tags and semantic versioning to drive a CI/CD release process.

**Expected Answer:**
In a mature DevOps culture, deployments should not occur manually from developers' laptops; they should be triggered automatically by Git state. I use **Git Tags** following Semantic Versioning (SemVer) principles (e.g., `v1.4.2`) to manage production releases immutably.

When a release is ready, the team creates and pushes a specific Git Tag to the repository. The CI/CD pipeline is configured to listen specifically for tag push events. When it detects the `v1.4.2` tag, the pipeline automatically builds the production Docker image, executes the final E2E test suite, and if successful, triggers the automated deployment to the AWS ECS production cluster. This ensures that every production release is irrevocably tied to a specific point in Git history, making immediate rollbacks to `v1.4.1` trivial if an incident occurs.
