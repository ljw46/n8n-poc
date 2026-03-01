# n8n Serverless on Google Cloud (Terraform)

This repository contains the Terraform configuration to deploy a cost-optimized n8n instance using Google Cloud Run and Cloud SQL (PostgreSQL).

## 🚀 Key Features
* Cost-Efficient: Scales to zero when not in use. (better to leave min as 1 if using schedule features though). 
* Startup CPU Boost: Prevents "Cold Start" timeouts during initialization.
* Secure: Secrets stored in Google Secret Manager.
* Automated: Handles IAM permissions and API enablement out of the box.

---

## 🛠 Prerequisites

1. Google Cloud Project with billing enabled.
2. Terraform installed locally.
3. GCloud CLI authenticated: gcloud auth application-default login

---

## 🔑 Setup Environment Variables

Run these commands in your terminal before deploying to configure the project securely.

### 1. Generate an Encryption Key
export TF_VAR_n8n_encryption_key=$(openssl rand -hex 16)
echo "Your encryption key is: $TF_VAR_n8n_encryption_key"

### 2. Set Remaining Variables
export TF_VAR_project_id="your-project-id"
export TF_VAR_region="us-central1"
export TF_VAR_db_password="your-complex-password"

# IMPORTANT: Leave this as placeholder for the first run
export TF_VAR_n8n_URL="https://placeholder-url.com"

---

## ⚡ Deployment Steps

### Step 1: Initialize Terraform
terraform init

### Step 2: First Pass (Generate Service)
terraform apply
# This creates the service and generates your unique Cloud Run URL.

### Step 3: Update the Webhook URL
Check the Terraform outputs for 'n8n_url'. Update your environment variable to match it exactly (no trailing slash):
export TF_VAR_n8n_URL="https://your-generated-url-here.a.run.app"

### Step 4: Final Pass
terraform apply
# This syncs n8n with its correct internal routing.

---

## 💰 Cost Management (Dev Mode)

* Cloud Run: Automatically scales to zero.
* Cloud SQL: The primary cost. Manually "Stop" the instance in the GCP Console when not in use to pause CPU/RAM billing.
* Ritual: Start DB -> Wait 2 mins -> Open n8n -> Stop DB when finished.

---

## 📝 Troubleshooting

* 404 Init Error: Ensure TF_VAR_n8n_URL does not end with a slash.
* Revision Conflicts: The template uses a 'deployment-heartbeat' label to ensure every apply creates a unique revision.
