**Elastic Load Balancer (ELB) Exercise** to help you understand how to create, configure, and test a load balancer in AWS.

---

### **Exercise Objective: Create and Test an Application Load Balancer**

You will:
1. Create an Application Load Balancer (ALB).
2. Register two EC2 instances with the ALB.
3. Test load balancing by accessing the ALB's DNS name.
4. Modify health checks and observe their impact.

---

### **Prerequisites**
- AWS account.
- Two EC2 instances running a basic web server (e.g., Apache or NGINX).
- Security groups that allow HTTP traffic (port 80) for the instances and the ALB.

---

### **Step-by-Step Exercise**

---

#### **Step 1: Launch and Configure Two EC2 Instances**
1. **Launch Instances**:
   - Launch two EC2 instances in the same VPC and Availability Zone.
   - Use the Amazon Linux 2 AMI with instance type `t2.micro`.
2. **Install Web Servers**:
   - SSH into each instance and install Apache:
     ```bash
     sudo yum update -y
     sudo yum install httpd -y
     sudo systemctl start httpd
     sudo systemctl enable httpd
     ```
   - Create custom index.html files to differentiate the instances:
     - Instance 1:
       ```bash
       echo "Instance 1: Hello from AWS!" | sudo tee /var/www/html/index.html
       ```
     - Instance 2:
       ```bash
       echo "Instance 2: Hello from AWS!" | sudo tee /var/www/html/index.html
       ```
3. **Test the Web Servers**:
   - Access the public IP of each instance in your browser to confirm the web servers are running.

---

#### **Step 2: Create an Application Load Balancer (ALB)**
1. **Navigate**:
   - Go to the **EC2 Dashboard** and select **Load Balancers** from the left menu.
2. **Create Load Balancer**:
   - Click **Create Load Balancer** and choose **Application Load Balancer**.
3. **Configure Basic Settings**:
   - **Name**: `MyALB`
   - **Scheme**: Internet-facing
   - **IP Address Type**: IPv4
   - **Listeners**: HTTP (port 80)
   - **Availability Zones**: Select the same VPC as your EC2 instances and include at least two subnets.
4. **Security Group**:
   - Assign a security group to the ALB that allows HTTP traffic (port 80) from anywhere.
5. **Target Group**:
   - Create a new target group:
     - **Name**: `MyTargetGroup`
     - **Target Type**: Instance
     - **Protocol**: HTTP
     - **Port**: 80
   - Register your two EC2 instances to this target group.

---

#### **Step 3: Test the Load Balancer**
1. **Find the DNS Name**:
   - Once the ALB is active, find its DNS name in the Load Balancer settings.
2. **Access the Load Balancer**:
   - Open the DNS name in your browser (e.g., `http://<ALB-DNS-Name>`).
   - Refresh the page multiple times to observe traffic being distributed between the two instances (you should see responses from both instances alternately).

---

#### **Step 4: Modify Health Checks**
1. **Default Health Check**:
   - By default, the ALB performs health checks on the `/` path.
   - Go to the **Target Groups** section, select `MyTargetGroup`, and view health check settings.
2. **Customize Health Checks**:
   - Change the **Health Check Path** to `/health`.
   - On both EC2 instances, create a simple health check file:
     ```bash
     echo "healthy" | sudo tee /var/www/html/health
     ```
   - Save the changes and observe the ALB detecting healthy targets.

---

#### **Step 5: Clean Up**
1. Delete the load balancer:
   - Navigate to the **Load Balancers** section, select `MyALB`, and delete it.
2. Delete the target group:
   - Go to **Target Groups**, select `MyTargetGroup`, and delete it.
3. Terminate the EC2 instances.

---
