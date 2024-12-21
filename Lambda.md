### **AWS Lambda Example: A Simple Event-Driven Function**

AWS Lambda function triggered by an Amazon S3 event to process a file uploaded to an S3 bucket.

---

### **Scenario**
- **Objective**: Automatically process text files uploaded to an S3 bucket by counting the number of words in the file.
- **Trigger**: S3 event notification.
- **Services Used**:
  - AWS Lambda
  - Amazon S3

---

### **Steps to Set Up the Lambda Function**

#### **1. Create an S3 Bucket**
1. Go to the **S3 Console**.
2. Create a bucket (e.g., `my-lambda-demo-bucket`).
3. Enable event notifications:
   - Navigate to **Properties > Event Notifications**.
   - Add a notification for **PUT** events (file uploads).
   - Choose to send events to a Lambda function.

---

#### **2. Create the Lambda Function**
1. Go to the **Lambda Console**.
2. Click **Create function**.
3. Choose **Author from scratch**.
4. Configure the function:
   - Name: `S3FileProcessor`
   - Runtime: **Python 3.x** (or another language of your choice)
   - Role: Create a new role with S3 access.

---

#### **3. Write the Lambda Function Code**
Here’s a sample Python code for the Lambda function:

```python
import json
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')

    # Get bucket name and file key from the event
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    file_key = event['Records'][0]['s3']['object']['key']
    
    # Fetch the file content
    file_object = s3.get_object(Bucket=bucket_name, Key=file_key)
    file_content = file_object['Body'].read().decode('utf-8')
    
    # Process the file: Count the number of words
    word_count = len(file_content.split())
    
    print(f"File '{file_key}' in bucket '{bucket_name}' has {word_count} words.")
    
    return {
        'statusCode': 200,
        'body': json.dumps(f"Processed {file_key} with {word_count} words.")
    }
```

---

#### **4. Configure Permissions**
Ensure the Lambda function has the necessary permissions to:
- Read from the S3 bucket.
- Write logs to Amazon CloudWatch.

#### **5. Add the S3 Trigger**
1. Go to the Lambda function **Configuration > Triggers**.
2. Add the S3 bucket created earlier as the event source.
3. Select **PUT** events for the trigger.

---

### **Test the Function**

1. **Upload a File to S3**:
   - Upload a `.txt` file containing text to the S3 bucket (e.g., `example.txt`).
   
2. **View the Lambda Execution**:
   - Go to the Lambda console and check the **Monitoring** tab for execution results.
   - Check **CloudWatch Logs** for details about the processed file and word count.

---
