# AWS IAM Governance Report 🔐

A Python-based tool that collects and analyzes **AWS IAM user security information** and generates an Excel governance report.

The project is designed to help identify common IAM security risks such as excessive permissions, missing MFA, inactive users, wildcard permissions, and old access keys.

---

## 📌 Overview

Managing IAM users manually can become difficult as the number of users, roles, policies, and access keys increases.

This project automates the initial IAM governance review by collecting information from **AWS IAM** and assigning a basic risk score to each IAM user.

The generated Excel report provides a centralized view of:

* IAM users
* User creation dates
* Last password usage
* MFA status
* Attached policies
* Inline policies
* Access keys
* Access key age
* Access key last-used information
* Administrative access
* Wildcard permissions
* Risk score
* Risk level
* Security recommendations

---

## 🏗️ Architecture

```text
                    AWS Account
                         │
                         ▼
                   AWS IAM API
                         │
              ┌──────────┴──────────┐
              │                     │
         IAM Users             IAM Policies
              │                     │
              ├── MFA               ├── Actions
              ├── Password          └── Resources
              ├── Access Keys
              └── Last Login
                         │
                         ▼
                  Python / Boto3
                         │
                         ▼
                  Risk Assessment
                         │
                         ▼
                  Pandas DataFrame
                         │
                         ▼
              IAM_Governance_Report.xlsx
```

---

## ⚙️ Features

### 1. IAM User Inventory

Collects all IAM users in the AWS account.

For each user, the script collects:

* Username
* Creation date
* Last password usage

---

### 2. MFA Assessment

Checks whether each IAM user has an MFA device configured.

Users without MFA receive additional risk points.

---

### 3. Policy Analysis

The script analyzes attached IAM policies and checks for potentially excessive permissions.

It currently checks for:

* `AdministratorAccess`
* `Action: "*"`
* `Resource: "*"`

These permissions can indicate a broader-than-necessary access scope and should be reviewed.

---

### 4. Access Key Analysis

For each IAM user, the script checks:

* Number of access keys
* Access key age
* Last-used date

This can help identify old or potentially unused credentials.

---

### 5. Risk Scoring

Each IAM user receives a risk score based on several security indicators.

| Finding                        | Score |
| ------------------------------ | ----: |
| AdministratorAccess            |  +100 |
| Wildcard Action                |   +50 |
| Wildcard Resource              |   +50 |
| MFA Disabled                   |   +25 |
| No login for more than 90 days |   +20 |

### Risk Levels

| Score | Risk Level |
| ----: | ---------- |
|  100+ | Critical   |
| 70–99 | High       |
| 30–69 | Medium     |
|  0–29 | Low        |

> The scoring model is a starting point for governance analysis and should be customized according to the organization's security policies and risk framework.

---

## 📊 Generated Report

The script generates:

```text
IAM_Governance_Report.xlsx
```

The report contains columns such as:

```text
User Name
Creation Date
Last Login
Days Since Login
MFA Enabled
Attached Policies
Inline Policies
Access Key Count
Access Key Age (Days)
Access Key Last Used
Admin Access
Wildcard Action
Wildcard Resource
Risk Score
Risk Level
Recommendation
```

---

## 🚀 Getting Started

### Prerequisites

Make sure you have:

* Python 3.x
* An AWS account
* AWS credentials configured
* IAM permissions required to read the IAM information

---

### 1. Clone the repository

```bash
git clone https://github.com/YOUR-USERNAME/aws-iam-governance-report.git

cd aws-iam-governance-report
```

---

### 2. Create a virtual environment

#### Windows

```powershell
python -m venv venv
```

Activate it:

```powershell
.\venv\Scripts\activate
```

#### Linux / macOS

```bash
python3 -m venv venv
source venv/bin/activate
```

---

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

### 4. Configure AWS credentials

Configure your AWS credentials using the AWS CLI:

```bash
aws configure
```

The script uses **Boto3**, which retrieves the configured AWS credentials automatically.

You can verify your AWS identity with:

```bash
aws sts get-caller-identity
```

---

### 5. Run the script

```bash
python IAM_Governance.py
```

You should see:

```text
Collecting IAM data...

Report saved: IAM_Governance_Report.xlsx
```

The Excel report will then be generated in the project directory.

---

## 🔐 Required IAM Permissions

The AWS identity running the script needs read-only permissions for the IAM APIs used by the project.

Examples include:

```text
iam:ListUsers
iam:ListMFADevices
iam:ListAttachedUserPolicies
iam:ListUserPolicies
iam:GetPolicy
iam:GetPolicyVersion
iam:ListAccessKeys
iam:GetAccessKeyLastUsed
```

A production implementation should follow the **principle of least privilege** and grant only the permissions required by the tool.

---

## ⚠️ Security Considerations

This project is designed for **read-only IAM governance analysis**.

It does not automatically:

* Delete users
* Delete access keys
* Modify IAM policies
* Enable MFA
* Remove permissions

This is intentional.

The tool identifies potential issues and provides recommendations so that changes can be reviewed before being applied.

---

## 🔎 Example Findings

A user could receive a report such as:

```text
User: example-user
MFA: Disabled
Admin Access: True
Wildcard Action: True
Wildcard Resource: True
Days Since Login: 125

Risk Score: 195
Risk Level: Critical

Recommendation:
Review AdministratorAccess;
Remove Action:*;
Limit Resource Scope;
Enable MFA;
Review Inactive User
```

---

## 🎯 Purpose

The main goal of this project is to demonstrate how **Python, AWS APIs, and automation** can be used to improve IAM governance and security visibility.

Instead of manually reviewing IAM users and permissions through the AWS Console, the process can be automated and converted into a repeatable governance workflow.

---

## 📚 Technologies

* **Python**
* **Boto3**
* **Pandas**
* **OpenPyXL**
* **AWS IAM**
* **AWS CLI**
* **Excel**

---

## 👩‍💻 Author

**Alanoud Alfawzan**

Cloud Engineering | AWS | Automation

---
