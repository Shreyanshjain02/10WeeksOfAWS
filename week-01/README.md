# Week 1 — Cloud Foundations + IAM Challenge


> **AWS Zero To Hero · CloudAdhar × TrainWithShubham**  
> Sessions S1–S2 · Jul 4–5, 2026  
> **SAA-C03 Domain 1 — Design Secure Architectures (30%)**  
> Primary Pillar: **Security** · Secondary Pillars: **Operational Excellence**, **Cost Optimization**

Welcome to Week 1 of the AWS Zero To Hero challenge. This week is your foundation. Before we build EC2, S3, VPC, databases, or real architectures, we must first understand how AWS is structured, how responsibility is shared, how to secure the account, and how IAM controls access.

---

## What You Will Learn This Week

By the end of Week 1, you should be able to:

- Explain AWS Global Infrastructure: Regions, Availability Zones, and Edge Locations.
- Explain the AWS Shared Responsibility Model.
- Secure your AWS account using root MFA and billing alerts.
- Understand the difference between root user, IAM user, IAM group, IAM role, and IAM policy.
- Create IAM users and groups for S3, EC2, and Billing access.
- Understand managed policies, customer managed policies, and inline policies.
- Read a basic JSON IAM policy.
- Apply the principle of least privilege.
- Understand permission boundaries at a high level.
- Configure GitHub Actions OIDC with AWS using IAM, STS, and temporary credentials.

---

## Exam + Pillar Mapping

| Topic | Exam Mapping | Pillar Mapping | Best Practice |
|---|---|---|---|
| AWS account security | D1-30% | Security, Operational Excellence | Protect root user with MFA |
| Billing alerts | D4-20% | Cost Optimization | Monitor cost early |
| Shared Responsibility Model | D1-30% | Security | Know what AWS secures vs what you secure |
| IAM users and groups | D1-30% | Security | Manage permissions using groups |
| IAM roles | D1-30% | Security, Operational Excellence | Use roles for temporary access and federation |
| GitHub OIDC with AWS | D1-30% | Security, Operational Excellence | Use temporary credentials instead of long-lived access keys |
| IAM policies | D1-30% | Security | Use least privilege |
| Permission boundaries | D1-30% | Security | Limit maximum permissions |

---

# Session 1 — Cloud Foundations

## Key Concepts

### 1. AWS Global Infrastructure

AWS infrastructure is built using:

- **Regions** — geographic locations where AWS has multiple data centers.
- **Availability Zones** — isolated data center locations inside a Region.
- **Edge Locations** — locations used by services like CloudFront to deliver content closer to users.

**Exam pointer:**  
For high availability, design across multiple Availability Zones. For low latency content delivery, use CloudFront and Edge Locations.

---

### 2. Shared Responsibility Model

AWS security is shared between AWS and the customer.

- AWS is responsible for **security of the cloud**.
- Customer is responsible for **security in the cloud**.

Example:

- AWS manages global infrastructure, data centers, hardware, and managed service infrastructure.
- You manage IAM users, permissions, data, application security, network configuration, and guest OS patching for EC2.

**Simple line:**  
AWS secures the cloud. You secure what you build in the cloud.

---

### 3. Account and Root User

The root user is the account owner identity and has full access to everything in the AWS account.

Best practices:

- Enable MFA on root user.
- Do not use root user for daily work.
- Create IAM users or roles for regular tasks.
- Monitor billing from the beginning.

---

# Session 2 — IAM Fundamentals

## Key Concepts

### 1. IAM Users, Groups, Roles, and Policies

IAM controls who can sign in and what actions they can perform.

**Identity + Permissions = Access**

- **IAM User** — named identity for a person or workload that needs AWS access.
- **IAM Group** — collection of users with common permissions.
- **IAM Role** — temporary permissions for AWS services or trusted identities.
- **IAM Policy** — JSON document that defines allowed or denied actions.

---

### 2. IAM Users

An IAM user is used when a person or workload needs AWS access.

Example:

- `learner-s3` user can be given S3 read-only access.
- `learner-ec2` user can be given EC2 read-only access.
- `learner-billing` user can be given billing view access.

Important point:

Having login access does not mean having full AWS access. Permissions decide what the user can do.

---

### 3. IAM Groups

IAM groups help manage permissions for multiple users together.

Examples:

- `S3ReadOnlyGroup` → attach `AmazonS3ReadOnlyAccess`
- `EC2ReadOnlyGroup` → attach `AmazonEC2ReadOnlyAccess`
- `BillingViewGroup` → attach `AWSBillingReadOnlyAccess`

Best practice:

Attach policies to groups and add users to groups. This is easier to manage than attaching policies to each user individually.

---

### 4. IAM Roles

IAM roles provide temporary credentials. They are commonly used when AWS services need to access other AWS services.

Example:

A trusted identity needs temporary access to AWS without using long-lived access keys.

Key point:

IAM roles are used for temporary access. In this Week 1 challenge, focus on understanding role assumption and OIDC-based access.

---

### 5. IAM Policies

IAM policies define permissions using JSON.

Common policy types:

- **AWS Managed Policy** — created and maintained by AWS. Example: `AmazonS3ReadOnlyAccess`.
- **Customer Managed Policy** — created by you and reusable across users, groups, and roles.
- **Inline Policy** — directly embedded into one user, group, or role.

Best practice:

Use customer managed policies when you need reusable custom permissions. Use inline policies only for very specific one-to-one use cases.

---

### 6. JSON Policy Structure

A basic IAM policy includes:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

Meaning:

- `Version` — policy language version.
- `Statement` — permission block.
- `Effect` — Allow or Deny.
- `Action` — AWS API action.
- `Resource` — AWS resource where action is allowed or denied.
- `Condition` — optional rules for when policy applies.

---

### 7. Least Privilege

Least privilege means giving only the permissions required to complete the task, nothing extra.

Example:

If a user only needs to view EC2 instances, do not give `AmazonEC2FullAccess`. Give read-only access.

Simple line:

Give enough access to do the work, but not enough access to create damage.

---

### 8. Permission Boundaries

A permission boundary defines the maximum permissions an IAM user or role can have.

Important:

- Permission boundary does not grant permissions by itself.
- It only limits the maximum possible permissions.
- Effective permission is the intersection of identity policy and permission boundary.

Example:

If the identity policy allows only `s3:GetObject`, and the permission boundary allows `s3:*`, the user can still only do `s3:GetObject`.

---

# Hands-on Labs

Use your own AWS account. Use the Free Tier carefully. Do not share sensitive account details in screenshots.

---

## Lab 1 — Secure Your AWS Account

Goal: Create a safe AWS account foundation for the next 10 weeks.

Steps:

1. Create or log in to your AWS account.
2. Enable MFA on the root user.
3. Open the Billing Dashboard.
4. Turn on billing alerts / budget alerts.
5. Create an alert for estimated charges, for example `$5`.
6. Stop using root user for daily activities.

Deliverables:

- Screenshot of root MFA enabled.
- Screenshot of billing alert or budget alert.
- Short note explaining why root user should not be used daily.

Security note:

Do not share root email, account ID, access keys, secret keys, MFA QR code, payment details, or detailed billing information in screenshots.

---

## Lab 2 — S3 Read-Only IAM Group and User

Goal: Understand IAM group-based access.

Create group:

```text
Group name: S3ReadOnlyGroup
Policy: AmazonS3ReadOnlyAccess
```

Create user:

```text
User name: learner-s3
Add user to: S3ReadOnlyGroup
```

Test:

1. Log in as `learner-s3`.
2. Open S3.
3. Confirm you can view/list S3 buckets.
4. Try an action that is not allowed.
5. Observe `Access Denied`.

Deliverables:

- Screenshot of group created.
- Screenshot of user added to group.
- Screenshot of policy attached.
- Screenshot of allowed S3 view action.
- Screenshot of denied action.

---

## Lab 3 — EC2 Read-Only Access

Goal: Apply least privilege to EC2.

Create group:

```text
Group name: EC2ReadOnlyGroup
Policy: AmazonEC2ReadOnlyAccess
```

Create user:

```text
User name: learner-ec2
Add user to: EC2ReadOnlyGroup
```

Test:

1. Log in as `learner-ec2`.
2. Open EC2 dashboard.
3. Confirm EC2 resources are visible.
4. Confirm the user cannot create or terminate instances.

Deliverables:

- Screenshot of EC2ReadOnlyGroup.
- Screenshot of policy attached.
- Screenshot of EC2 dashboard access.
- Screenshot or note for denied create/terminate action.

---

## Lab 4 — Billing View Access

Goal: Understand billing access with limited permissions.

Create group:

```text
Group name: BillingViewGroup
Policy: AWSBillingReadOnlyAccess
```

Create user:

```text
User name: learner-billing
Add user to: BillingViewGroup
```

Test:

1. Log in as `learner-billing`.
2. Open Billing Dashboard.
3. Verify billing visibility.
4. Confirm user cannot manage unrelated AWS services.

Deliverables:

- Screenshot of BillingViewGroup.
- Screenshot of billing policy attached.
- Screenshot of Billing Dashboard access.

---

## Lab 5 — Custom S3 Read-Only JSON Policy

Goal: Read and create a basic JSON policy.

Create a customer managed policy:

```text
Policy name: CustomS3ReadOnlyTrainingPolicy
```

Use this sample policy and replace `YOUR-BUCKET-NAME` with your actual bucket name:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

Deliverables:

- Add policy JSON in your repo.
- Screenshot of custom policy created.
- Screenshot of allowed action.
- Screenshot or note for denied action.

---

## Optional Advanced Lab — Switch Role

Goal: Understand role assumption and temporary access.

Create role:

```text
Role name: TrainingReadOnlyRole
Policy: ReadOnlyAccess
```

Create a policy for an IAM user that allows assuming this role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::ACCOUNT-ID:role/TrainingReadOnlyRole"
    }
  ]
}
```

Replace:

```text
ACCOUNT-ID
```

with your AWS account ID.

Test:

1. Log in as IAM user.
2. Use Switch Role in AWS Console.
3. Switch into `TrainingReadOnlyRole`.
4. Verify role-based access.

Key learning:

- IAM User = login identity.
- IAM Role = temporary access.
- STS = provides temporary credentials.
- Policy = defines allowed or denied actions.

---

---

## Optional Advanced Challenge — GitHub OIDC with AWS

Goal: Configure secure GitHub Actions access to AWS without storing long-lived AWS access keys in GitHub Secrets.

This challenge helps you understand how GitHub Actions can authenticate to AWS using OIDC, IAM, and AWS STS.

### Architecture

```text
GitHub Actions
      │
      │ OIDC Token
      ▼
AWS IAM OIDC Provider
      │
      ▼
AWS STS AssumeRoleWithWebIdentity
      │
Temporary Credentials
      ▼
AWS Resources such as S3 or EC2
```

### Step 1 — Create GitHub OIDC Provider in AWS

Open:

```text
AWS Console → IAM → Identity Providers → Add Provider
```

Provider type:

```text
OpenID Connect
```

Provider URL:

```text
https://token.actions.githubusercontent.com
```

Audience:

```text
sts.amazonaws.com
```

Click **Add Provider**.

### Step 2 — Create IAM Role for GitHub Actions

Go to:

```text
IAM → Roles → Create Role
```

Select:

```text
Trusted entity: Web Identity
Identity Provider: token.actions.githubusercontent.com
Audience: sts.amazonaws.com
```

Use your own GitHub details:

```text
GitHub organization or username: <YOUR_GITHUB_ORG_OR_USERNAME>
GitHub repository: <YOUR_REPOSITORY_NAME>
```

Example:

```text
GitHub organization or username: your-github-username
GitHub repository: aws-week-1-challenge
```

Role name:

```text
<YOUR_OIDC_ROLE_NAME>
```

Example:

```text
github-oidc-challenge-role
```

Attach a safe challenge permission such as:

```text
AmazonS3ReadOnlyAccess
```

Create the role.

### Step 3 — Update the Trust Policy

Replace the role trust relationship with the policy below.

Replace the placeholders with your own values:

```text
<YOUR_AWS_ACCOUNT_ID> = your AWS account ID
<YOUR_GITHUB_ORG_OR_USERNAME> = your GitHub username or organization
<YOUR_REPOSITORY_NAME> = your repository name
```

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        },
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:<YOUR_GITHUB_ORG_OR_USERNAME>/<YOUR_REPOSITORY_NAME>:*"
        }
      }
    }
  ]
}
```

Challenge note:

For stricter security, you can restrict the trust policy to a specific branch later, for example:

```text
repo:<YOUR_GITHUB_ORG_OR_USERNAME>/<YOUR_REPOSITORY_NAME>:ref:refs/heads/main
```

### Step 4 — Add GitHub Actions Workflow

Create this file in your repository:

```text
.github/workflows/aws-oidc-challenge.yml
```

Use this workflow:

```yaml
name: AWS OIDC Challenge

on:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  aws-oidc-challenge:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Configure AWS Credentials using OIDC
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:role/<YOUR_OIDC_ROLE_NAME>
          aws-region: ap-south-1

      - name: Verify AWS Identity
        run: aws sts get-caller-identity

      - name: Validate S3 Read Access
        run: aws s3 ls
```

Replace the placeholders with your own values:

```text
<YOUR_AWS_ACCOUNT_ID> = your AWS account ID
<YOUR_OIDC_ROLE_NAME> = your IAM role name created for this challenge
```

### Step 5 — Run the Challenge

1. Push the workflow to GitHub.
2. Open the repository in GitHub.
3. Go to **Actions**.
4. Select **AWS OIDC Challenge**.
5. Click **Run workflow**.
6. Open the workflow logs.
7. Check the output of `aws sts get-caller-identity`.

Expected output should show an assumed-role ARN similar to:

```text
arn:aws:sts::<YOUR_AWS_ACCOUNT_ID>:assumed-role/<YOUR_OIDC_ROLE_NAME>/...
```

If you see an assumed-role ARN, your GitHub OIDC challenge is completed successfully.

### Challenge Deliverables

Add the following files and screenshots in your Week 1 submission:

```text
week-01/day-02-iam/YOUR-NAME/
├── oidc/
│   ├── trust-policy.json
│   └── aws-oidc-challenge.yml
└── screenshots/
    ├── oidc-provider.png
    ├── github-oidc-role.png
    ├── oidc-trust-policy.png
    ├── github-action-success.png
    └── sts-assumed-role-output.png
```

Also add a short note in your README:

```markdown
## Optional Advanced Challenge — GitHub OIDC with AWS

I configured GitHub Actions to access AWS using OIDC.

Instead of storing AWS access keys in GitHub Secrets, GitHub requested an OIDC token and AWS STS exchanged that token for temporary credentials.

The key learning is that OIDC is more secure because it avoids long-lived access keys and uses temporary role-based access.
```

### Key Learning

Normally, many beginners store AWS access keys in GitHub Secrets. That works, but the keys must be protected and rotated.

With OIDC, GitHub requests a signed identity token. AWS validates the token using the IAM OIDC provider and the role trust policy. If the repository condition matches, AWS STS issues temporary credentials.

This is more secure because no long-lived AWS access keys are stored in GitHub.

Security note:

Do not share AWS account ID publicly if you are not comfortable. Do not share access keys, secret keys, GitHub secrets, or sensitive workflow logs.

# Repo Submission Format

Create a folder:

```text
week-01/day-02-iam/YOUR-NAME/
```

Suggested structure:

```text
week-01/
└── day-02-iam/
    └── your-name/
        ├── README.md
        ├── notes.md
        ├── policies/
        │   └── custom-s3-readonly-policy.json
        └── screenshots/
            ├── root-mfa.png
            ├── billing-alert.png
            ├── iam-group.png
            ├── iam-user.png
            ├── s3-access.png
            ├── ec2-readonly.png
            ├── iam-role.png
            ├── oidc-provider.png
            └── github-action-success.png
```

---

# README Template for Submission

```markdown
# Week 1 Day 2 — IAM Challenge

## Name
Your Name

## Topics Practiced
- AWS account security
- Root MFA
- Billing alert
- IAM users
- IAM groups
- IAM roles and OIDC-based temporary access
- IAM policies
- JSON policy
- Least privilege
- Permission boundaries
- GitHub OIDC role for AWS access
- GitHub OIDC with AWS

## What I Learned
Today I learned how IAM controls access in AWS.

The most important concept I understood is:

> Identity + Permissions = Access

I also learned that root user should not be used for daily work and IAM roles can provide temporary access without long-lived access keys.

## Screenshots Added
- Root MFA
- Billing alert
- IAM user
- IAM group
- Policy attached
- S3 access test
- IAM role
- GitHub OIDC role
- GitHub Actions OIDC workflow success

## Custom Policy
Policy file added:

policies/custom-s3-readonly-policy.json

## Optional Advanced Challenge
I also completed GitHub OIDC with AWS and verified access using AWS STS temporary credentials.

Workflow file:

.github/workflows/aws-oidc-challenge.yml

Trust policy file:

oidc/trust-policy.json

## Key Takeaway
Least privilege means giving only the required permissions, nothing extra.
```

# Cleanup

After completing the lab:

- Delete test IAM users.
- Delete test IAM groups.
- Delete custom test policies if not needed.
- Keep billing alerts enabled.
- Keep MFA enabled.
- Keep your main admin IAM user.

---

# Learn in Public

Share a short post this week. Adapt this for LinkedIn, X, or GitHub:

```text
Week 1 of #10WeeksOfAWS done.

This week I set up my AWS foundation:
- Secured the account with root MFA and billing alert
- Created IAM users, groups, and least-privilege policies
- Understood IAM roles and why roles are better than hardcoded access keys
- Learned the Shared Responsibility Model and AWS Global Infrastructure

The thing that finally clicked: [write one thing you actually understood this week]

#10WeeksOfAWS #AWS10WeekChallenge #CloudAdhar #TrainWithShubham
@TrainWithShubham @Gangadhar Ure
```

Attach a screenshot of your billing alarm or IAM setup. Do not include sensitive information.

---

<div align="center">

[Home](../README.md) · [Week 2 →](../week-02/)

</div>
