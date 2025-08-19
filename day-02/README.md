# 🚀 Day-02: GCP Project Setup & Billing

## GCP Hierarchy

<img width="1024" height="1024" alt="GCP Resource Hierarchy Flowchart" src="https://github.com/user-attachments/assets/c1d9b947-bf12-4b30-9df6-df3491dc01a8" />

### Structure
**Organization → Folders → Projects → Resources**

- **Organization**: 
  - Represents your company or enterprise. 
  - Root node for managing billing, IAM, and policies across the entire org.  
- **Folders**: 
  - Optional grouping mechanism.  
  - Used for teams, departments, or environments (e.g., `Finance`, `IT`, `Production`).  
  - Policies/permissions applied here flow down to projects inside.  
- **Projects**: 
  - The **core container** where all resources (VMs, buckets, DBs) live.  
  - Has its own **Project ID, IAM roles, APIs, and Billing**.  
- **Resources**: 
  - Actual GCP services like **VMs, Storage Buckets, Pub/Sub, BigQuery**.  
  - Can only exist inside a **project**.  

⚡ **Policy Inheritance**: IAM roles, billing permissions, and org policies **flow top → down**.  
Ex: IAM role at Organization → available at Folder → Project → Resource.  

---

## Understanding GCP Projects

In GCP, everything you create (VMs, buckets, databases, APIs, IAM) lives inside a **project**.  
Think of it as a **logical folder** that defines **ownership, billing, IAM, and quotas**.

### Key Features of a Project:
- **Unique Project ID** (immutable)  
- **Project Name** (editable)  
- **Billing association**  
- **IAM roles & permissions**  
- **APIs & services enabled per project**  
- **Quotas and limits** applied per project  

### Why Projects?
- Provides **isolation** between workloads (security, IAM, billing).  
- Allows **multi-project strategy**:
  - Separate projects for **teams** (e.g., Payments, Logistics).  
  - Separate projects for **environments** (Dev, Staging, Prod).  
- Easier **cost tracking and auditing**.  

---

## Billing Accounts in GCP

A **Billing Account** defines **who pays for resources** inside projects.  

### Types of Billing Accounts:
1. **Free Trial**:  
   - ₹22,500 (~$300) credits, valid for 90 days.  
   - Auto-created during signup.  
2. **Self-Service**:  
   - Linked to credit/debit card.  
   - Pay-as-you-go.  
3. **Invoiced**:  
   - For enterprises with contracts.  

### Important Points:
- One **Billing Account** → can link to **multiple projects**.  
- One **Project** → linked to **only one billing account at a time**.  
- Projects can be **moved between billing accounts**.  
- Costs are calculated **per project**, then aggregated under billing account.  
- If **free credits expire**, charges apply only if you **manually upgrade**.  

---

## Task 1: Create a New Project

1. Go to [GCP Console](https://console.cloud.google.com)  
2. From the top bar, click on the **Project dropdown**  
3. Click **New Project**  
4. Enter name → Example: `gcp-zero-to-hero`  
5. Select billing account → Click **Create**  

👉 This project will be used for all future labs.  

---

## Task 2: Set Budget Alerts

Budget alerts help you **track usage and avoid unexpected charges**.  

### Steps:
1. Go to **Billing → Budgets & Alerts**  
2. Click **Create Budget**  
3. Enter budget limit (e.g., ₹1000)  
4. Configure email alerts at:  
   - **50% usage** (early warning)  
   - **90% usage** (critical warning)  
   - **100% usage** (full budget exhausted)  

⚠️ **Note**: Budgets are **alerts only**. GCP will **not auto-stop** services.  
👉 To enforce hard limits: Use **Budgets + Pub/Sub + Cloud Functions** to shut down resources automatically.  

---

## Task 3: Enable APIs for Core Services

In GCP, **APIs are disabled by default** for security and cost reasons.  
You must **enable APIs** before using services.  

### Why Enable APIs?
- APIs provide the **backend endpoints** for Console, SDKs, `gcloud`, Terraform.  
- Without enabling → service calls will fail.  

### Recommended Core APIs to Enable:
- **Compute Engine API** → Create/manage Virtual Machines.  
- **Cloud Storage API** → Buckets & object storage.  
- **Cloud Logging API** → Store/view logs from services.  
- **Cloud Monitoring API** → Collect metrics & create alerts.  
- **Cloud Resource Manager API** → Manage org, folders, and projects.  
- **IAM Service Account Credentials API** → Manage service account tokens & keys.  

### Steps:
1. Go to **APIs & Services → Library**  
2. Search each API by name → Click **Enable**  

**Or enable via Cloud Shell**:

```bash
gcloud services enable \
  compute.googleapis.com \
  storage.googleapis.com \
  logging.googleapis.com \
  monitoring.googleapis.com \
  cloudresourcemanager.googleapis.com \
  iamcredentials.googleapis.com
