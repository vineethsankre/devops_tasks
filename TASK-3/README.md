# AWS IAM Task – Users, Roles, Policies & MFA

## 🎯 Objective
Configure AWS IAM users, roles, policies, EC2 access to S3, and enable Multi-Factor Authentication (MFA).

---

## 1. Create IAM Users

Go to: **AWS Console → IAM → Users → Create user**

Create the following users:
- `developer-user`
- `tester-user`
- `admin-user`

Enable:
- **AWS Management Console access**
- Set password (auto or custom)

---

## 2. Create IAM Roles

Go to: **IAM → Roles → Create role**

Choose:
- **Trusted entity:** AWS Service  
- **Use case:** EC2  

Create these roles:
- `DeveloperRole`
- `TesterRole`
- `AdminRole`

---

## 3. Attach IAM Policies

### Developer
Attach:
- `AmazonS3ReadOnlyAccess`
- `AmazonEC2ReadOnlyAccess`

### Tester
Attach:
- `AmazonS3FullAccess`
- `AmazonEC2ReadOnlyAccess`

### Admin
Attach:
- `AdministratorAccess`

---

## 4. Create EC2 IAM Role for S3 Access

Go to: **IAM → Roles → Create role**

Choose:
- **Trusted entity:** AWS Service  
- **Use case:** EC2  

Attach policy:
- `AmazonS3FullAccess`

Role name:
- `EC2-S3-Access-Role`


---

## 5. Attach IAM Role to EC2 Instance

Go to: **EC2 → Instances**

1. Select your instance
2. Click **Actions → Security → Modify IAM role**
3. Choose `EC2-S3-Access-Role`
4. Save

This allows EC2 to access S3 without access keys.

---

## 6. Enable MFA for IAM Users

For each user (Developer, Tester, Admin):

Go to:
**IAM → Users → Select User → Security Credentials**

1. Click **Assign MFA device**
2. Choose **Virtual MFA device**
3. Scan QR code using Google Authenticator or Authy
4. Enter two MFA codes
5. Activate

---

## ✅ Final Outcome

- IAM users created
- Roles and permissions applied
- EC2 securely accesses S3 using IAM role
- MFA enabled for user security

---

## 🔐 Security Best Practice
- Do not use access keys on EC2
- Always use IAM roles
- Enable MFA for privileged users

