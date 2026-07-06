# 01: IAM and Permission Escalation 🛡️

In the cloud, "Identity" is the new perimeter. There is no physical firewall or network cable to hack. Instead, you hack the **Identity and Access Management (IAM)** service. If you compromise an IAM user or role with excessive permissions, you don't need a zero-day exploit—you have "legitimate" access to the entire cloud infrastructure.



---

## 📌 Table of Contents
1. [The IAM Core Concepts](#1-the-iam-core-concepts)
2. [Understanding Privilege Escalation](#2-understanding-privilege-escalation)
3. [Common Escalation Vectors](#3-common-escalation-vectors)
4. [The Principle of Least Privilege](#4-the-principle-of-least-privilege)

---

## 1. The IAM Core Concepts

To hack the cloud, you must understand the "Language of Permissions." Every cloud provider uses a similar model:

* **Principal:** Who is making the request? (A human user, an application, or a service).
* **Action:** What are they trying to do? (e.g., `s3:ListBucket`, `ec2:RunInstances`).
* **Resource:** What are they doing it to? (e.g., a specific S3 bucket, an EC2 instance).
* **Effect:** Allow or Deny.



---

## 2. Understanding Privilege Escalation

Privilege escalation in the cloud happens when you have limited permissions, but you find a way to manipulate the environment to gain **Administrative (Root/Global)** access.

Unlike traditional OS escalation (where you look for kernel exploits), cloud escalation is about **policy logic**. You are essentially looking for a "loophole" in the JSON/YAML policy document that lets you grant yourself more power.

---

## 3. Common Escalation Vectors

If you land on a cloud environment with limited access, look for these specific misconfigurations:

| Vector | How it works |
| :--- | :--- |
| **`iam:PassRole`** | You have permission to create an EC2 instance and attach a role to it. You attach a highly privileged "Admin" role to that instance, SSH into it, and steal the Admin credentials. |
| **`iam:CreatePolicyVersion`** | You have permission to edit policy versions. You overwrite an existing policy to grant yourself `AdministratorAccess`, apply it, and then revert the change after you finish your task. |
| **`iam:CreateAccessKey`** | You can create new access keys for an existing Admin user, allowing you to bypass the need to crack their password. |
| **`lambda:UpdateFunctionCode`** | You can modify the code of a Lambda function. If that function has a high-privilege execution role, you can inject malicious code to exfiltrate credentials. |

---

## 4. The Principle of Least Privilege

The goal of Cloud Security is to ensure that every entity has exactly the permissions it needs, and **nothing more**.

### Prevention Checklist:
* [ ] **Avoid Admin Access:** Never use `AdministratorAccess` for daily tasks or applications.
* [ ] **Use Permission Boundaries:** Set a maximum limit on what a user or role can ever do, even if they are granted broader permissions later.
* [ ] **Audit Over-privileged Roles:** Use tools like *AWS IAM Access Analyzer* or *Azure AD Privileged Identity Management* to identify roles that haven't been used in 90+ days.
* [ ] **Infrastructure as Code (IaC) Scanning:** Scan your Terraform or CloudFormation templates with `tfsec` or `Checkov` before deployment to catch bad policies automatically.

---

> **💡 Professional Analysis:**
> Privilege Escalation in the cloud is often a **"Chain of Hooks."** For example, you might not have permission to delete a database, but you have permission to update a Lambda function (`lambda:UpdateFunctionCode`). If that Lambda function is triggered by a database event, you can hijack the Lambda to execute the delete command on your behalf. Always map out the relationships between different cloud services—the "path" to root is rarely a straight line.


---
