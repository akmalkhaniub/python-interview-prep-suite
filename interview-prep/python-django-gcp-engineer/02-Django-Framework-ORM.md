# Django Framework & ORM Mastery
**Focus:** MTV Architecture, ORM Optimization, Query Sets, Middleware, Migrations

---

## Section A: Django Architecture & Middleware

### Q1: Django's MTV vs Traditional MVC
**Question:** Django uses an MTV architecture. Explain how this differs from traditional MVC and how data flows through a Django request.

**Expected Answer:**
Traditional Model-View-Controller (MVC) architectures designate the "Controller" as the router that handles user input and orchestrates logic between the Model and the View. Django's implementation is slightly different, famously dubbed "Model-Template-View" (MTV). In Django, the framework itself—specifically the URL dispatcher—acts as the implicit Controller. 

When an HTTP request enters a Django application, the URL dispatcher routes it to a specific **View**. In Django, the View is a Python function or class containing the core business logic (equivalent to the Controller in MVC). The View interrogates the **Model** (the Python representation of the database schema via the ORM) to retrieve data. Finally, the View passes that data context to the **Template** (the HTML rendering engine, equivalent to the View in traditional MVC). The Template generates the final HTML payload, which the View returns as an HTTP response to the client.

---

### Q2: Django Middleware
**Question:** What is Django Middleware, and how would you implement custom middleware to track API request latencies?

**Expected Answer:**
Django Middleware is a framework of hooks that wrap around the entire request/response lifecycle. It operates globally, meaning every incoming HTTP request and outgoing HTTP response passes through the middleware stack sequentially. It is the perfect architectural layer for cross-cutting concerns like session management, authentication, CORS headers, or request logging, preventing this logic from duplicating across hundreds of individual views.

To track API request latency, I would create a custom middleware class. In its `__call__` method, the middleware would record `start_time = time.time()` immediately before passing the `request` to the next layer via `self.get_response(request)`. Once the view finishes and returns the `response` object, the middleware calculates the `duration = time.time() - start_time`. It can then log this duration alongside the request path and HTTP status code using Python's `logging` module, which can be scraped by our observability stack (like ELK or Datadog) to monitor application performance seamlessly.

---

## Section B: Advanced Django ORM

### Q3: The N+1 Query Problem
**Question:** What is the N+1 query problem in the Django ORM, and how do you resolve it?

**Expected Answer:**
The N+1 query problem is the most common performance bottleneck in Django applications. It occurs when you query a database for a list of objects (1 query) and then loop through those objects, accessing a related foreign key field on each one. Because Django ORM evaluates relationships lazily, it will issue a brand new `SELECT` statement for every single item in the loop (N queries). If your list has 1,000 items, a simple page load suddenly requires 1,001 database queries, completely crippling performance.

To resolve this, engineers must force the ORM to eagerly fetch the related data in the initial database hit. For `ForeignKey` or `OneToOneField` relationships, I use `select_related()`, which executes a SQL `JOIN` to retrieve the related object in the same query. For `ManyToManyField` or reverse `ForeignKey` relationships, a SQL join would return massive amounts of duplicated data. Instead, I use `prefetch_related()`, which issues exactly two queries—one for the primary objects, and a second for all related objects using a SQL `IN` clause—and performs the joining internally in Python memory, drastically reducing database round-trips.

---

### Q4: High-Traffic ORM Optimization
**Question:** Beyond eager loading, how do you optimize Django ORM queries for a high-traffic production application?

**Expected Answer:**
Optimizing the Django ORM for high traffic requires minimizing memory footprint and reducing database locking. First, if a view only needs a few specific columns (e.g., displaying a list of usernames), I strictly use `.values()` or `.values_list()`. Returning native Python dictionaries or tuples instead of instantiating heavy Django Model instances saves massive amounts of RAM and CPU overhead. If model instances are required but certain large fields (like text blobs) aren't, I use `.only()` or `.defer()` to exclude them from the `SELECT` statement.

For write operations, hitting `.save()` inside a `for` loop is disastrous. I aggressively utilize `bulk_create()` and `bulk_update()` to combine hundreds of operations into a single SQL transaction. When dealing with race conditions in a highly concurrent environment (like inventory deduction), I avoid fetching, calculating, and saving. Instead, I push the calculation down to the database level using `F() expressions` (e.g., `inventory = F('inventory') - 1`), which execute as atomic SQL operations, preventing lost updates without requiring expensive database locks.

---

### Q5: Managing Database Migrations
**Question:** How do you safely handle Django database migrations in a distributed team to prevent merge conflicts and production downtime?

**Expected Answer:**
Database migrations are a critical operational risk. In a distributed team, developers frequently generate conflicting migration files if they branch from the same base schema. When reviewing PRs, I ensure the `makemigrations --merge` command is used correctly, but operationally, I enforce sequential migration numbering via CI/CD checks before code can be merged into `main`.

For production deployment, schema changes must be completely decoupled from application code deployment to achieve zero downtime. I mandate the "Expand and Contract" pattern. For example, if we are renaming a column, the first migration adds the new column. The application is deployed to dual-write to both columns. A data migration (using `RunPython`) backfills the historical data. The application is updated to read exclusively from the new column. Finally, a separate deployment drops the old column. This guarantees that at no point during the rollout will the running application crash due to a missing database field.
