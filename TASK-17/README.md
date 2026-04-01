# 🚀 Terraform Remote Backend with AWS KMS (Production-Ready Guide)

## 📌 Overview
This guide walks you through setting up a secure Terraform backend using:

- S3 → Remote state storage
- DynamoDB → State locking
- KMS → Encryption

All resources are created in **ap-south-1** region.

---

## 🧱 Architecture

Terraform → S3 (state storage) → KMS (encryption)  
           ↘ DynamoDB (locking)

---

## ⚙️ Prerequisites

- AWS CLI configured
- Terraform installed
- IAM permissions for S3, KMS, DynamoDB

```bash
aws configure
terraform -v
```

---

## 🌍 Step 1: Set Region

```bash
aws configure set region ap-south-1
```

---

## 🔐 Step 2: Create KMS Key

```bash
aws kms create-key --description "Terraform KMS Key"
```

Optional alias:

```bash
aws kms create-alias   --alias-name alias/terraform-key   --target-key-id <KEY_ID>
```

---

## 🪣 Step 3: Create S3 Backend Bucket

```bash
aws s3api create-bucket   --bucket vineeth-tf-state-bucket-001   --region ap-south-1   --create-bucket-configuration LocationConstraint=ap-south-1
```

### Enable Versioning

```bash
aws s3api put-bucket-versioning   --bucket vineeth-tf-state-bucket-001   --versioning-configuration Status=Enabled
```

### Enable KMS Encryption

```bash
aws s3api put-bucket-encryption   --bucket vineeth-tf-state-bucket-001   --server-side-encryption-configuration '{
    "Rules": [{
      "ApplyServerSideEncryptionByDefault": {
        "SSEAlgorithm": "aws:kms",
        "KMSMasterKeyID": "<KEY_ID>"
      }
    }]
  }'
```

---

## 🧱 Step 4: Create DynamoDB Table

```bash
aws dynamodb create-table   --table-name terraform-locks   --attribute-definitions AttributeName=LockID,AttributeType=S   --key-schema AttributeName=LockID,KeyType=HASH   --billing-mode PAY_PER_REQUEST   --region ap-south-1
```

Verify:

```bash
aws dynamodb describe-table   --table-name terraform-locks   --query "Table.TableStatus"
```

---

## 📁 Step 5: Terraform Project Setup

```bash
mkdir terraform-kms-demo
cd terraform-kms-demo
```

### main.tf

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "example" {
  bucket = "vineeth-demo-bucket-unique-001"
}
```

---

### backend.tf

```hcl
terraform {
  backend "s3" {
    bucket         = "vineeth-tf-state-bucket-001"
    key            = "dev/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
    kms_key_id     = "<KEY_ID>"
  }
}
```

---

## ⚙️ Step 6: Initialize

```bash
terraform init -reconfigure
```

---

## 🚀 Step 7: Apply

```bash
terraform apply
```

---

## ✅ Verification

- S3 → state file exists
- Encryption → KMS enabled
- DynamoDB → lock entries during apply

---

## ⚠️ Common Issues

### ❌ DynamoDB not found
→ Region mismatch

### ❌ KMS key not found
→ Key created in different region

### ❌ Bucket already exists
→ Use unique name or import

---

## 🧠 Key Concepts

- Backend bucket = Terraform state storage
- Resource bucket = actual infrastructure
- All backend components must be in same region

---

## 🔥 Best Practices

- Enable versioning on S3
- Use KMS for encryption
- Use DynamoDB for locking
- Restrict access with IAM
- Separate environments (dev/prod)

---


