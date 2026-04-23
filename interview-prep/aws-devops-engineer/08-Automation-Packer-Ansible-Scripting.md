# Automation: Packer, Ansible & Scripting

**Focus:** AMI Pipelines, Configuration Management, Bash/Python/PowerShell

---

## Section A: Packer & AMI Management

### Q1: Golden AMI Pipeline
**Question:** Describe your process for building and maintaining a "Golden AMI."

**Expected Answer:**
- **Base image**: Official Amazon Linux 2023 or Ubuntu AMI.
- **Packer template**: HCL2 format. Source → Provisioners (shell, Ansible) → Post-processors.
- **Hardening**: CIS benchmark, remove unnecessary packages, configure SSH, install CloudWatch agent.
- **Pipeline**: GitLab CI triggers weekly. Test AMI (launch, run Inspec/Goss tests). Promote.
- **Versioning**: Tag with build date, commit SHA. Clean up AMIs older than 90 days.

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
- **Immutable infrastructure**: Don't patch running instances. Build new AMI → rolling replacement.
- **ASG**: Update Launch Template with new AMI → Instance Refresh.
- **EKS**: Update node group AMI → managed rolling replacement.
- **Automation**: Weekly Packer build → Terraform auto-updates AMI var → MR for review.
- **Compliance**: AWS Systems Manager Patch Manager for tracking.

---

## Section B: Ansible

### Q3: Ansible vs. Baking
**Question:** When do you use Ansible at runtime vs. baking everything into AMI/Docker?

**Expected Answer:**
- **Bake**: Faster boot, deterministic, no external deps. Preferred for containers and immutable infra.
- **Runtime Ansible**: Environment-specific config (secrets, endpoints), dynamic inventory, legacy systems.
- **Hybrid**: Bake 90% into AMI. Use Ansible/User Data for the last 10%.
- **Containers**: Almost everything baked into Docker image. Runtime config via env vars.

---

### Q4: Ansible Best Practices
**Question:** How do you structure Ansible for managing both Linux and Windows servers?

**Expected Answer:**
- **Roles**: Per concern (`common`, `hardening`, `monitoring`, `web-server`).
- **Inventory**: Dynamic inventory plugin for AWS (`aws_ec2`). Group by tags.
- **Variables**: `group_vars/linux.yml`, `group_vars/windows.yml`.
- **Windows**: `win_*` modules. Connection via WinRM. `ansible_connection: winrm`.
- **Idempotency**: Every task safe to run multiple times.
- **Testing**: Molecule for role testing. `ansible-lint` for linting.
- **Vault**: `ansible-vault` for encrypting sensitive variables.

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
- **Python**: Cross-platform. `boto3`, `paramiko`, `pywinrm`. Best for complex logic.
- **Ansible**: Abstracts OS differences via modules (`package` vs `win_package`).
- **AWS SSM Run Command**: Execute on any managed instance. Supports Bash AND PowerShell.
- **Testing**: CI matrix to test on both Amazon Linux and Windows Server.
