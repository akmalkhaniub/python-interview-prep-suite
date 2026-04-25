# GCP Platform & Production Infrastructure
**Focus:** Google Cloud Architecture, Linux Troubleshooting, Deployment, Scalability

---

## Section A: Google Cloud Platform (GCP)

### Q1: High-Availability Django Architecture on GCP
**Question:** Design a highly available, scalable architecture for a Django web application deployed on Google Cloud Platform.

**Expected Answer:**
To build a scalable architecture on GCP, I would containerize the Django application using Docker and deploy it to **Google Kubernetes Engine (GKE)** or **Cloud Run** if a fully serverless abstraction is preferred. Using GKE, I would configure a multi-zonal cluster to ensure compute nodes survive localized availability zone failures. The application would sit behind a Global External HTTP(S) Load Balancer, which natively integrates with Cloud CDN to cache static and media assets globally, and Cloud Armor to protect against DDoS and SQL injection attacks.

For the database layer, I would rely on **Cloud SQL for PostgreSQL/MySQL**, configuring it for High Availability (HA) which automatically provisions a synchronous standby replica in a different zone for immediate failover. Static files and user-uploaded media (handled by `django-storages`) would be routed to **Cloud Storage (GCS)** buckets. To handle asynchronous background tasks like email processing, I would utilize **Cloud Pub/Sub** to queue messages, consumed by dedicated Celery worker pods running on GKE, backed by a **Memorystore for Redis** instance serving as both the Celery broker and the Django caching backend.

---

### Q2: GCP Database & Media Scaling
**Question:** As user traffic grows exponentially, how do you scale the database and media storage layers using GCP services?

**Expected Answer:**
Media storage on GCP scales infinitely and frictionlessly. By configuring Django to utilize **Google Cloud Storage (GCS)**, we offload all static and media file I/O from the compute instances. GCS provides petabyte-scale storage, and by pairing it with Cloud CDN, we ensure massive spikes in media requests are served directly from global edge caches, virtually eliminating load on our backend.

Database scaling is significantly more complex. In **Cloud SQL**, I would first scale vertically, dynamically allocating more vCPUs and RAM to the primary instance. When vertical scaling hits cost or hardware ceilings, I would scale horizontally for read operations by deploying Cloud SQL Read Replicas, explicitly routing Django's `SELECT` queries to the replicas using database routers. If the application requires massive, global horizontal scaling for both reads *and* writes, I would architect a migration from Cloud SQL to **Cloud Spanner**, Google's globally distributed, strongly consistent relational database, which scales horizontally seamlessly without requiring application-level sharding logic.

---

## Section B: Linux Production Infrastructure

### Q3: Maintaining Linux Production Environments
**Question:** What are the key practices for maintaining and monitoring a production Linux infrastructure running web applications?

**Expected Answer:**
Maintaining production Linux infrastructure requires strict adherence to immutable infrastructure and proactive observability. Servers should never be "snowflakes" manually configured via SSH. Instead, I use infrastructure-as-code (Terraform) and configuration management (Ansible) to provision environments identically. Security is enforced through rigorous OS patching protocols, strict `iptables`/`ufw` firewall rules blocking all ports except 80/443, and the enforcement of key-based SSH access with Fail2Ban.

For observability, I deploy centralized agents. `systemd` manages our web servers (like Gunicorn) and background workers, ensuring they auto-restart upon failure. Standard output and system logs (like `/var/log/syslog` or `journalctl`) are forwarded by Fluentd to a centralized logging platform like ELK or GCP Cloud Logging. Finally, I monitor host-level metrics (CPU, RAM, Disk I/O) using Prometheus Node Exporter, feeding into Grafana to trigger alerts before critical thresholds—like disk space exhaustion—cause an application outage.

---

### Q4: Troubleshooting High CPU Utilization
**Question:** You receive an alert that a production Linux server's CPU is pegged at 100%. Walk me through your troubleshooting steps.

**Expected Answer:**
My first action is to connect to the server and identify the offending process using interactive tools like `htop` or `top`. If the culprit is the Python/Gunicorn process, I need to determine if it's struggling with legitimately high traffic or caught in an infinite loop. I analyze the application access logs to check the current Requests Per Second (RPS) and error rates. If traffic is normal but the CPU is spiked, I use `strace` to trace system calls or `py-spy` to generate a real-time flame graph of the running Python process, instantly revealing exactly which function or library is monopolizing the CPU.

If the culprit is *not* the application—for instance, if `kswapd0` is consuming the CPU—it indicates the server has exhausted its physical RAM and is aggressively "thrashing" the swap space on the hard drive, destroying performance. In that scenario, I pivot to memory troubleshooting using `free -m` and `dmesg` to check for Out Of Memory (OOM) killer invocations. The ultimate resolution relies on mitigation first: gracefully restarting the bogged-down service or scaling out new instances behind the load balancer, followed by a deeper root-cause analysis of the identified code path or memory leak.

---

### Q5: Securing the Deployment Pipeline
**Question:** How do you securely manage environment variables, database passwords, and API keys in a production deployment?

**Expected Answer:**
Committing secrets to version control is a catastrophic security violation. In a modern infrastructure, secrets must be decoupled entirely from the application codebase. In GCP, I utilize **Google Cloud Secret Manager**. Passwords, API keys, and TLS certificates are encrypted and stored centrally, protected by strict IAM policies.

During deployment, the application does not have hardcoded access. Instead, if running on GKE, I use Workload Identity to grant the specific Kubernetes Service Account temporary, scoped permissions to access Secret Manager. The deployment pipeline injects these secrets into the container environment natively at runtime, or the application code uses the GCP SDK to fetch them dynamically upon startup. This ensures developers never touch production credentials, and allows for automated credential rotation policies without requiring code deployments.
