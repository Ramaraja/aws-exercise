**AWS Elastic Block Store (EBS) Exercise** to help you understand how to create, attach, use, and manage EBS volumes in AWS.

---

### **Exercise Objective: Work with EBS Volumes**

You will:
1. Create an EBS volume.
2. Attach it to an EC2 instance.
3. Format and mount the volume.
4. Perform basic file operations.
5. Detach and delete the EBS volume.

---

### **Prerequisites**
- An AWS account.
- An existing EC2 instance in the same Availability Zone (AZ) as the EBS volume.
- Basic knowledge of Linux commands.

---

### **Step-by-Step Exercise**

---

#### **Step 1: Create an EBS Volume**
1. **Login**: Sign in to the AWS Management Console.
2. **Navigate**: Go to the **EC2 Dashboard**.
3. **Create Volume**:
   - Click **Volumes** in the left-hand menu under "Elastic Block Store."
   - Click **Create Volume** and configure:
     - **Size**: 10 GiB
     - **Volume Type**: General Purpose SSD (gp3 or gp2)
     - **Availability Zone**: Same as your EC2 instance.
   - Click **Create Volume**.

---

#### **Step 2: Attach the Volume to an EC2 Instance**
1. **Select the Volume**:
   - Find the newly created volume in the Volumes list.
   - Select it and click **Actions > Attach Volume**.
2. **Attach**:
   - Choose your EC2 instance from the list.
   - Note the device name (e.g., `/dev/xvdf`).
   - Click **Attach Volume**.

---

#### **Step 3: Format and Mount the Volume**
1. **Connect to the EC2 Instance**:
   - SSH into your EC2 instance using your key pair:
     ```bash
     ssh -i <your-key-pair.pem> ec2-user@<instance-public-ip>
     ```
2. **Verify the Volume**:
   - Run the following command to confirm the volume is attached:
     ```bash
     lsblk
     ```
     You should see the attached volume (e.g., `/dev/xvdf`).
3. **Format the Volume**:
   - Use the `mkfs` command to format the volume with an ext4 file system:
     ```bash
     sudo mkfs.ext4 /dev/xvdf
     ```
4. **Create a Mount Point**:
   - Create a directory to mount the volume:
     ```bash
     sudo mkdir /mnt/ebs-volume
     ```
5. **Mount the Volume**:
   - Mount the volume to the directory:
     ```bash
     sudo mount /dev/xvdf /mnt/ebs-volume
     ```
6. **Verify the Mount**:
   - Run `df -h` to confirm the volume is mounted.

---

#### **Step 4: Perform File Operations**
1. **Create a File**:
   - Navigate to the mount point:
     ```bash
     cd /mnt/ebs-volume
     ```
   - Create a file:
     ```bash
     echo "Hello, EBS!" | sudo tee test-file.txt
     ```
2. **List Files**:
   - Verify the file exists:
     ```bash
     ls -l
     ```

---

#### **Step 5: Unmount, Detach, and Delete the Volume**
1. **Unmount the Volume**:
   - Unmount the volume from the instance:
     ```bash
     sudo umount /mnt/ebs-volume
     ```
2. **Detach the Volume**:
   - Go to the AWS Management Console.
   - Select the volume, click **Actions > Detach Volume**, and confirm.
3. **Delete the Volume**:
   - Once detached, select the volume and click **Actions > Delete Volume**.

---
