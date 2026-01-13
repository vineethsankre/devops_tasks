# 🌐 AWS Task-6 — Route 53 + ALB + HTTPS Setup  
**Date:** `12-01-2026`
**Domain:** `vineethsworld.xyz`

---

## 🧭 Objective
Connect a custom domain to an Application Load Balancer using Route 53 and secure it with HTTPS using ACM.

---

## 🌍 1. Create Route 53 Hosted Zone

1. Go to **AWS Console → Route 53 → Hosted Zones → Create hosted zone**
2. Enter:
   - **Domain name:** `vineethsworld.xyz`
   - **Type:** Public hosted zone
3. Click **Create hosted zone**

AWS will generate 4 Name Servers:
ns-xxx.awsdns-xx.com
ns-xxx.awsdns-xx.net
ns-xxx.awsdns-xx.org
ns-xxx.awsdns-xx.co.uk

---

## 🌐 2. Update Nameservers in Domain Provider

1. Login to your domain registrar (GoDaddy / Namecheap / etc)
2. Open **DNS / Nameservers**
3. Choose **Custom nameservers**
4. Replace existing values with the 4 AWS nameservers
5. Save and wait 5–30 minutes (can take up to 24 hrs)

---

## 🌍 3. Create Root Domain → ALB (Alias A Record)

1. Go to **Route 53 → Hosted zones → vineethsworld.xyz → Create record**
2. Enter:

| Field | Value |
|------|-------|
| Record name | *(leave empty)* |
| Record type | A |
| Alias | ON |
| Route traffic to | Application Load Balancer |
| Region | us-east-1 |
| Load balancer | your-alb.amazonaws.com |

3. Click **Create**

Now:
vineethsworld.xyz → ALB

---

## 🌐 4. Create www Subdomain (CNAME)

Create another record:

| Field | Value |
|------|-------|
| Record name | www |
| Type | CNAME |
| Value | your-alb.amazonaws.com |

Save.

Now:
www.vineethsworld.xyz → ALB

---

## 🔐 5. Request SSL Certificate (ACM)

1. Go to **AWS Certificate Manager (us-east-1)**
2. Click **Request certificate**
3. Add:
   - vineethsworld.xyz
   - www.vineethsworld.xyz
4. Select **DNS validation**
5. Click **Request**

---

## 🧾 6. Validate Certificate Using DNS

1. ACM will show CNAME records
2. Click **Create records in Route 53**
3. Wait until status becomes:
   Issued

---

## 🔒 7. Add HTTPS (443) Listener to ALB

1. Go to **EC2 → Load Balancers → myapp-alb → Listeners**
2. Click **Add listener**

| Field | Value |
|------|------|
| Protocol | HTTPS |
| Port | 443 |
| Certificate | Select ACM certificate |
| Default action | Forward to target group |

Save.

---

## 🔁 8. Redirect HTTP to HTTPS

1. Edit **HTTP : 80** listener
2. Change action to: Redirect to HTTPS : 443
3. Save

---

## 🎯 9. Create Target Group

1. Go to **EC2 → Target Groups → Create**
2. Configure:

| Setting | Value |
|-------|------|
| Target type | Instances |
| Protocol | HTTP |
| Port | 80 |
| Health check path | / |

3. Register your EC2 instances
4. Attach target group to HTTPS listener

---

## 🩺 10. Configure Health Checks

Target Group → Health checks:

| Field | Value |
|------|------|
| Path | / |
| Healthy threshold | 2 |
| Unhealthy threshold | 2 |

---

## 📊 11. Enable ALB Access Logs

1. Go to **EC2 → Load Balancers → myapp-alb → Attributes**
2. Enable:
   Access logs → ON
   S3 bucket → select bucket
3. Save

---

## ✅ 12. Verify Domain & HTTPS

Open in browser:
- https://vineethsworld.xyz
- https://www.vineethsworld.xyz

You should see:
- HTTPS 🔒
- Website loading

---

## 🌍 13. Check DNS Propagation

Go to:
https://dnschecker.org
Enter:
vineethsworld.xyz
Select **A Record**

Green = Live globally 🌍

---

## 🧠 Final Architecture

      vineethsworld.xyz
      ↓
      Route 53
      ↓
      ALB (HTTPS 443)
      ↓
      Target Group
      ↓
      EC2



