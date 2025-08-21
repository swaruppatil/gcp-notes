# 💻 Google Cloud Compute Engine (VMs) — Beginner-Friendly Guide

This README covers the **most essential concepts** you need to understand before working with VMs in GCP.  
We’ll go step by step from basics to deploying a real application (NGINX) on a VM.

---

## 🌍 Regions and Zones

- **Region**: A geographical area where Google Cloud resources live. Example: `us-central1` (Iowa, USA).
- **Zone**: A single data center inside a region. Example: `us-central1-a`, `us-central1-b`.

👉 **Why they matter**:  
- If a **zone** fails (e.g., power outage), your VM in that zone will be unavailable.  
- Deploying across **multiple zones** or **regions** increases reliability.  
- Choose the **region closest to your users** for low latency.

---

## 🖥️ Compute Engine (Virtual Machines)

- **Compute Engine** is Google Cloud’s **IaaS (Infrastructure as a Service)** that provides **virtual machines**.  
- A **VM (Virtual Machine)** is like a computer running inside Google’s data center.  
- You control the OS, software, and networking of the VM.  
- Similar to AWS EC2 or Azure Virtual Machines.

---

## ⚙️ Machine Families (Types of VMs)

When creating a VM, you pick a **machine family** depending on workload:

1. **General Purpose** (E2, N2, Tau)  
   - Balanced CPU & RAM.  
   - Best for web servers, small apps, dev environments.  

2. **Compute Optimized** (C2, C3)  
   - High-performance CPUs.  
   - Best for analytics, gaming servers, scientific computing.  

3. **Memory Optimized** (M2, M3)  
   - Very high RAM (hundreds of GBs).  
   - Best for databases, in-memory caches.  

👉 Start with **General Purpose** (e.g., `e2-micro`) for learning.

---

## 💽 Disks and Storage

- **Boot Disk**: The disk with the operating system (Ubuntu, Debian, Windows). Required for every VM.  
- **Additional Data Disks**: Extra persistent disks you can attach for app data.  

Types of storage:
- **Standard (HDD)** – cheap, slow, for backups/logs.  
- **Balanced (SSD-lite)** – good balance of price and performance (default).  
- **SSD** – high performance, databases or heavy I/O apps.  
- **Local SSD** – extremely fast, but **data is lost if VM stops**.  

👉 Best practice: Use **Balanced SSD** for most workloads.

---

## 🚀 Creating a VM

### Using the GCP Console
1. Go to **Compute Engine → VM instances → Create Instance**.  
2. Pick **Region & Zone**.  
3. Select a **Machine type** (start with `e2-micro`).  
4. Choose **Boot disk** (Ubuntu LTS is common).  
5. Check firewall boxes: **Allow HTTP/HTTPS traffic**.  
6. Click **Create**.  

### Using `gcloud` CLI
```bash
gcloud compute instances create my-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=ubuntu-2004-lts \
  --image-project=ubuntu-os-cloud \
  --tags=http-server


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
