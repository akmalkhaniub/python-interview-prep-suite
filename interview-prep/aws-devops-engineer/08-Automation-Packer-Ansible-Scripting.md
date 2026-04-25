# Automation: Packer, Ansible & Scripting

**Focus:** AMI Pipelines, Configuration Management, Bash/Python/PowerShell

---

## Section A: Packer & AMI Management

### Q1: Golden AMI Pipeline
**Question:** Describe your process for building and maintaining a "Golden AMI."

**Expected Answer:**
Building a "Golden AMI" pipeline is foundational for secure, immutable infrastructure. The process starts with a HashiCorp Packer template written in modern HCL2. The `source` block defines the absolute base image, strictly pulling the official, most recent Amazon Linux 2023 or Ubuntu AMI published by the vendor. During the `build` phase, Packer spins up a temporary EC2 instance and triggers provisioners. I heavily rely on the Ansible provisioner to execute strict CIS benchmark hardening, uninstall unnecessary packages, lockdown SSH configurations, and pre-install mandatory observability tools like the CloudWatch and SSM agents.

This entire process must be automated via a CI/CD pipeline (e.g., GitLab CI) that runs on a weekly schedule. Crucially, before the new AMI is promoted, the pipeline must launch a test instance from it and execute compliance verification using tools like Inspec or Goss. If the tests pass, the AMI is tagged with immutable metadata—including the build date, Git commit SHA, and pipeline ID—for strict auditability. Finally, a post-processor or lambda function must clean up legacy AMIs older than 90 days to prevent exorbitant EBS snapshot storage costs.

```hcl
source "amazon-ebs" "golden" {
  ami_name      = "golden-{{timestamp}}"
  instance_type = "t3.medium"
  region        = "us-east-1"
  source_ami_filter {
    filters = {
      name = "al2023-ami-*-x86_64"
    }
    owners      = ["amazon"]
    most_recent = true
  }
  ssh_username = "ec2-user"
}

build {
  sources = ["source.amazon-ebs.golden"]
  provisioner "ansible" {
    playbook_file = "./playbooks/harden.yml"
  }
}
```

---

### Q2: AMI Patching Strategy
**Question:** How do you ensure all running EC2 instances use the latest patched AMI?

**Expected Answer:**
Ensuring all EC2 instances are running the latest patched AMI relies entirely on the philosophy of immutable infrastructure. You should never SSH into running instances to run `yum update` or apply patches manually, as this creates configuration drift and snowflake servers. Instead, when a vulnerability is announced or a routine patch is required, the Packer pipeline builds an entirely new Golden AMI from scratch.

Once the new AMI is published, the deployment process involves updating the AWS Launch Template with the new AMI ID. If the instances are part of an Auto Scaling Group (ASG), I trigger an ASG "Instance Refresh," which gracefully terminates old instances and provisions new ones in a rolling fashion, ensuring zero downtime. For EKS clusters, updating the Managed Node Group's AMI triggers a similar, native rolling replacement. To tie this all together, I automate the pipeline so that a successful Packer build automatically opens a Terraform Merge Request containing the updated AMI ID, ensuring human review before the fleet is recycled. For compliance auditing, I utilize AWS Systems Manager Patch Manager to continually report on the fleet's patch compliance status.

---

## Section B: Ansible

### Q3: Ansible vs. Baking
**Question:** When do you use Ansible at runtime vs. baking everything into AMI/Docker?

**Expected Answer:**
The decision to bake configuration directly into an AMI versus applying it at runtime using Ansible depends on scaling speed and environment parity. "Baking" means pre-installing dependencies and configurations via Packer. This is highly deterministic, removes reliance on external package repositories during boot, and drastically reduces EC2 startup times—making it absolutely essential for rapidly scaling Auto Scaling Groups.

Conversely, running Ansible at runtime (often via EC2 User Data) is necessary for environment-specific configurations that cannot be baked in, such as injecting dynamic database endpoints, attaching environment-specific IAM roles, or retrieving runtime secrets. In practice, I implement a hybrid approach: I bake 90% of the heavy dependencies (interpreters, monitoring agents, security hardening) deeply into the AMI. The remaining 10%—the environment-specific glue—is injected dynamically at boot via lightweight User Data scripts. For containerized environments like EKS, this paradigm shifts entirely toward baking; the Docker image must be 100% immutable, with all environment differences passed strictly as runtime environment variables.

---

### Q4: Ansible Best Practices
**Question:** How do you structure Ansible for managing both Linux and Windows servers?

**Expected Answer:**
Structuring a large-scale Ansible repository to manage disparate operating systems requires strict modularity. I break all logic down into distinct Ansible Roles based on separation of concerns (e.g., `role-hardening`, `role-monitoring`, `role-webserver`), ensuring code is highly reusable. To manage AWS environments, hardcoded inventories are useless; I strictly utilize the `aws_ec2` dynamic inventory plugin, which automatically groups EC2 instances based on their AWS tags (e.g., mapping tags to `group_vars/linux.yml` or `group_vars/windows.yml`).

When targeting Windows instances, Ansible abstracts the OS differences, but you must utilize specific `win_*` modules (like `win_feature` or `win_service`) and configure the transport connection to use WinRM (`ansible_connection: winrm`) rather than SSH. The most critical best practice across both OSs is enforcing idempotency—every task must be written so that running the playbook 100 times yields the exact same system state without failing or making unnecessary changes. Finally, I mandate the use of `ansible-lint` for code quality, Molecule for automated role testing in CI, and `ansible-vault` to ensure no plaintext secrets are committed to the repository.

---

## Section C: Scripting Challenges

### Q5: Bash — Log Analysis
**Question:** Find the top 10 IP addresses by request count from an Nginx access log.

```bash
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10
```

**Follow-up:** Alert if any IP exceeds 10,000 requests in 5 minutes? → `fail2ban`, CloudWatch custom metric, or WAF rate-based rules.

---

### Q6: Python — AWS Resource Cleanup
**Question:** Find and delete unattached EBS volumes older than 30 days.

```python
import boto3
from datetime import datetime, timezone, timedelta

ec2 = boto3.client('ec2')
cutoff = datetime.now(timezone.utc) - timedelta(days=30)

volumes = ec2.describe_volumes(
    Filters=[{'Name': 'status', 'Values': ['available']}]
)['Volumes']

for vol in volumes:
    if vol['CreateTime'] < cutoff:
        print(f"Deleting {vol['VolumeId']} created {vol['CreateTime']}")
        # ec2.delete_volume(VolumeId=vol['VolumeId'])  # Uncomment to execute
```

**Follow-up:** Make it safe? → DRY-RUN mode, check `DoNotDelete` tag, SNS notification before deletion.

---

### Q7: PowerShell — Disk Space Monitoring
**Question:** Check disk space and alert if any drive is over 90% full.

```powershell
$drives = Get-WmiObject Win32_LogicalDisk -Filter "DriveType=3"
foreach ($drive in $drives) {
    $usedPercent = [math]::Round(($drive.Size - $drive.FreeSpace) / $drive.Size * 100, 2)
    if ($usedPercent -gt 90) {
        Write-Warning "$($drive.DeviceID) is $usedPercent% full!"
        aws cloudwatch put-metric-data `
            --namespace "Custom/DiskSpace" `
            --metric-name "DiskUsedPercent" `
            --dimensions "Drive=$($drive.DeviceID)" `
            --value $usedPercent
    }
}
```

---

### Q8: Cross-Platform Scripting
**Question:** How do you write automation that works across Linux and Windows?

**Expected Answer:**
Writing automation that seamlessly targets both Linux and Windows fleets requires elevating the logic above OS-specific shell scripts. I primarily rely on Python because it is inherently cross-platform and natively supported by the AWS `boto3` SDK. Python can easily branch logic based on the target OS, and libraries like `paramiko` (for SSH) or `pywinrm` allow remote execution regardless of the target platform, making it the best choice for complex data manipulation or API integrations.

For configuration management, Ansible is unparalleled because its modules abstract the underlying OS commands entirely. While you occasionally have to branch between `package` and `win_package`, the overarching YAML syntax remains identical. However, for operational incident response, my preferred tool is AWS Systems Manager (SSM) Run Command. It eliminates the need for jump hosts or cross-platform connection handling entirely. You simply pass an SSM document containing Bash (for Linux) or PowerShell (for Windows), and the local SSM agent safely executes the script on the target instance. To ensure reliability, all cross-platform automation must be rigorously validated in a CI pipeline using a matrix strategy to test execution against both OS families simultaneously.
