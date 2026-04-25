# Docker Containerization
**Focus:** Multi-Stage Builds, Docker Compose, Optimization, Security

---

## Section A: Docker Fundamentals & Builds

### Q1: Multi-Stage Docker Builds for Next.js
**Question:** Walk me through writing a Dockerfile using multi-stage builds for a Next.js application. Why is this approach necessary?

**Expected Answer:**
A naive single-stage Dockerfile copies the entire source code, installs all dependencies (including heavy development tools), builds the app, and runs it. This results in massive, bloated images (often >1GB) that are slow to pull and contain numerous security vulnerabilities from unnecessary tooling.

A **Multi-Stage Build** solves this by separating the build environment from the runtime environment. 
- **Stage 1 (Deps):** I start with a `node:alpine` image, copy only the `package.json` and `package-lock.json`, and run `npm ci` to install all dependencies cleanly, leveraging Docker's layer caching to speed up subsequent builds.
- **Stage 2 (Builder):** I copy the installed `node_modules` and the rest of the application code, then execute `npm run build` to generate the optimized Next.js `.next` folder.
- **Stage 3 (Runner):** I start completely fresh with a minimal `node:alpine` base image. I only copy over the essential production runtime files from the Builder stage: the `.next/standalone` directory, the `public` folder, and the production `node_modules`. I then define `CMD ["node", "server.js"]`. The final image is significantly smaller, faster to deploy, and radically more secure because all build-time source code and dev-dependencies are left behind.

### Q2: Docker CMD vs. ENTRYPOINT
**Question:** Explain the difference between `CMD` and `ENTRYPOINT` in a Dockerfile, and when you would use each.

**Expected Answer:**
Both instructions define what executable should run when a container starts, but they behave differently when a user attempts to override the command at runtime.

`ENTRYPOINT` is designed to be the immutable executable of the container. If you define `ENTRYPOINT ["npm", "start"]`, the container will always run `npm start`. If a user runs `docker run my-image --port 8080`, the `--port 8080` string is appended as an argument to the Entrypoint, resulting in `npm start --port 8080`.

`CMD`, however, provides default arguments or a default command that is easily overridden. If you define `CMD ["node", "server.js"]` and a user runs `docker run my-image bash`, the `bash` command entirely replaces the `CMD`, and the container drops into a shell instead of starting the server. A common best practice is to use them together: define the executable as the `ENTRYPOINT` (e.g., `["node"]`) and pass the default script as the `CMD` (e.g., `["server.js"]`), allowing users to easily swap the script being executed without changing the underlying runtime engine.

---

## Section B: Local Development & Optimization

### Q3: Local Development with Docker Compose
**Question:** How do you structure a `docker-compose.yml` file to replicate a complex AWS production environment on your local machine?

**Expected Answer:**
`docker-compose` is essential for eliminating the "it works on my machine" problem by locally orchestrating the entire microservices stack. To replicate a production environment, I define distinct services in the `docker-compose.yml`.

I define a `postgres` service using the official Postgres image, configuring local volume mounts so database data survives container restarts. I define a `redis` service for caching and Celery queues. For the Node.js backend and Next.js frontend, I map my local source code directories into the containers using Docker Volumes (`- ./:/app`). This allows for hot-reloading: when I save a file on my host machine, the change instantly reflects inside the running container without requiring a full image rebuild. Finally, I use Docker Compose networks to allow the backend to connect to the database via a simple DNS alias (e.g., `postgres:5432`) precisely mirroring how ECS containers communicate within an AWS VPC.

### Q4: Optimizing Image Size and Build Caching
**Question:** How do you optimize Docker image size and leverage build caching to speed up CI/CD pipelines?

**Expected Answer:**
Optimizing Docker builds is fundamentally about manipulating Docker's layer cache. Docker builds images sequentially line-by-line; if a line changes, that layer and *every subsequent layer* must be rebuilt from scratch. 

To exploit this, I order instructions strictly from "least frequently changed" to "most frequently changed." I copy only the `package.json` and `package-lock.json` first, and run `npm ci`. I only copy the rest of the application source code *after* this step. Because source code changes constantly but dependencies change rarely, Docker will cache the heavy `npm ci` layer and skip it on 95% of CI builds, dropping build times from minutes to seconds. Furthermore, I aggressively use a `.dockerignore` file to exclude massive local directories like `node_modules/`, `.git/`, and build artifacts from ever being sent to the Docker daemon, significantly reducing the build context payload and final image size.

### Q5: Docker Security Best Practices
**Question:** What are the critical security best practices when building Docker images for a regulated healthcare application?

**Expected Answer:**
Container security in a healthcare domain requires defending against container breakouts and supply chain attacks. First, I never run containers as the `root` user. In the Dockerfile, I explicitly create a dedicated user and switch to it via the `USER node` directive. If an attacker compromises the Node.js process, they are trapped as an unprivileged user, making it much harder to escalate privileges to the host EC2 instance.

Second, I base images exclusively on minimal, hardened distributions like Alpine Linux or distroless images, drastically reducing the available attack surface (fewer installed binaries means fewer potential vulnerabilities). I avoid generic tags like `node:latest`, instead pinning to strict, immutable SHA-256 digests (`node@sha256:...`) to prevent upstream poisoning attacks. Finally, I integrate automated container scanning tools (like Trivy or AWS ECR basic scanning) directly into the CI/CD pipeline to block any deployment if the base image contains known high-severity CVEs.
