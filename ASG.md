### **Exercise: Implementing Load Balancing and Auto Scaling in AWS**

This exercise demonstrates how to configure **Elastic Load Balancing (ELB)** and **Auto Scaling** in AWS to distribute traffic across multiple instances and automatically adjust capacity based on traffic demand.

---

### **Prerequisites**
1. AWS Account.
2. Basic familiarity with EC2, ELB, and Auto Scaling.
3. A web server setup script (optional, provided in Step 4).

---

### **Objective**
1. Launch multiple EC2 instances behind a Load Balancer (ALB).
2. Configure Auto Scaling to adjust the number of instances based on demand.
3. Test the setup by simulating traffic.

---

### **Step-by-Step Instructions**

---

#### **Step 1: Launch an EC2 Instance**
1. Go to the **EC2 Dashboard** and click **Launch Instances**.
2. Choose **Amazon Linux 2 AMI** or Ubuntu.
3. Select the instance type (`t2.micro` for free tier).
4. Configure the instance:
   - Add storage (default is fine).
   - Create or use an existing security group allowing:
     - **HTTP** (Port 80) from anywhere.
     - **SSH** (Port 22) from your IP.
5. Add a tag: `Key: Name, Value: WebServer`.
6. Review and launch.

---

#### **Step 2: Install a Web Server on the Instance**
1. SSH into the instance:
   ```bash
   ssh -i your-key.pem ec2-user@<Public-IP>
   ```
2. Install and start a web server:
   - Amazon Linux:
     ```bash
     sudo yum update -y
     sudo yum install -y httpd
     echo "Welcome to $(hostname)!" | sudo tee /var/www/html/index.html
     sudo systemctl start httpd
     sudo systemctl enable httpd
     ```
   - Ubuntu:
     ```bash
     sudo apt update
     sudo apt install -y apache2
     echo "Welcome to $(hostname)!" | sudo tee /var/www/html/index.html
     sudo systemctl start apache2
     sudo systemctl enable apache2
     ```

3. Confirm the web server is running by accessing the public IP in your browser.

---

#### **Step 3: Create a Target Group for Load Balancing**
1. Navigate to **EC2 Dashboard** > **Target Groups**.
2. Create a new **Target Group**:
   - Type: **Instances**.
   - Protocol: **HTTP**.
   - Port: **80**.
   - Health Check Path: `/`.
3. Register your running EC2 instance to the target group.

---

#### **Step 4: Create an Application Load Balancer (ALB)**
1. Go to **EC2 Dashboard** > **Load Balancers**.
2. Click **Create Load Balancer** > **Application Load Balancer**.
3. Configure the ALB:
   - Name: `WebApp-ALB`.
   - Scheme: Internet-facing.
   - Listeners: HTTP (Port 80).
   - Availability Zones: Select at least two.
4. Assign a security group allowing **HTTP** traffic (Port 80).
5. Register the target group created in Step 3.
6. Complete the setup and note the ALB DNS name.

---

#### **Step 5: Configure Auto Scaling**
1. Navigate to **Auto Scaling Groups**.
2. Click **Create Auto Scaling Group**.
3. Configure the Auto Scaling group:
   - Launch Template or Configuration:
     - Create a new one with the same AMI and instance type as the initial EC2 instance.
   - VPC and Subnets: Use the same as the Load Balancer.
   - Attach the Auto Scaling group to the target group.
4. Set Scaling Policies:
   - Minimum Instances: 1
   - Desired Instances: 1
   - Maximum Instances: 3
   - Add a scaling policy based on CPU utilization:
     - **Target Tracking Scaling Policy**: Target value of 50% CPU utilization.

---

#### **Step 6: Test the Setup**
1. Open the ALB DNS name in your browser. It should load the web page served by one of the instances.
2. Simulate high traffic by using a load testing tool (e.g., `ab` or `locust`):
   ```bash
   ab -n 10000 -c 100 http://<ALB-DNS-Name>/
   ```
3. Monitor Auto Scaling:
   - Go to the Auto Scaling group and observe the number of instances increasing.
   - Check the target group to see healthy instances being added.

---

#### **Step 7: Clean Up**
1. Terminate the EC2 instances.
2. Delete the Load Balancer.
3. Delete the Target Group.
4. Delete the Auto Scaling group.

---
