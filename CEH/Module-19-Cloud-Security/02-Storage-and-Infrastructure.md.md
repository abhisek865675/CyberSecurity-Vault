# 02: Storage and Infrastructure Security 🗄️

Cloud infrastructure security is about visibility and configuration. Because cloud assets (like storage buckets or virtual machines) are often accessible via the public internet, a single "public" checkbox or a misconfigured rule can expose petabytes of data instantly.

---

## 📌 Table of Contents
1. [Cloud Storage Misconfigurations](#1-cloud-storage-misconfigurations)
2. [Metadata Service Abuse (SSRF)](#2-metadata-service-abuse-ssrf)
3. [Infrastructure as Code (IaC) Vulnerabilities](#3-infrastructure-as-code-iac-vulnerabilities)
4. [Defensive Best Practices](#4-defensive-best-practices)

---

## 1. Cloud Storage Misconfigurations
Storage buckets (AWS S3, Azure Blob, Google Cloud Storage) are the primary targets for attackers. The most common vulnerability is **Public Access**.



* **The Risk:** Developers often enable "Public Read" to debug an application or share assets, then forget to disable it. 
* **The Attack:** Attackers use automated tools (like `CloudEnum` or `BucketStream`) to scan the internet for open buckets. Once found, they list the contents, steal database backups, customer PII, or API keys stored in configuration files.
* **The "Wait, I didn't make it public" trap:** Sometimes buckets aren't "Public," but they are "Authenticated" (accessible by *anyone* with an AWS account). This means an attacker can use a free, throwaway AWS account to access your private data.

---

## 2. Metadata Service Abuse (SSRF)
Every cloud instance has a special "Metadata Service" (e.g., `169.254.169.254`). It provides configuration data, instance tags, and—most importantly—**temporary security credentials** for the IAM role assigned to that instance.



* **The Attack (SSRF):** If you find a web application on a cloud instance that is vulnerable to Server-Side Request Forgery (SSRF), you can trick the server into fetching data from the Metadata Service.
* **The Payload:**
  ```bash
  # Inside a vulnerable app, trigger this request:
  curl [http://169.254.169.254/latest/meta-data/iam/security-credentials/role-name](http://169.254.169.254/latest/meta-data/iam/security-credentials/role-name)
  ```
  
- **The Result:** The metadata service returns the `AccessKeyId`, `SecretAccessKey`, and `Token`. You can now use these credentials on your own machine to impersonate the server's role.
    

## 3. Infrastructure as Code (IaC) Vulnerabilities

In the modern cloud, nobody clicks "Create" in the web console. They use **Terraform, CloudFormation, or ARM templates**.

- **The Issue:** If the code that defines your infrastructure is insecure, your _entire infrastructure_ is insecure.
    
- **Common Mistakes:**
    
    - Hardcoding secrets (passwords/keys) directly into the Terraform files.
        
    - Overly permissive Security Groups (e.g., `0.0.0.0/0` allowed on SSH port 22).
        
    - Missing logging configurations (making it impossible to detect an attack).
        

## 4. Defensive Best Practices

|**Control**|**Implementation**|
|---|---|
|**Block Public Access**|Enable "S3 Block Public Access" at the account level.|
|**Enforce IMDSv2**|Use Instance Metadata Service Version 2 (IMDSv2), which requires a session token, mitigating SSRF-based credential theft.|
|**Encryption**|Always use Server-Side Encryption (SSE) for all storage buckets.|
|**IaC Scanning**|Integrate tools like `tfsec` or `checkov` into your CI/CD pipeline. If a template has a security flaw, the deployment should automatically fail.|

> **💡 Professional Analysis:**
> 
> The most dangerous cloud vulnerability is **credential sprawl**. Credentials often end up in places they shouldn't be: inside Docker containers, embedded in git commits, stored in plain text files in S3 buckets, or printed in application logs. When assessing cloud infrastructure, always prioritize finding **where credentials live**. If you find an access key in a public-facing asset, you have effectively bypassed the entire cloud security posture.


---
