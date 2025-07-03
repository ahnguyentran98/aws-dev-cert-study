
### API Gateway Stages, Deployments, and Stage Configuration: A Hands-On Guide

API Gateway stages allow you to manage and deploy different versions of your API, providing a powerful way to handle development, testing, and production environments.

#### Core Concepts Explained:

  * **API (Application Programming Interface):** A set of defined rules that enable applications to communicate with each other. In API Gateway, this is your overarching service (e.g., "MyWebAppAPI").
  * **Deployment:** The act of taking a snapshot of your API's configuration and making it live and accessible. Any changes you make to your API methods, integrations, or security settings in the console (or via CLI/SDK) won't take effect until you create a new deployment.
  * **Stage:** A named reference to a specific deployment of your API. Stages allow you to manage different environments (e.g., `dev`, `test`, `prod`) for the same API. Each stage gets its own unique URL.
  * **Stage Variables:** Key-value pairs that you can define for each stage. These are like environment variables, allowing you to dynamically configure your API's behavior without redeploying the entire API. This is especially useful for pointing to different backend resources (like Lambda function aliases or different HTTP endpoints) across stages.

-----

#### Hands-On Walkthrough: Image Upload API (Conceptual)

We'll conceptually build upon the image upload feature we discussed, showing how stages help manage it.

**Prerequisites:**

1.  **An AWS Account:** With sufficient permissions to create API Gateway, Lambda, and S3 resources.
2.  **An S3 Bucket:** Create an S3 bucket (e.g., `your-unique-bucket-for-images`).
3.  **A Lambda Function:**
      * Go to the Lambda console.
      * Click "Create function".
      * Choose "Author from scratch".
      * **Function name:** `ImageUploaderLambda`
      * **Runtime:** Node.js 18.x (or Python 3.9)
      * **Architecture:** `x86_64`
      * **Execution role:** Create a new role with basic Lambda permissions. **Crucially, edit this role** after creation to add permissions to **`s3:PutObject`** for your S3 bucket (e.g., `arn:aws:s3:::your-unique-bucket-for-images/*`).
      * **Function Code (Node.js example):**
        ```javascript
        const AWS = require('aws-sdk');
        const s3 = new AWS.S3();

        exports.handler = async (event) => {
            const bucketName = process.env.BUCKET_NAME; // Stage variable will populate this
            const fileName = event.queryStringParameters && event.queryStringParameters.name ? event.queryStringParameters.name : `image-${Date.now()}.jpg`;

            try {
                // Assuming base64 encoded image in the body
                const imageBuffer = Buffer.from(event.body, 'base64');

                await s3.putObject({
                    Bucket: bucketName,
                    Key: fileName,
                    Body: imageBuffer,
                    ContentType: 'image/jpeg' // Adjust based on your image type
                }).promise();

                return {
                    statusCode: 200,
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ message: `Successfully uploaded ${fileName} to ${bucketName}` })
                };
            } catch (error) {
                console.error("Upload error:", error);
                return {
                    statusCode: 500,
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ message: "Failed to upload image", error: error.message })
                };
            }
        };
        ```
      * Set **Timeout** for the Lambda function to at least **10 seconds** under "Configuration" -\> "General configuration".

-----

#### Step 1: Create a REST API in API Gateway

1.  **Navigate to API Gateway:** Open the AWS Management Console and search for "API Gateway".
2.  **Create API:** Click on "Create API".
3.  **Choose REST API:** Under "REST API", click "Build".
4.  **Settings:**
      * **API name:** `ImageUploadAPI`
      * [cite\_start]**Endpoint Type:** Choose `Edge optimized` for lower latency for global clients. [cite: 288]
      * Click "Create API".

-----

#### Step 2: Create a Resource and Method

1.  **Create Resource:**
      * In your `ImageUploadAPI`, click on "Resources" in the left navigation pane.
      * Click "Actions" -\> "Create Resource".
      * **Resource Name:** `upload`
      * **Resource Path:** `/upload`
      * Click "Create Resource".
2.  **Create Method:**
      * Select the newly created `/upload` resource.
      * Click "Actions" -\> "Create Method".
      * Choose `PUT` from the dropdown.
      * [cite\_start]**Integration type:** `Lambda Function` [cite: 686]
      * **Use Lambda proxy integration:** Check this box. [cite\_start](This passes the raw request to Lambda and expects a raw HTTP response back, simplifying Lambda code.) [cite: 696]
      * **Lambda Region:** Select your AWS Region.
      * **Lambda Function:** Type `ImageUploaderLambda` (or search for it) and select your function.
      * Click "Create method".
      * You will be prompted to grant API Gateway permissions to invoke your Lambda function. Click "OK".

-----

#### Step 3: Deploy to a 'dev' Stage

Now that you've configured your API, you need to deploy it to make it accessible.

1.  **Deploy API:**

      * In the left navigation pane, click "Deploy API".
      * **Deployment stage:** Select `[New Stage]` from the dropdown.
      * [cite\_start]**Stage name:** `dev` [cite: 690]
      * **Stage description:** `Development environment`
      * Click "Deploy".

2.  **Test the `dev` Stage:**

      * After deployment, you'll see the "Stage Editor" for the `dev` stage.
      * Copy the `Invoke URL`. It will look something like `https://abcdef123.execute-api.us-east-1.amazonaws.com/dev`.
      * To test, you'll need a tool like Postman or `curl`.
          * **Method:** `PUT`
          * **URL:** `[Your Invoke URL]/upload?name=testimage.jpg`
          * **Headers:** `Content-Type: image/jpeg` (or appropriate image type)
          * **Body:** Select `binary` and upload a small image file.
      * You should receive a `200 OK` response with a success message if everything is configured correctly.

-----

#### Step 4: Add Stage Variables

Stage variables allow you to configure different Lambda aliases or other backend details per stage.

1.  **Add Stage Variable to `dev`:**

      * In the "Stage Editor" for the `dev` stage, click on the `dev` stage name in the left navigation pane.
      * Click on the "Stage Variables" tab.
      * Click "Add stage variable".
      * **Name:** `BUCKET_NAME`
      * **Value:** `your-unique-bucket-for-images` (the name of your S3 bucket)
      * Click "Add".

2.  **Configure Lambda to use Stage Variable:**

      * Go back to your `ImageUploaderLambda` function in the Lambda console.
      * Go to "Configuration" -\> "Environment variables".
      * Click "Edit" and add a new environment variable:
          * **Key:** `BUCKET_NAME`
          * **Value:** `YOUR_DEV_BUCKET_NAME` (For now, put a placeholder or your dev bucket. We'll simulate different buckets for prod and dev.)
      * **Important:** This environment variable in Lambda acts as a default. API Gateway's stage variable will **override** it if configured correctly through the integration.

3.  **Link Lambda Integration to Stage Variable (Crucial Step):**

      * Go back to API Gateway, select your `ImageUploadAPI`.
      * Click "Resources" -\> `PUT /upload`.
      * In the "Integration Request" box, you'll see "Lambda Function". Click the pencil icon next to the Lambda function name.
      * Change the Lambda function to reference the stage variable. The format is `${stageVariables.BUCKET_NAME}`.
          * **Example (for Node.js runtime):** `arn:aws:lambda:us-east-1:123456789012:function:ImageUploaderLambda:${stageVariables.LAMBDA_ALIAS}` (Replace `LAMBDA_ALIAS` with an actual alias if you're using them, otherwise just the function name if you want the `$LATEST` for simplicity, but using aliases is best practice for stages).
          * **Correction for simple setup without aliases (for the sake of this example):** For this simpler example, you'd typically have your Lambda function accept the `BUCKET_NAME` from the event itself, or use environment variables set on the Lambda function that are *managed* by the stage variable. Let's refine the Lambda environment variable part.

    **Revised Lambda Integration with Stage Variables (More Common Approach):**

    Instead of direct Lambda ARN manipulation in the API Gateway integration for bucket names, it's often cleaner to pass values through.

      * **In API Gateway Method (`PUT /upload` -\> Integration Request):**

          * Make sure "Use Lambda proxy integration" is checked. This passes all request details, including query string parameters and headers, to your Lambda function.
          * In your Lambda function code, you're already reading `process.env.BUCKET_NAME`. We will define this as a stage variable.

      * **Define Stage Variable in API Gateway:**

          * For the `dev` stage, create a stage variable named `BUCKET_NAME` with the value `your-dev-bucket-name`.

      * **Lambda Function Configuration (No direct override needed by API Gateway for env vars):**

          * The Lambda function itself **doesn't need an environment variable named `BUCKET_NAME`** if you're pulling it from the event or a stage variable. My example code reads from `process.env.BUCKET_NAME`. For this to work dynamically with stages, you'd set the `BUCKET_NAME` environment variable on the *Lambda function itself*, and then use a stage variable to *override* that value or influence how the Lambda is invoked.

    **Simpler approach for this guide: Use a stage variable to dynamically select the Lambda alias.**

    Let's create a Lambda alias for `dev`.

      * In Lambda, go to `ImageUploaderLambda`.
      * Click "Aliases" under "Versions".
      * Create a new alias named `dev`, pointing to `$LATEST`.
      * Now, in API Gateway:
          * Go to `PUT /upload` method.
          * In the "Integration Request" box, click the pencil next to "Lambda Function".
          * For "Lambda Function", enter: `ImageUploaderLambda:${stageVariables.LAMBDA_ALIAS}`
          * Save the integration.
      * Go to `dev` stage -\> "Stage Variables" tab.
      * Add a stage variable:
          * **Name:** `LAMBDA_ALIAS`
          * **Value:** `dev`
      * **Crucial:** You still need to manage which S3 bucket your `dev` Lambda alias points to. The best way is via Lambda Environment Variables that vary per alias.
          * Go to `ImageUploaderLambda` in Lambda.
          * Go to the `dev` alias.
          * Under "Configuration" -\> "Environment variables" for the `dev` alias, add:
              * **Key:** `BUCKET_NAME`
              * **Value:** `your-dev-bucket-name`

-----

#### Step 5: Deploy to a 'prod' Stage (Blue/Green like scenario)

Now, let's imagine you've made changes and want to deploy a "production" version.

1.  **Make a minor Lambda change (simulate a new version):**

      * Go to your `ImageUploaderLambda` in Lambda.
      * Change the success message slightly, e.g., `Successfully uploaded ${fileName} to ${bucketName} (Prod Version)`
      * **Deploy as a new Lambda Version:** Click "Deploy" to save changes to `$LATEST`. Then, under "Actions", click "Publish new version". Note the new version number (e.g., Version 2).

2.  **Create a `prod` Lambda Alias:**

      * Go to `ImageUploaderLambda` -\> "Aliases".
      * Create a new alias named `prod`, pointing to your new Lambda version (e.g., Version 2).
      * For the `prod` alias, set its environment variable:
          * **Key:** `BUCKET_NAME`
          * **Value:** `your-prod-bucket-name` (create a separate S3 bucket for prod if you want a true isolation for this example, otherwise use the same one)

3.  **Deploy API Gateway to `prod` Stage:**

      * In API Gateway, `ImageUploadAPI`, click "Deploy API".
      * **Deployment stage:** Select `[New Stage]` from the dropdown.
      * [cite\_start]**Stage name:** `prod` [cite: 690]
      * **Stage description:** `Production environment`
      * Click "Deploy".

4.  **Configure `prod` Stage Variables:**

      * In the "Stage Editor" for the `prod` stage, click on the `prod` stage name.
      * Click "Stage Variables" tab.
      * Add a stage variable:
          * **Name:** `LAMBDA_ALIAS`
          * **Value:** `prod`
      * (Optional) If you were using a different S3 bucket for production and passing it via a separate stage variable in the Lambda integration, you'd define that here too.

5.  **Test the `prod` Stage:**

      * Copy the `Invoke URL` for the `prod` stage (it will have `/prod` in the path).
      * Use Postman/curl again with the `prod` URL and upload an image.
      * You should see the "Prod Version" message in the response, indicating it's using the new Lambda alias and potentially a different S3 bucket (if you configured it that way).

-----

#### Clean Up (Important\!)

To avoid unexpected charges, remember to clean up the resources you've created.

1.  **Delete API Gateway Stages:**
      * In API Gateway, go to your `ImageUploadAPI`.
      * In the left navigation, click "Stages".
      * Select the `dev` stage, click "Delete Stage". Confirm.
      * Repeat for the `prod` stage.
      * Once all stages are deleted, you can delete the API itself by going to "Actions" -\> "Delete API".
2.  **Delete Lambda Function and Aliases:**
      * In Lambda, select `ImageUploaderLambda`.
      * First, delete any aliases (e.g., `dev`, `prod`).
      * Then, delete all versions of the function (except `$LATEST`, which you can just delete the function to remove).
      * Finally, delete the `ImageUploaderLambda` function.
3.  **Delete Lambda Role:**
      * In IAM, go to "Roles".
      * Search for the role created for your Lambda function (e.g., `ImageUploaderLambda-role-...`).
      * Select it and click "Delete role".
4.  **Empty and Delete S3 Buckets:**
      * In S3, navigate to `your-dev-bucket-name` and `your-prod-bucket-name`.
      * Select all objects and delete them.
      * Then, delete the buckets themselves.

-----