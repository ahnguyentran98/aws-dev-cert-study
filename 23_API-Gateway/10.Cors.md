### AWS API Gateway - CORS

**CORS (Cross-Origin Resource Sharing)** is a web browser-based mechanism that allows a web page running in one domain (origin) to request resources from another domain (cross-origin)[cite: 1603, 277]. Without CORS, web browsers enforce a "same-origin policy," which prevents a web page from making requests to a different domain for security reasons. When a web application in your browser makes a request to an API in a different domain, the browser will block the request unless the server (in this case, API Gateway) explicitly allows it using CORS headers[cite: 1603].

An "origin" is defined by its scheme (protocol), host (domain), and port[cite: 1603]. For example, `https://www.example.com` (with an implied port of 443) is a different origin from `http://www.example.com` or `https://other.example.com`[cite: 1603].

**How CORS Works (Preflight Request):**

When a client (typically a web browser) makes a "complex" cross-origin request (e.g., `PUT`, `DELETE`, or a `POST` with certain `Content-Type` headers), the browser first sends a "preflight request" using the `OPTIONS` HTTP method[cite: 1605]. This preflight request asks the server if the actual request is allowed. The server (API Gateway) then responds with specific CORS headers.

**Key CORS Headers:**

If the server allows the request, its response to the preflight `OPTIONS` request will include headers like:
* `Access-Control-Allow-Origin`: Specifies which origins are allowed to access the resource (e.g., `https://www.example.com` or `*` for all origins)[cite: 1606, 279].
* `Access-Control-Allow-Methods`: Lists the HTTP methods allowed for the resource (e.g., `GET`, `PUT`, `DELETE`)[cite: 1605, 279].
* `Access-Control-Allow-Headers`: Indicates which HTTP headers are allowed in the actual request.

**CORS in Amazon API Gateway:**

![](/resource/image_27.png)

If a client makes a cross-origin request to an API deployed on API Gateway, you need to enable the correct CORS headers on API Gateway itself[cite: 279]. API Gateway makes it straightforward to configure CORS directly from the console[cite: 712].

**Common Use Case (Static Website Hosting):**

A very common scenario requiring CORS is when you host a static website on Amazon S3, and that website (served from an S3 domain) needs to call an API hosted on API Gateway (a different domain)[cite: 1606].

For example:
1.  A website is hosted at `http://my-bucket-html.s3-website.us-west-2.amazonaws.com`[cite: 1606].
2.  This website tries to fetch images from an S3 bucket or make API calls through an API Gateway endpoint, which would be on a different domain, e.g., `http://my-bucket-assets.s3-website.us-west-2.amazonaws.com` for assets or `https://api.my-app.com` for the API[cite: 1606].
3.  Without proper CORS configuration on the `my-bucket-assets` bucket or the API Gateway, the web browser would block these requests due to the same-origin policy[cite: 1603].
4.  By enabling CORS on API Gateway and specifying the website's origin (e.g., `http://my-bucket-html.s3-website.us-west-2.amazonaws.com`) in the `Access-Control-Allow-Origin` header, the browser will permit the cross-origin requests[cite: 1606].

**Configuration:**

You can enable CORS through the API Gateway console[cite: 712]. When enabling CORS, you specify the allowed origins, methods, and headers.