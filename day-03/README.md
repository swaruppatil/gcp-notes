# 🔐 GCP IAM Fundamentals for Beginners  

## 🧭 What is IAM?  

IAM (**Identity and Access Management**) in GCP is a **security framework** that defines:  

- **Who** (identity)  
- **Can do what** (role/permission)  
- **On which resource** (GCP object like VM, bucket, DB)  

👉 Formula: **Who → Can do what → On which resource?**  

It’s the **backbone of GCP security**, ensuring that the **right people/apps** have the **right access** at the **right level**.  

---

## 🧱 Core Concepts  

| Component      | Description | Example |
|----------------|-------------|---------|
| **Identity**   | An entity that requests access. Can be: <br> - User (Google account) <br> - Group <br> - Domain <br> - Service Account (non-human) | `alice@gmail.com`, `devops-team@example.com`, `my-service-account@project.iam.gserviceaccount.com` |
| **Role**       | A set of permissions bundled together. Determines **what actions** an identity can perform. | `roles/storage.admin` |
| **Policy**     | JSON/YAML document binding **identities → roles → resources** | Example: "Give `alice@gmail.com` the `roles/viewer` role on project X" |
| **Resource**   | Any GCP object where IAM applies. | Project, VM, Bucket, Pub/Sub Topic |

⚡ **Note**: Permissions cannot be assigned directly → must always be **through a role**.  

---

## 🎭 Types of Roles  

| Role Type        | Description | Example |
|------------------|-------------|---------|
| **Basic Roles**  | Broad, legacy roles. Apply across **entire project**. Not recommended for security. | `Owner`, `Editor`, `Viewer` |
| **Predefined**   | Fine-grained roles curated by Google. Service-specific. | `roles/compute.instanceAdmin`, `roles/storage.objectViewer` |
| **Custom**       | You define exact permissions. Useful for **principle of least privilege**. | Only `compute.instances.start` and `compute.instances.stop` |

👉 Best Practice: Use **Predefined** for most cases, **Custom** for critical use cases. Avoid **Basic roles**.  

---

## 🧬 IAM Policy Hierarchy  

IAM policies can be applied at **different levels of the GCP Resource Hierarchy**:  

1. **Organization** → Root level (company-wide policies).  
2. **Folder** → Department or environment (e.g., `Finance`, `DevOps`, `Prod`).  
3. **Project** → Default level for IAM assignments.  
4. **Resource** → Specific services (VM, bucket, DB, API).  

⚡ **Inheritance**: Policies assigned at a higher level **propagate down** unless overridden.  

Example:  
- If `Viewer` is assigned at Project level → All resources (VMs, buckets) inside inherit it.  

---

## 🤖 Service Accounts  

- Special type of identity used by **apps, VMs, pipelines, automation tools**.  
- Acts like a **robot account** with roles/permissions.  
- Each service account has:  
  - **Email ID** (`name@project.iam.gserviceaccount.com`)  
  - **Private Key (optional)** for external auth  
  - IAM roles assigned to it  

### Example  
- Cloud Build needs permission to deploy an app → uses a **service account** with `roles/cloudbuild.builds.editor`.  

⚠️ **Best Practice**: Avoid embedding long-lived keys. Prefer **Workload Identity Federation**.  

---

## 🛡️ IAM Best Practices for DevOps  

- ✅ Enforce **Principle of Least Privilege** (only minimum permissions).  
- 🚫 Avoid using **Basic Roles** (`Owner`, `Editor`, `Viewer`).  
- 🔐 Use **Service Accounts** for automation (pipelines, scripts).  
- 🔁 Rotate keys/secrets regularly (or avoid keys altogether).  
- 📊 Enable **Cloud Audit Logs** and review IAM changes frequently.  
- 🗂️ Use **Groups** for team management instead of assigning roles to individual users.  
- 🔒 Separate **Production vs Development** access via different projects/service accounts.  

---


## 🧪 Hands-On Lab

### 🔧 Lab 1: Create Project & Assign Viewer Role

    export PROJECT_ID="devops-iam-demo-$(date +%s)"
    gcloud projects create $PROJECT_ID
    gcloud config set project $PROJECT_ID

    gcloud projects add-iam-policy-binding $PROJECT_ID \
      --member="user:devops.engineer@example.com" \
      --role="roles/viewer"

---

### 🔧 Lab 2: Create a Service Account & Grant Role

    gcloud iam service-accounts create devops-bot \
      --display-name="DevOps Pipeline Bot"

    gcloud projects add-iam-policy-binding $PROJECT_ID \
      --member="serviceAccount:devops-bot@$PROJECT_ID.iam.gserviceaccount.com" \
      --role="roles/cloudbuild.builds.editor"

---

## 📦 Real-World DevOps Scenario

**Use Case**:  
Your Cloud Build pipeline should deploy to GKE but NOT delete clusters.

**Solution**:
- Create a **Custom Role** with only `container.deployments.create`
- Assign that role to a **Service Account**
- Use that Service Account in your CI/CD pipeline

---

## ✅ Key Takeaways

- IAM is foundational for secure access in GCP.
- Use **Predefined or Custom Roles** — avoid basic roles.
- Service accounts are key to **DevOps automation**.
- **Always enforce least privilege and audit regularly.**

---
