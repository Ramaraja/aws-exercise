### **AWS CloudFront Example: Distribute a Static Website**

**Amazon CloudFront** to serve a static website hosted in an **Amazon S3 bucket**, enabling faster global delivery and caching.

---

### **Scenario**
- **Objective**: Use CloudFront to distribute a static website globally with caching and security.
- **Services Used**:
  - Amazon S3 (for hosting the static website content).
  - Amazon CloudFront (for content delivery).


### **Steps to Set Up CloudFront**

#### **1. Create an S3 Bucket**
1. Go to the **S3 Console**.
2. Create a bucket (e.g., `my-cloudfront-demo-bucket`).
3. Enable static website hosting:
   - Navigate to **Properties > Static Website Hosting**.
   - Enable and specify:
     - Index document: `index.html`
     - Error document: `error.html`

4. Upload website files:
   - `index.html`: Your homepage.
   - `error.html`: A custom error page.

#### Example `index.html` File:
```html
<!DOCTYPE html>
<html>
<head>
    <title>CloudFront Example</title>
</head>
<body>
    <h1>Welcome to My CloudFront-Enabled Website!</h1>
</body>
</html>
```


#### **2. Set Bucket Permissions**
1. Make the bucket public (optional, if using S3 bucket policy for CloudFront):
   - Go to **Permissions > Bucket Policy**.
   - Add the following policy:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:GetObject",
           "Resource": "arn:aws:s3:::my-cloudfront-demo-bucket/*"
         }
       ]
     }
     ```


#### **3. Create a CloudFront Distribution**
1. Go to the **CloudFront Console**.
2. Click **Create Distribution**.
3. Select **Web** as the delivery method.
4. Configure the distribution:
   - **Origin**:
     - Origin domain: Select your S3 bucket (or enter the URL if static hosting is enabled).
     - Origin Access: Use an **Origin Access Control (OAC)** to restrict direct access to the S3 bucket.
   - **Default Cache Behavior**:
     - Viewer Protocol Policy: Redirect HTTP to HTTPS.
     - Cache Policy: Use the default or create a custom policy.
   - **Distribution Settings**:
     - Enable logging (optional).
     - Price Class: Choose based on your geographic target (e.g., "US, Canada, Europe" or "All Locations").
5. Click **Create Distribution**.

---

#### **4. Test the CloudFront Distribution**
1. Wait for the distribution to be deployed (status changes to **Deployed**).
2. Access your website using the CloudFront domain name (e.g., `https://<CloudFront-URL>`).

---
