# 🌐 Highly Available Web App on AWS with CloudFormation

Deploy a secure, resilient "Hello World" web application on AWS using **Infrastructure as Code (IaC)** with **AWS CloudFormation**. This template provisions a complete, production-ready architecture—including VPC, public/private subnets, NAT Gateway, Application Load Balancer, and Auto Scaling Group—all in a single stack.

> ✨ **Fully automated. One-click deploy. One-click destroy.**

---

## 📦 What’s Included

- **VPC** with CIDR `13.0.0.0/16`
- **Public & private subnets** in `us-east-1a` and `us-east-1b`
- **Internet Gateway** for public access
- **NAT Gateway** for private instances to download updates
- **Application Load Balancer (ALB)** in public subnets
- **Auto Scaling Group** with 2+ Amazon Linux 2 web servers in **private subnets**
- **Security groups** enforcing least-privilege access
- **CloudFormation outputs** with ALB DNS name

---

## 🖼️ Architecture

<img width="541" height="348" alt="image" src="https://github.com/user-attachments/assets/c9a5bcea-e686-42f3-968f-e691985563c3" />


> 💡 **Note**: Web servers run in **private subnets** and cannot be accessed directly from the internet—only via the ALB.

---

## 🛠️ Prerequisites

Before you deploy, ensure you have:

1. An **AWS account** with permissions to create:
   - VPC, EC2, ELB, Auto Scaling, NAT Gateway, EIP, Security Groups
   - (AdministratorAccess is sufficient for testing)
2. An **EC2 Key Pair** created in the **`us-east-1` (N. Virginia)** region  
   → [Create one here](https://console.aws.amazon.com/ec2/v2/home#KeyPairs)
3. Your **public IP address** (to restrict SSH access)  
   → Find it at [whatismyipaddress.com](https://whatismyipaddress.com/)
4. The **AWS CLI installed** (optional, for CLI deployment)

> ⚠️ **This template is designed for `us-east-1` only** (uses hardcoded AZs: `us-east-1a`, `us-east-1b`).
> ⚠️ Before you deploy, ensure to change the name in **number284** to your name.

---

## ▶️ Deploy via AWS Console

1. **Switch to `us-east-1`**  
   In the AWS Console, select **N. Virginia (us-east-1)** from the region dropdown (top-right).

2. **Open CloudFormation**  
   Go to the [CloudFormation Console](https://console.aws.amazon.com/cloudformation).

3. **Create Stack**  
   - Click **Create stack** → **With new resources (standard)**
   - Choose **Upload a template file** → Upload `ha-web-app.yaml`
   - Click **Next**

4. **Configure Stack**  
   - **Stack name**: e.g., `my-ha-web-app`
   - **Parameters**:
     - `KeyName`: Select your existing key pair
     - `SSHLocation`: Replace `0.0.0.0/0` with your IP (e.g., `203.0.113.25/32`)
     - (Leave other parameters as default)

5. **Review & Deploy**  
   - Click **Next** → **Next**
   - Check **"I acknowledge that AWS CloudFormation might create IAM resources."** (not required, but safe)
   - Click **Submit**

6. **Wait for Completion**  
   - Status: `CREATE_IN_PROGRESS` → `CREATE_COMPLETE` (~8–10 minutes)

---

## 🔍 Test Your Application

1. In the CloudFormation console, go to your stack → **Outputs** tab.
2. Copy the value of **`LoadBalancerDNS`**.
3. Open a browser and visit:  
   `http://<your-alb-dns-name>.us-east-1.elb.amazonaws.com`
   
<img width="903" height="292" alt="alb_page" src="https://github.com/user-attachments/assets/9c8e9e77-7635-4526-aa47-83ffa21f0cc3" />


✅ You should see:

> **Hello World! This was deployed through CloudFormation**  
> This is <your_name>!.

---

## 🧹 Clean Up (Avoid Unexpected Charges)

**NAT Gateway and EC2 instances incur hourly costs!** Delete the stack when done:

### Via Console:
- Select your stack → **Delete** → **Delete stack**

### Via CLI:
```bash
aws cloudformation delete-stack --stack-name my-ha-web-app --region us-east-1
```

> 💡 The entire environment—VPC, subnets, ALB, EC2, NAT Gateway—will be removed automatically.

---

## 📝 Notes & Best Practices

- 🔒 **Never use `SSHLocation: 0.0.0.0/0` in production**—always restrict to your IP.
- 🌍 **To deploy in another region**, update the AZs in the template (e.g., `eu-west-1a`, `eu-west-1b`).
- 🔄 **Reusability**: The template uses dynamic names (e.g., `${AWS::StackName}-web-lt`) to support multiple deployments.
- 🛡️ **Security**: Web servers run as the `apache` user with restricted file permissions.

---

> ✨ **Happy deploying!**  
> *Infrastructure as Code isn’t optional—it’s how we build reliably in the cloud.*
