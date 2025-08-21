# 💻 Google Cloud Compute Engine (VMs) – Complete Guide

## 🎯 Goal
By the end of this guide, you will:
- Understand **what Compute Engine is** and why it’s important.  
- Know **every concept** around virtual machines in GCP.  
- Learn how to **create, connect, configure, secure, and automate VMs**.  
- Be able to use **startup scripts, firewall rules, snapshots, and custom images**.  
- Practice **real-world DevOps workflows** with Compute Engine.  

---

## 🧱 What is Compute Engine?

Google Cloud Compute Engine is GCP’s **Infrastructure as a Service (IaaS)** offering that allows you to run **virtual machines (VMs)** on Google’s infrastructure.  

Think of Compute Engine as **Google’s version of AWS EC2** or **Azure VMs**.  

### ✨ Key Benefits:
- **Scalability** – Run 1 VM or 10,000.  
- **Flexibility** – Choose machine type, OS, disk, GPU, networking.  
- **Automation** – Use startup scripts, instance templates, and autoscaling.  
- **Integration** – Works with Cloud Storage, VPC networks, IAM, Kubernetes, etc.  

---

## ⚙️ Core Concepts

| Concept           | Description |
|------------------|-------------|
| **Instance**      | A VM (virtual server) that runs in a GCP zone |
| **Region**        | A geographical location (e.g., `us-central1`) |
| **Zone**          | A single data center in a region (e.g., `us-central1-a`) |
| **Machine Type**  | Defines CPU, RAM (e.g., `e2-micro`, `n1-standard-1`) |
| **Boot Disk**     | Persistent disk attached to VM with OS |
| **Metadata**      | Key-value data attached to instances (used by scripts) |
| **Startup Script**| Script that runs automatically when VM starts |
| **Firewall Rule** | Controls allowed traffic (SSH, HTTP, HTTPS) |
| **Instance Template** | Blueprint for creating multiple identical VMs |
| **Managed Instance Group (MIG)** | Auto-scaling group of identical VMs |
| **Image**         | Base OS (Ubuntu, Debian, Windows, Custom) |
| **Snapshot**      | Point-in-time backup of a disk |

---

## 🛠️ Hands-On Guide

### 1️⃣ Create a VM and Connect via SSH

```bash
gcloud compute instances create devops-vm   --zone=us-central1-a   --machine-type=e2-micro   --image-family=ubuntu-2004-lts   --image-project=ubuntu-os-cloud   --tags=http-server

# SSH into the VM
gcloud compute ssh devops-vm --zone=us-central1-a
```

---

### 2️⃣ Install NGINX Manually

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

Test:
```bash
curl http://localhost
```

---

### 3️⃣ Automate with Startup Script

```bash
gcloud compute instances create nginx-vm   --zone=us-central1-a   --machine-type=e2-micro   --image-family=ubuntu-2004-lts   --image-project=ubuntu-os-cloud   --metadata startup-script='#! /bin/bash
apt update
apt install -y nginx
systemctl enable nginx
systemctl start nginx'   --tags=http-server
```

---

### 4️⃣ Enable Firewall Rules

```bash
gcloud compute firewall-rules create allow-http   --allow tcp:80   --target-tags=http-server   --description="Allow HTTP traffic"   --direction=INGRESS   --priority=1000   --network=default
```

---

### 5️⃣ Create a Custom Image

```bash
# Stop the VM before creating image
gcloud compute instances stop nginx-vm --zone=us-central1-a

# Create custom image
gcloud compute images create nginx-custom-image   --source-disk=nginx-vm   --source-disk-zone=us-central1-a

# Launch VM from custom image
gcloud compute instances create nginx-from-image   --zone=us-central1-a   --machine-type=e2-micro   --image=nginx-custom-image   --tags=http-server
```

---

### 6️⃣ Snapshots and Backups

```bash
# Create snapshot of disk
gcloud compute disks snapshot nginx-vm   --snapshot-names=nginx-snapshot

# Create VM from snapshot
gcloud compute disks create disk-from-snapshot   --source-snapshot=nginx-snapshot   --zone=us-central1-a
```

---

### 7️⃣ Instance Templates & Autoscaling

```bash
# Create instance template
gcloud compute instance-templates create nginx-template   --machine-type=e2-micro   --tags=http-server   --metadata startup-script='#! /bin/bash
apt update
apt install -y nginx
systemctl start nginx'

# Create managed instance group with autoscaling
gcloud compute instance-groups managed create nginx-group   --base-instance-name=nginx   --template=nginx-template   --size=2   --zone=us-central1-a

# Enable autoscaling
gcloud compute instance-groups managed set-autoscaling nginx-group   --zone=us-central1-a   --max-num-replicas=5   --target-cpu-utilization=0.6
```

---

## 🧠 Real-World DevOps Scenarios

- **Scenario 1: Web App Hosting**  
  - Launch VM with startup script installing NGINX + App.  
  - Enable HTTP/HTTPS firewall rules.  
  - Use custom images for CI/CD.  

- **Scenario 2: Auto-Healing Services**  
  - Use Managed Instance Groups with health checks.  
  - Configure autoscaling based on CPU load.  

- **Scenario 3: Disaster Recovery**  
  - Use snapshots for backup/restore.  
  - Create VMs in different regions.  

---

## ✅ Key Takeaways

- Compute Engine is GCP’s IaaS for **running VMs**.  
- Use **startup scripts** for automation.  
- Configure **firewall rules** for external access.  
- Create **custom images** for standardized environments.  
- Use **snapshots** for backups and recovery.  
- Scale with **instance groups and autoscaling**.  
- Manage access with **IAM roles** (e.g., Compute Admin).  

---

## 📚 Next Steps

- Learn **VPC Networking** for custom subnets & routing.  
- Explore **Terraform** for Infrastructure as Code (IaC).  
- Integrate with **Cloud Monitoring** for metrics & alerts.  
- Move to **GKE (Kubernetes)** for container orchestration.  
