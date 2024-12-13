**AWS Security Group Exercise** to help you understand how to create, configure, and manage security groups in AWS:

---

### **Exercise Objective: Create and Apply a Security Group**

You will:
1. Create a security group with custom inbound and outbound rules.
2. Attach the security group to an EC2 instance.
3. Test the security group configuration.
4. Modify rules to demonstrate their impact.

---

### **Prerequisites**
- An AWS account.
- Access to the AWS Management Console or AWS CLI.
- A running EC2 instance or ability to launch one.

---

### **Step-by-Step Exercise**

---

#### **Step 1: Create a Security Group**
1. **Login**: Sign in to the AWS Management Console.
2. **Navigate**: Go to the **EC2 Dashboard**.
3. **Select Security Groups**:
   - In the left-hand menu, under "Network & Security," click **Security Groups**.
   - Click **Create Security Group**.
4. **Configure Security Group**:
   - **Name**: `MySecurityGroup`
   - **Description**: "Security group for testing inbound and outbound rules."
   - **VPC**: Select the default VPC or the VPC where your resources reside.
5. **Add Inbound Rules**:
   - Rule 1: **SSH (port 22)**, Source: Your IP (for SSH access).
   - Rule 2: **HTTP (port 80)**, Source: Anywhere (0.0.0.0/0).
6. **Add Outbound Rules**:
   - Leave default rules to allow all outbound traffic.
7. **Create**:
   - Click **Create Security Group**.

---

#### **Step 2: Attach the Security Group to an EC2 Instance**
1. **Navigate to EC2 Instances**:
   - Select an existing EC2 instance or launch a new one.
2. **Edit Security Groups**:
   - Select the instance and click **Actions > Security > Change Security Groups**.
   - Attach the `MySecurityGroup` to the instance.
3. **Verify**:
   - Ensure the security group is listed under the instance's **Security** tab.

---

#### **Step 3: Test Security Group Rules**
1. **Test SSH Access**:
   - Use the following command to SSH into the instance:
     ```bash
     ssh -i <your-key-pair.pem> ec2-user@<instance-public-ip>
     ```
   - Verify successful access.
2. **Test HTTP Access**:
   - If you have a web server running on the instance, access it via a browser:
     ```
     http://<instance-public-ip>
     ```
   - Verify access to the web server (e.g., Apache default page or your custom page).

---

#### **Step 4: Modify Security Group Rules**
1. **Restrict SSH Access**:
   - In the Security Group settings, edit the **Inbound Rules** and change the **Source** of the SSH rule to a specific IP range (e.g., `192.168.0.0/24`).
   - Try connecting via SSH from an unauthorized IP.
2. **Block HTTP Traffic**:
   - Remove the **HTTP (port 80)** rule.
   - Test web server access again; it should fail.

---

#### **Step 5: Clean Up**
1. **Detach Security Group**:
   - Go to the EC2 instance, and replace `MySecurityGroup` with the default security group.
2. **Delete Security Group**:
   - In the Security Groups menu, select `MySecurityGroup` and click **Delete**.

