# 🔐 Terraform API Key Management with GitHub Actions

## 🎯 Objective
Securely store and use API keys in Terraform using GitHub Actions without hardcoding secrets.

---

## 🧰 Tech Stack
- Terraform
- GitHub Actions
- GitHub Secrets

---

## 📁 Project Structure
```
.
├── main.tf
├── variables.tf
├── outputs.tf
└── .github/workflows/terraform.yml
```

---

## 🧩 Step 1: Define Sensitive Variable

Create `variables.tf`

```hcl
variable "api_key" {
  description = "API key for external service"
  type        = string
  sensitive   = true
}
```

---

## 🧩 Step 2: Use API Key in Terraform

Example in `main.tf`

```hcl
provider "example" {
  api_key = var.api_key
}
```

---

## 🧩 Step 3: Add GitHub Secret

1. Go to your GitHub repo
2. Navigate to **Settings → Secrets and variables → Actions**
3. Click **New repository secret**
4. Add:
   - Name: `API_KEY`
   - Value: your actual API key

---

## 🧩 Step 4: Create GitHub Actions Workflow

Create `.github/workflows/terraform.yml`

```yaml
name: Terraform Deploy

on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Plan
        run: terraform plan -var="api_key=${{ secrets.API_KEY }}"

      - name: Terraform Apply
        run: terraform apply -auto-approve -var="api_key=${{ secrets.API_KEY }}"
```

---

## 🔐 Security Best Practices

- Never hardcode API keys in `.tf` files
- Never commit `.tfvars` with secrets
- Always use GitHub Secrets or a secret manager
- Mark variables as `sensitive = true`

---

## ✅ Outcome

- API keys are securely stored in GitHub
- Terraform uses secrets dynamically
- No sensitive data is exposed in code
