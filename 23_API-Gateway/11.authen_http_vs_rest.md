
### API Gateway – Security (Authentication and Authorization)

API Gateway offers several powerful mechanisms to secure your APIs, ensuring that only authorized users and applications can access your resources. These mechanisms primarily fall under authentication (verifying identity) and authorization (determining what an authenticated identity can do).

1.  **IAM Permissions:**
    * **Purpose:** IAM (Identity and Access Management) policies can be used to control access to your API Gateway endpoints. This method is especially useful for **internal applications** or when AWS services (like EC2 instances or Lambda functions) need to invoke your API.
    * **How it Works:** You create an IAM policy that specifies which API Gateway actions (e.g., `execute-api:Invoke`) are allowed or denied, and attach this policy to an IAM user or role.
    * **Leverages Signature Version 4 (SigV4):** When using IAM for authentication, clients must sign their HTTP requests with their AWS credentials (Access Key ID and Secret Access Key) using Signature Version 4. API Gateway then verifies this signature against the associated IAM policy.
    * **Benefits:** Strong security, granular control, and seamless integration with existing AWS IAM structures.
    * **Use Cases:** Providing access for EC2 instances, Lambda functions, or other AWS services to call your API Gateway; restricting API access to specific internal users or roles.

2.  **API Gateway – Resource Policies:**
    * **Purpose:** Resource policies in API Gateway are JSON policies that you attach directly to your API, similar to S3 bucket policies or Lambda resource policies. They allow you to define permissions at the API level.

* **How it Works :** These policies allow you to:
    * **Cross-Account Access:** Grant access to your API to users or roles in different AWS accounts. This works in conjunction with IAM policies attached to the users/roles in the other accounts.
    * **Source IP Filtering:** Restrict API access to specific IP address ranges (CIDR blocks).
    * **VPC Endpoint Access:** Control access when your API Gateway is configured as a private endpoint within a VPC.
    * **Evaluation Logic:** When an IAM principal attempts to access your API, both the IAM policy attached to the principal AND the API Gateway resource policy are evaluated. An explicit DENY in either policy will override any ALLOW. If there is no explicit DENY, an ALLOW in either policy will grant access.

3.  **Cognito User Pools (Authentication for External Users):**
    * **Purpose:** Amazon Cognito User Pools is a managed user directory service that allows you to easily add sign-up, sign-in, and access control to your web and mobile applications. API Gateway can directly integrate with Cognito User Pools to authenticate API requests.
    * **How it Works:**
        * Your client application first authenticates users with a Cognito User Pool.
        * Upon successful authentication, Cognito returns a JSON Web Token (JWT) to the client.
        * The client then includes this JWT in the authorization header of its requests to API Gateway.
        * API Gateway, configured with a Cognito User Pool authorizer, automatically verifies the JWT's signature and expiration. If the token is valid, the request proceeds.
    * **Benefits:** Fully managed user lifecycle, automatic token expiration, support for social identity providers (Facebook, Google), and no custom implementation required for authentication logic in your Lambda functions.
    * **Use Cases:** Securing APIs for consumer-facing mobile and web applications where you manage user identities.

4.  **Lambda Authorizer (formerly Custom Authorizers):**
    * **Purpose:** This is the most flexible authentication and authorization option, allowing you to implement custom authorization logic using an AWS Lambda function.
    * **How it Works:**
        * When a request comes to an API Gateway method protected by a Lambda authorizer, API Gateway invokes your designated Lambda function.
        * The Lambda function receives the incoming request's authorization token (e.g., a bearer token, JWT, or custom parameter).
        * Your Lambda function then performs custom logic (e.g., validating a third-party JWT, looking up user roles in a database, calling an external identity provider).
        * The Lambda function must return an **IAM policy** (an `Allow` or `Deny` policy) for the user, specifying what resources and actions the user is authorized to perform.
        * API Gateway caches the policy for a configurable TTL to reduce Lambda invocations.
    * **Benefits:** Highly flexible for integrating with any custom or third-party authentication system (e.g., OAuth, custom JWTs from external providers).
    * **Pricing:** You pay for each Lambda invocation of your authorizer function.
    * **Use Cases:** Integrating with legacy authentication systems, implementing complex multi-factor authorization, or using identity providers not natively supported by Cognito.

**Summary of Security Options:**

| Feature                | IAM Permissions                                     | Cognito User Pools                                  | Lambda Authorizer                                         |
| :--------------------- | :-------------------------------------------------- | :-------------------------------------------------- | :-------------------------------------------------------- |
| **Authentication** | AWS credentials (SigV4)                             | JWT from Cognito User Pool                          | Custom token/parameter (validated by Lambda)              |
| **Authorization** | IAM Policy attached to principal + Resource Policy  | API Gateway method authorization                    | IAM Policy returned by Lambda function                    |
| **Best For** | Internal AWS applications, AWS service access       | Consumer-facing web/mobile apps with managed users  | Custom identity providers, complex authorization logic    |
| **Code Required** | No custom code (client uses AWS SDK/SigV4 signing) | No custom code in backend (Cognito handles auth)    | Yes, custom Lambda function for authorization logic       |
| **Token Expiration** | Managed by STS temporary credentials                | Managed by Cognito User Pool tokens                 | Defined and managed by your Lambda authorizer             |

**5. HTTPS VS REST**
![](/resource/image_28.png)