# CI/CD, Git & Cloud Services

**Focus:** Git collaboration, Continuous Integration/Deployment, Cloud basics

---

### Q1: Git Branching & Collaboration
**Question:** Explain your typical Git workflow when working on a new feature in a team setting.

**Expected Answer:**
- **Branching**: Branch off the main development branch (e.g., `git checkout -b feature/user-authentication`).
- **Committing**: Make small, logical commits with clear, descriptive conventional commit messages (e.g., `feat: add JWT login endpoint`).
- **Syncing**: Regularly pull from the main branch to resolve conflicts early (`git pull origin main --rebase`).
- **Pull Request (PR)**: Push the feature branch to remote and open a PR.
- **Code Review**: Address comments, make fixes, and squash commits if necessary to keep history clean.
- **Merging**: Merge into main (using squash-merge or merge-commit based on team policy) once CI checks pass and approvals are granted.

---

### Q2: Resolving Git Conflicts
**Question:** You try to merge the `main` branch into your feature branch and encounter a massive merge conflict. How do you handle it?

**Expected Answer:**
- **Don't panic**: Abort the merge if needed (`git merge --abort`) to assess the situation.
- **Communication**: If the conflict involves code I am not familiar with, I will reach out to the developer who wrote the conflicting code to understand their intent.
- **Resolution**: Open the files in VS Code (or a merge tool), review the incoming vs. current changes, and manually select the correct code.
- **Testing**: After resolving, immediately run the unit tests and start the app locally to ensure the resolution didn't break functionality.
- **Commit**: Commit the resolved files.

---

### Q3: CI/CD Pipeline Architecture
**Question:** The job description mentions contributing to the CI/CD process. What should an ideal CI/CD pipeline for a React/Node.js stack look like?

**Expected Answer:**
- **Continuous Integration (CI)**: Runs automatically on every Pull Request.
  - **Linting & Formatting**: Run ESLint and Prettier.
  - **Type Checking**: Run `tsc --noEmit` if using TypeScript.
  - **Testing**: Run unit and integration tests (Jest/Mocha).
  - **Security Scan**: Run `npm audit` or tools like Snyk to check for vulnerable dependencies.
  - *Goal*: Block the PR from being merged if any of these fail.
- **Continuous Deployment (CD)**: Runs automatically when code is merged into `main`.
  - **Build**: Compile React app (`npm run build`) and transpile Node.js code if needed.
  - **Containerization**: Build a Docker image and push it to a registry (eCR, Docker Hub).
  - **Deployment**: Deploy the new image to the staging/production environment (ECS, Kubernetes, Vercel, Heroku).
  - **Migration**: Run database migrations automatically before the new app code starts.

---

### Q4: Cloud Services (AWS / General)
**Question:** As a Full Stack developer, how do you typically interact with Cloud Services when deploying or architecting applications?

**Expected Answer:**
- **Frontend Hosting**: Using services like AWS S3 + CloudFront (CDN), Vercel, or Netlify to host static React assets for global, low-latency delivery.
- **Backend Hosting**: Deploying Node.js APIs on AWS EC2, Elastic Beanstalk, or containerized via ECS/Fargate.
- **Managed Databases**: Using AWS RDS (for PostgreSQL/MySQL) or MongoDB Atlas rather than managing database servers manually. Ensures automated backups, scaling, and high availability.
- **Object Storage**: Storing user-uploaded files (images, PDFs) directly in AWS S3 and saving the URL in the database, rather than storing binaries in the DB or the local file system.

---

### Q5: Twelve-Factor App Methodology
**Question:** Have you heard of the Twelve-Factor App methodology? How do you manage configuration/environment variables in your Node.js apps?

**Expected Answer:**
- **Principle**: Configuration that varies between environments (dev, staging, prod) should be stored in the environment, not in the codebase.
- **Implementation**:
  - Never hardcode database URIs or API keys in code.
  - Exclude `.env` files from version control (`.gitignore`).
  - Use `process.env.DB_URI` in Node.js.
  - Use cloud secret managers (AWS Secrets Manager, GitHub Secrets) to inject these values into the CI/CD pipeline and production environment.
