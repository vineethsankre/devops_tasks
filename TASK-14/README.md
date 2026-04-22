# Deploy a Three-Tier E-Commerce Application on AWS EKS using Helm

## Overview

This project demonstrates how to deploy a **three-tier e-commerce
application** on **Amazon EKS** using **Helm** and the **AWS Load
Balancer Controller**.

Architecture layers:

-   **Presentation Layer** -- User interface accessed via browser
-   **Application Layer** -- Backend services containing business logic
-   **Data Layer** -- Databases and persistent storage

Kubernetes and Helm allow scalable and repeatable cloud-native
deployments.

------------------------------------------------------------------------

# Prerequisites

Install the following tools:

-   kubectl -- Kubernetes CLI\
    https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html

-   eksctl -- EKS cluster management tool\
    https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html

-   AWS CLI -- AWS command line tool\
    https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html

Configure AWS credentials:

``` bash
aws configure
```

------------------------------------------------------------------------

# Deployment Steps

## 1. Create EKS Cluster

``` bash
eksctl create cluster \
--name my-cluster \
--region ap-south-1 \
--nodegroup-name my-nodegroup \
--node-type t3.small \
--nodes 2 \
--nodes-min 1 \
--nodes-max 3
```

Verify cluster:

``` bash
kubectl get nodes
```

------------------------------------------------------------------------

## 2. Configure IAM OIDC Provider

``` bash
export cluster_name=my-cluster
```

``` bash
oidc_id=$(aws eks describe-cluster \
--name $cluster_name \
--query "cluster.identity.oidc.issuer" \
--output text | cut -d '/' -f 5)
```

Check if OIDC provider exists:

``` bash
aws iam list-open-id-connect-providers | grep $oidc_id
```

If not configured:

``` bash
eksctl utils associate-iam-oidc-provider \
--cluster $cluster_name \
--approve
```

------------------------------------------------------------------------

## 3. Install AWS Load Balancer Controller

Download IAM policy:

``` bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

Create IAM policy:

``` bash
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json
```

Create IAM role:

``` bash
eksctl create iamserviceaccount \
--cluster=my-cluster \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--role-name AmazonEKSLoadBalancerControllerRole \
--attach-policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/AWSLoadBalancerControllerIAMPolicy \
--approve
```

------------------------------------------------------------------------

## 4. Deploy Load Balancer Controller

``` bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

Install controller:

``` bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=my-cluster \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller \
--set region=ap-south-1 \
--set vpcId=<VPC-ID>
```

Verify deployment:

``` bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

------------------------------------------------------------------------

## 5. Configure Amazon EBS CSI Driver

Create IAM role:

``` bash
eksctl create iamserviceaccount \
--name ebs-csi-controller-sa \
--namespace kube-system \
--cluster my-cluster \
--role-name AmazonEKS_EBS_CSI_DriverRole \
--role-only \
--attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
--approve
```

Install EBS CSI driver addon:

``` bash
eksctl create addon \
--name aws-ebs-csi-driver \
--cluster my-cluster \
--service-account-role-arn arn:aws:iam::<ACCOUNT-ID>:role/AmazonEKS_EBS_CSI_DriverRole \
--force
```

------------------------------------------------------------------------

## 6. Deploy Application using Helm

Clone the repository:

``` bash
git clone https://github.com/uniquesreedhar/RobotShop-Project.git
```

Navigate to Helm chart:

``` bash
cd RobotShop-Project/EKS/helm
```

Create namespace:

``` bash
kubectl create ns robot-shop
```

Install Helm chart:

``` bash
helm install robot-shop . -n robot-shop
```

Verify pods:

``` bash
kubectl get pods -n robot-shop
```

------------------------------------------------------------------------

## 7. Create Ingress

``` bash
kubectl apply -f ingress.yaml
```

Retrieve ingress URL:

``` bash
kubectl get ingress -n robot-shop
```

Open the Load Balancer DNS in your browser.

------------------------------------------------------------------------

# Application Workflow

Users can:

1.  Register and login
2.  Browse products
3.  Add items to cart
4.  Checkout and place orders

------------------------------------------------------------------------

# Cleanup

Delete the cluster if no longer required:

``` bash
eksctl delete cluster --name my-cluster
```


