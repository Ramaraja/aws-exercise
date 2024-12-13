**AWS Elastic File System (EFS) Exercise** to help you learn how to create, mount, and use an EFS file system with EC2 instances.

---

### **Exercise Objective: Create and Use an EFS File System**

You will:
1. Create an EFS file system.
2. Mount the EFS file system on two EC2 instances.
3. Perform file operations to demonstrate shared storage.
4. Clean up resources.

---

### **Prerequisites**
- AWS account.
- Two EC2 instances in the same VPC and security group.
- Basic knowledge of Linux commands.

---

### **Step-by-Step Exercise**

---

#### **Step 1: Create an EFS File System**
1. **Login**: Sign in to the AWS Management Console.
2. **Navigate**: Go to the **EFS Dashboard**.
3. **Create File System**:
   - Click **Create File System** and configure:
     - **Name**: `MyEFS`
     - **VPC**: Select the same VPC as your EC2 instances.
   - Click **Create** to use the default settings.
4. **Access Points** *(Optional)*:
   - Create an access point if you want to enforce specific permissions for accessing the EFS.

---

#### **Step 2: Configure Security Group**
1. **Edit Security Group**:
   - Ensure the security group associated with your EC2 instances allows **NFS traffic (port 2049)**:
     - Inbound rule: Custom TCP Rule, Port Range: 2049, Source: Security group of the EC2 instances.

---

#### **Step 3: Mount EFS on EC2 Instances**
1. **Connect to EC2 Instances**:
   - SSH into both EC2 instances.
2. **Install NFS Utilities**:
   - Install the necessary tools to mount the EFS:
     ```bash
     sudo yum install -y amazon-efs-utils
     ```
3. **Mount the File System**:
   - Get the **EFS DNS Name** from the EFS dashboard (e.g., `fs-xxxxxx.efs.<region>.amazonaws.com`).
   - Create a mount point directory:
     ```bash
     sudo mkdir /mnt/efs
     ```
   - Mount the EFS file system:
     ```bash
     sudo mount -t efs fs-xxxxxx:/ /mnt/efs
     ```
   - Verify the mount:
     ```bash
     df -h
     ```
4. **Repeat on the Second Instance**:
   - Follow the same steps to mount the EFS on the second EC2 instance.

---

#### **Step 4: Perform File Operations**
1. **Create a File**:
   - On Instance 1, create a file:
     ```bash
     echo "Hello from Instance 1!" | sudo tee /mnt/efs/shared-file.txt
     ```
2. **Verify from Instance 2**:
   - On Instance 2, check if the file is visible:
     ```bash
     cat /mnt/efs/shared-file.txt
     ```
3. **Add Another File**:
   - On Instance 2, create another file:
     ```bash
     echo "Hello from Instance 2!" | sudo tee /mnt/efs/instance2-file.txt
     ```
4. **Verify from Instance 1**:
   - On Instance 1, check if the file is visible:
     ```bash
     cat /mnt/efs/instance2-file.txt
     ```

---

#### **Step 5: Clean Up**
1. **Unmount the File System**:
   - On both instances:
     ```bash
     sudo umount /mnt/efs
     ```
2. **Delete the EFS File System**:
   - Go to the EFS dashboard, select the file system, and click **Delete File System**.
3. **Terminate the EC2 Instances** (if created specifically for this exercise).

---
