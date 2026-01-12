# AWS Transit Gateway Demo

## 📌 Objective
Learn how to create and use an AWS Transit Gateway (TGW) to establish a "hub-and-spoke" network architecture. This project connects three isolated VPCs (A, B, and C) through a central Transit Gateway, allowing them to communicate via private IP addresses.

## 🏗 Architecture
* **Central Hub:** AWS Transit Gateway
* **Spokes:** 3 separate VPCs with non-overlapping CIDR blocks.
* **Verification:** ICMP (Ping) connectivity between EC2 instances in different VPCs.

## ⚙️ Prerequisites
* AWS Account
* Basic understanding of VPCs, Subnets, and Route Tables.

---

## 🚀 Implementation Steps

### 1. Create VPCs
Create three VPCs to simulate separate networks.
* **VPC A:** CIDR `10.1.0.0/16` (Subnet: `10.1.1.0/24`)
* **VPC B:** CIDR `10.2.0.0/16` (Subnet: `10.2.1.0/24`)
* **VPC C:** CIDR `10.3.0.0/16` (Subnet: `10.3.1.0/24`)
* *Note: Ensure "Auto-assign public IPv4" is enabled for subnets to allow SSH access.*

### 2. Create Transit Gateway
1.  Navigate to **VPC Dashboard** > **Transit Gateways**.
2.  Click **Create Transit Gateway**.
3.  **Name:** `My-Demo-TGW`.
4.  **Options:** Enable "Auto accept shared attachments".

### 3. Attach VPCs to Transit Gateway
1.  Navigate to **Transit Gateway Attachments**.
2.  Create an attachment for each VPC.
3.  **Important:** Ensure you select the correct subnets where your resources reside.

### 4. Configure Routing
Update the **Route Tables** for *each* VPC (A, B, and C) to direct internal traffic to the Gateway.
* **Destination:** `10.0.0.0/8` (Covers all 3 VPC CIDRs).
* **Target:** `Transit Gateway` (Select `My-Demo-TGW`).

### 5. Configure Security Groups
Update the **Security Groups** attached to your EC2 instances to allow traffic.
* **Type:** `All ICMP - IPv4` (Allows Ping).
* **Source:** `10.0.0.0/8` (Allows traffic from any internal VPC).

---

## ✅ Verification
1.  Launch EC2 instances in **VPC A** and **VPC B**.
2.  SSH into Instance A.
3.  Ping the **Private IP** of Instance B:
    ```bash
    ping 10.2.x.x
    ```
4.  Success is confirmed by receiving packet replies.

---

## 🧹 Cleanup
To avoid AWS charges (~$0.05/hr per attachment), delete resources in this order:
1.  Terminate all **EC2 Instances**.
2.  Delete **Transit Gateway Attachments** (Wait for them to disappear).
3.  Delete **Transit Gateway**.
4.  Delete **VPC A, B, and C**.
