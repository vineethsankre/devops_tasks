# Task 4: Auto Scaling in AWS (EC2 + ALB)

This document outlines the steps to configure Auto Scaling with an Application Load Balancer (ALB) and EC2 instances on AWS.

## Architecture Flow

**User** $\rightarrow$ **ALB** $\rightarrow$ **Target Group** $\rightarrow$ **Auto Scaling Group** $\rightarrow$ **EC2 Instances**
*(Scaling Trigger: CPU > 70% triggers scale out)*

---

## Step 1: Create Launch Template for EC2 Instances

1.  Navigate to **AWS Console** $\rightarrow$ **EC2** $\rightarrow$ **Launch Templates**.
2.  Click **Create launch template**.
3.  **Configure Settings:**
    * **Launch template name:** `webserver-lt`
    * **AMI:** Amazon Linux 2
    * **Instance type:** `t2.micro` (Free-tier eligible)
    * **Key pair:** Select your existing key pair
    * **Security group:** Create a new SG or select an existing one with:
        * Allow **HTTP (80)** from `0.0.0.0/0`
        * Allow **SSH (22)** from `My IP`
    * **IAM role:** (Optional) Attach EC2 role with S3 access if required.
4.  **Advanced details (User Data):**
    Paste the following script to install and start Apache automatically:
    ```bash
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "<h1>Auto Scaling Instance - $(hostname)</h1>" > /var/www/html/index.html
    ```
5.  Click **Create launch template**.

---

## Step 2: Create Application Load Balancer (ALB)

### A. Create Target Group
1.  Navigate to **EC2** $\rightarrow$ **Target Groups**.
2.  Click **Create target group**.
3.  **Configure:**
    * **Type:** Instances
    * **Protocol:** HTTP
    * **Port:** 80
    * **Health check path:** `/`
    * **Name:** `asg-tg`

### B. Create ALB
1.  Navigate to **EC2** $\rightarrow$ **Load Balancers** $\rightarrow$ **Create Load Balancer**.
2.  Choose **Application Load Balancer**.
3.  **Configure:**
    * **Name:** `web-alb`
    * **Scheme:** Internet-facing
    * **IP type:** IPv4
    * **Listener:** HTTP (80)
    * **VPC:** Default
    * **Subnets:** Select at least 2 Availability Zones (AZs)
    * **Security Group:** Allow HTTP (80)
    * **Listener Routing:** Forward traffic to Target Group `asg-tg`.

---

## Step 3: Configure Auto Scaling Group (Linked to ALB)

1.  Navigate to **EC2** $\rightarrow$ **Auto Scaling Groups** $\rightarrow$ **Create**.
2.  **Name:** `web-asg`
3.  **Launch Template:** Select `webserver-lt`.
4.  **Network:** Choose VPC & select minimum 2 subnets.
5.  **Group Size:**
    * **Desired:** 2
    * **Minimum:** 1
    * **Maximum:** 4
6.  **Load Balancing:**
    * Select **Attach to existing load balancer**.
    * Choose **Application Load Balancer**.
    * **Target group:** `asg-tg`.
    * Enable **ELB health checks**.

---

## Step 4: Set Scaling Policy (CPU > 70%)

1.  Go to the **Auto Scaling Group** created (`web-asg`).
2.  Click the **Automatic scaling** tab.
3.  Click **Create dynamic scaling policy**.
4.  **Policy Type:** Target tracking scaling.
5.  **Metric type:** Average CPU Utilization.
6.  **Target value:** `70`
7.  **Instance warm-up:** `300` seconds.

> **Note:** AWS will now:
> * 📈 Scale **OUT** when CPU > 70%
> * 📉 Scale **IN** when CPU < 70%

---

## Step 5: Verify Auto Scaling (Optional)

To test the configuration, perform a stress test on the CPU.

1.  **SSH** into one of the running EC2 instances.
2.  Install and run `stress`:
    ```bash
    sudo yum install stress -y
    stress --cpu 2 --timeout 300
    ```
3.  **Monitor:**
    * Go to **CloudWatch** $\rightarrow$ **Metrics** $\rightarrow$ **Auto Scaling**.
    * Observe the instance count increase as CPU load rises.

---

## Final Checklist

- [x] Launch Template created (`webserver-lt`)
- [x] ALB configured (`web-alb`)
- [x] Target Group attached (`asg-tg`)
- [x] Auto Scaling Group linked (`web-asg`)
- [x] CPU-based scaling enabled (Target: 70%)
