# ⚡ Event-Driven Automation using S3, Lambda & CloudWatch

## 🧠 Overview
This project demonstrates a **serverless event-driven architecture** where an AWS Lambda function is automatically triggered whenever a file is uploaded to an S3 bucket.

- No servers to manage
- Fully automated workflow
- Real-time execution

---

## 🏗️ Architecture Flow

S3 Upload → Lambda Trigger → CloudWatch Logs

---

## ⚙️ AWS Services Used

| Service        | Purpose                                      |
|----------------|----------------------------------------------|
| Amazon S3      | Stores files and triggers events             |
| AWS Lambda     | Processes events automatically               |
| IAM            | Manages permissions securely                 |
| CloudWatch     | Logs and monitors execution                  |

---

## 🪜 Implementation Steps

### 1. Create Lambda Function
- Name: `processS3Upload`
- Runtime: Python 3.9 / Node.js 18
- Attach IAM Policies:
  - AWSLambdaBasicExecutionRole
  - AmazonS3ReadOnlyAccess

---

### 2. Lambda Code (Python)

```python
import json

def lambda_handler(event, context):
    print("=== FULL EVENT PAYLOAD ===")
    print(json.dumps(event, indent=2))

    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key']
        size = record['s3']['object']['size']

        print(f"File uploaded: {key} ({size} bytes) in bucket {bucket}")

    return {"status": "processed"}
```

---

### 3. Configure S3 Trigger
- Go to S3 → Bucket → Properties
- Create Event Notification:
  - Event Type: ObjectCreated (PUT)
  - Destination: Lambda (processS3Upload)

---

### 4. Test the Setup

Upload file using CLI:

```bash
aws s3 cp sample.txt s3://your-bucket-name/
```

---

### 5. Verify CloudWatch Logs

Log group:
```
/aws/lambda/processS3Upload
```

Expected output:
- Event payload
- File name
- File size
- Bucket name

---

### 6. IAM & Security Best Practices

- Use least privilege access
- Avoid AdministratorAccess
- Restrict S3 to invoke only specific Lambda
- Enable logging for debugging

---

## 🎯 Expected Outcome

| Action            | Result                          |
|------------------|----------------------------------|
| Upload file       | S3 triggers event                |
| Trigger Lambda    | Function executes automatically  |
| Log generation    | Visible in CloudWatch            |

---

## 🚀 Use Cases

- File processing pipelines
- Image/video processing
- Data ingestion systems
- Event-driven automation
- CI/CD triggers

---

## ⭐ Key Takeaways

- Event-driven architecture
- Serverless automation
- AWS integration (S3 + Lambda)
- Observability with CloudWatch
- Secure IAM practices

---

## 🚀 Final Result

**Event Flow:**

User Uploads File → S3 Event → Lambda Execution → CloudWatch Logs

✔ Fully automated  
✔ No manual intervention  
✔ Scalable and secure  

---


