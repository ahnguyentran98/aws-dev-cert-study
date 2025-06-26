# 1. Lambda Container Images Concept

**Lambda Container Images** allow you to package your Lambda application, all dependencies, and even necessary data into a single image, upload it to **Amazon ECR** (Elastic Container Registry), and use this image to deploy your Lambda function.

- **Image size:** up to **10GB** (much larger than the usual zip file).
- **Best suited for:** Applications with many native libraries, complex environments, AI/ML, image processing, etc.

---

# 2. Lambda Container Images Workflow

**General workflow:**

1. **Build:** Write a `Dockerfile` using an AWS-provided base image (or your own).
2. **Push:** Build the image and push it to Amazon ECR.
3. **Deploy:** Create a Lambda function, select the image you just pushed to ECR as the source.

```
Application Code + Dependencies + Datasets
        ↓
Base Image (implement Lambda Runtime API)
        ↓
Build → Push to ECR → Deploy to Lambda
```

---

# 3. Basic Dockerfile Example

```dockerfile
# Use an image that implements the Lambda Runtime API
FROM amazon/aws-lambda-nodejs:12

# Copy application code into the image
COPY app.js package*.json ./

# Install dependencies
RUN npm install

# Define the function to run when Lambda is invoked
CMD [ "app.lambdaHandler" ]
```

> You can change the base image according to the language: Java, Python, .NET, Go, Ruby.

---

# 4. Test Container Locally with Lambda Runtime Interface Emulator

- AWS provides the **Lambda Runtime Interface Emulator** to test container images locally.
- Helps with debugging and optimization before deploying to the cloud.

---

# 5. Best Practices for Lambda Container Images

- **Use AWS base images:** Stable, secure, optimized for Lambda.
- **Multi-stage build:** Build artifacts in a separate stage, copy into the final image to optimize size.
- **Leverage Docker cache:** Place frequently changing commands (COPY code) at the end of the Dockerfile.
- **Reuse base image:** Create a custom base image if multiple functions share large libraries.
- **ECR layer comparison:** When pushing images, ECR only uploads new layers, saving bandwidth and reducing deployment time.

---

# 6. Quick Comparison with Traditional Deployment (zip file)

| Criteria                | Zip file (traditional)  | Lambda Container Image |
|-------------------------|------------------------|-----------------------|
| Max size                | 250MB uncompressed     | 10GB                  |
| Dependency packaging    | Complicated with native libs | Simple with Docker   |
| Custom runtime support  | Possible but hard to extend | Flexible, self-built |
| Environment management  | Limited                | Proactive, like local |

---

# 7. When Should You Use Lambda Container Images?

- Large Lambda applications, many dependencies, or require complex setup environments.
- Want to synchronize dev workflow with Docker.
- Need to overcome the 250MB limit of the traditional Lambda zip file.

---

# 8. Further Suggestions

- **Java + Spring:** Create a Dockerfile from the `amazon/aws-lambda-java` base image, copy the jar/war into the image, and configure the entrypoint as required by Lambda.
- **Test locally:**  
  ```sh
  docker run -p 9000:8080 <image-name>
  ```
  Then send a POST request to:  
  `localhost:9000/2015-03-31/functions/function/invocations`