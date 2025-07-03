Let's continue the summary of API Gateway concepts with Usage Plans & API Keys.

### API Gateway – Usage Plans & API Keys

If you intend to offer your API as a service or product to customers, Usage Plans and API Keys are essential for controlling and metering access[cite: 706].

**Usage Plan:**
A Usage Plan in API Gateway defines:
* **Who can access:** It specifies which deployed API stages and methods are accessible[cite: 706].
* **How much and how fast:** It sets limits on how much and how fast clients can access those APIs[cite: 706].
* **API Key Identification:** Usage Plans use API keys to identify API clients and meter their access[cite: 706].
* **Configurable Limits:** You can configure two main types of limits that are enforced on individual clients:
    * **Throttling Limits:** Control the steady-state rate (requests per second) and burst capacity (maximum concurrent requests) allowed for API calls[cite: 706].
    * **Quota Limits:** Define the overall number of maximum requests that a client can make within a specified time period (e.g., 10,000 requests per month)[cite: 706].

**API Keys:**
* API Keys are alphanumeric string values that you distribute to your customers[cite: 706].
* Clients include this API Key in the `x-api-key` header in their requests to your API Gateway[cite: 707].
* They are used in conjunction with Usage Plans to control access and meter usage[cite: 706].
* Throttling and quota limits defined in the Usage Plan are applied to these API Keys[cite: 706].

### Correct Order for API Keys (Configuration Process)

To correctly configure and implement a Usage Plan with API Keys, follow these steps[cite: 707]:

1.  **Create and Deploy APIs:** First, you need to create one or more APIs, configure their methods to require an API key, and then deploy these APIs to stages (e.g., `prod` stage)[cite: 707].
2.  **Generate/Import API Keys:** Next, generate or import the API keys that you will distribute to your application developers (customers) who will be consuming your API[cite: 707].
3.  **Create the Usage Plan:** Define the Usage Plan itself, specifying the desired throttle and quota limits that you want to enforce[cite: 707].
4.  **Associate API Stages and Keys:** Finally, associate the specific API stages (that require API keys) and the generated API keys with this Usage Plan[cite: 707].