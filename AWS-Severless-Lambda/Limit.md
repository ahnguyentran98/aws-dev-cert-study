# AWS Lambda Limits

---

## 1. Execution Limits

- **Memory allocation:** 128 MB – 10 GB (in 1 MB increments)
- **Maximum execution time (timeout):** 900 seconds (15 minutes)
- **Ephemeral storage (`/tmp`):** 512 MB – 10 GB (available only during execution)
- **Environment variables:** Up to 4 KB total

---

## 2. Deployment Limits

- **Deployment package size:**  
  - 50 MB (compressed .zip, direct upload)  
  - Up to 250 MB uncompressed (includes dependencies)
- **Layers:**  
  - Up to 5 layers per function  
  - Total of 250 MB (unzipped)
- **Container image size:** Up to 10 GB (if using container packaging)
- **Environment variables:** 4 KB per function

---

## 3. Concurrency & Invocations

- **Default concurrency limit:** 1,000 concurrent executions per region (can be increased by request)
- **Reserved concurrency:** Set per function to limit or guarantee capacity
- **Throttle behavior:**  
  - If concurrency limit is reached:  
    - **Synchronous calls:** Return 429 (ThrottleError)  
    - **Asynchronous calls:** Retried, then sent to Dead Letter Queue (DLQ) if configured

---

## 4. Other Limits

- **Ephemeral storage in `/tmp`:** Max 10 GB
- **File system (EFS) mounting:** Limited by EFS connection limits (one Lambda instance = one EFS connection)

---

## Summary Table

| Limit Type           | Value / Behavior                                 |
|----------------------|--------------------------------------------------|
| Memory               | 128 MB – 10 GB                                   |
| Timeout              | Max 15 minutes (900 seconds)                     |
| /tmp storage         | Up to 10 GB                                      |
| Deployment package   | 50 MB (zip upload), 250 MB (unzipped)            |
| Container image      | 10 GB (from ECR)                                 |
| Layers               | 5 per function, 250 MB total (unzipped)          |
| Env. variables       | 4 KB per function                                |
| Concurrency          | 1,000 concurrent executions (default)            |

---

## Real-World Example

Suppose your image-processing Lambda function needs to handle large images.  
If you try to process files bigger than 10 GB using `/tmp`, the function will fail due to the storage limit.

Or, if your function runs for over 15 minutes (for example, a heavy ETL job), it will be forcibly stopped by AWS Lambda.  
You will need to redesign the job to split it into smaller pieces.

---

> **Note:**  
> If you hit these limits and need more (e.g., higher concurrency), you can request an increase via AWS Support.