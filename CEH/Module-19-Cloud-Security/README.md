# Module 19: Cloud Security ☁️

Cloud security represents a paradigm shift. Traditional security relied on "castle-and-moat" defenses (firewalls, physical access). Cloud security relies on **Identity** and **Configuration**. In the cloud, "Infrastructure as Code" (IaC) means one misconfigured JSON file can expose your entire production environment to the internet.

---

## 📌 Module Overview
1. **Shared Responsibility Model:** Understanding what the Cloud Provider secures vs. what YOU secure.
2. **Identity & Access Management (IAM):** Mastering the "new perimeter."
3. **Storage & Infrastructure:** Identifying common misconfigurations (Public S3 buckets, exposed metadata).
4. **Cloud Exploitation:** Pivoting, persistence, and container breakout.

---

## 🏗️ The Shared Responsibility Model
You cannot secure the cloud if you don't know where your responsibility ends and the provider's begins.



* **Provider (AWS/Azure/GCP):** Secures the *Cloud* (Physical datacenters, network hardware, hypervisors).
* **Customer (You):** Secures the *Things IN the Cloud* (Data, Identity, Permissions, OS patching, Network config).

**The Trap:** Many companies think that by moving to the cloud, they don't need to worry about security. This is how data breaches happen—they leave the "doors" (permissions) unlocked.

---

## 🛠️ The Cloud Toolkit
* **CLI Tools:** `aws-cli`, `az-cli`, `gcloud-sdk`. These are your primary interaction points.
* **Enumeration Tools:** `Pacu` (the Swiss Army Knife for AWS exploitation), `CloudEnum` (for finding public assets).
* **IaC Scanning:** `Checkov` or `tfsec` (to audit Terraform/CloudFormation code for security flaws before deployment).
* **Monitoring:** `CloudTrail` (AWS) / `Activity Logs` (Azure). Essential for detecting if your account has been breached.

---

## ⚠️ The Golden Rule of Cloud
In Cloud, **everything is an API call.** If you can make the API call, you can manipulate the environment. Your goal as a pentester is to find identities (access keys, roles) that have higher permissions than they actually need and leverage them to escalate privileges.