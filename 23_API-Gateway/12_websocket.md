### API Gateway – WebSocket API

The WebSocket Protocol offers a persistent, two-way interactive communication channel between a user's browser (or other client) and a server. Unlike traditional HTTP requests (which are short-lived and client-initiated), WebSockets allow the server to push information to the client in real-time, enabling more dynamic and interactive applications[cite: 720].

**What is WebSocket?**
* **Two-way communication:** Allows both the client and the server to send messages to each other at any time after the initial connection is established[cite: 720].
* **Persistent connection:** Once opened, the connection remains open until explicitly closed by either the client or the server[cite: 720].
* **Stateful applications:** Enables use cases where the server needs to maintain context or state about the client connection[cite: 720].

**Common Use Cases for WebSocket APIs:**
* Chat applications [cite: 720]
* Collaboration platforms (e.g., shared whiteboards, document editing) [cite: 720]
* Multiplayer games [cite: 720]
* Financial trading platforms (for real-time stock updates) [cite: 720]
* Real-time dashboards and data feeds [cite: 720]

**How API Gateway's WebSocket API Works:**

API Gateway provides a managed service for building WebSocket APIs, integrating seamlessly with AWS services like Lambda and DynamoDB, or HTTP endpoints[cite: 720].

1.  **Connecting to the API:**
    * Clients connect to the WebSocket API using a unique WebSocket URL, which looks like `wss://[some-uniqueid].execute-api.[region].amazonaws.com/[stage-name]`[cite: 721].
    * When a client connects, API Gateway can invoke a designated backend (e.g., a Lambda function configured for the `$connect` route) to handle the connection event[cite: 721, 725].
    * API Gateway assigns a unique `connectionId` to each client connection, which can be stored (e.g., in DynamoDB) for later use to send messages back to specific clients[cite: 721, 722].

    ![](/resource/image_31.png)

2.  **Client-to-Server Messaging:**
    * Once connected, clients send JSON messages to the WebSocket API[cite: 725].
    * API Gateway uses a **route selection expression** (e.g., `$request.body.action`) to determine how to route the incoming JSON message based on a field in the message body[cite: 725].
    * The result of the expression is evaluated against defined **route keys** (e.g., `join`, `quit`, `sendMessage`) in your API Gateway configuration[cite: 725].
    * Each route key is integrated with a specific backend (e.g., a Lambda function configured for the `sendMessage` route) to process the message[cite: 722, 725].
![](/resource/image_32.png)

3.  **Server-to-Client Messaging:**
    * To send messages from your backend (e.g., a Lambda function) to a connected client, you use the API Gateway's **`@connections` endpoint**[cite: 723, 724].
    * You make an HTTP `POST` request (signed with IAM SigV4) to a specific `Connection URL` (`wss://abcdef.execute-api.us-west-1.amazonaws.com/dev/@connections/connectionId`), using the client's `connectionId`[cite: 723, 724].
    * API Gateway then pushes the message to that specific connected client[cite: 723].
    * Other operations on the `Connection URL` include `GET` to get connection status and `DELETE` to disconnect a client[cite: 724].

![](/resource/image_33.png)

**Routing in WebSocket APIs:**
* Incoming JSON messages are routed to different backends based on defined routes[cite: 725].
* If no specific route matches, messages are sent to the `$default` route[cite: 725].
* Special built-in routes are:
    * `$connect`: Handles new client connections[cite: 725].
    * `$disconnect`: Handles client disconnections[cite: 725].
    * `$default`: Handles messages when no other route key is matched[cite: 725].

![](/resource/image_34.png)

**Example Diagram (Chat Application):**
In a chat application[cite: 720, 721, 722, 723, 725]:
1.  A user connects to the WebSocket URL, triggering a Lambda function (`onConnect`). This function stores the `connectionId` (e.g., in DynamoDB)[cite: 721].
2.  When a user sends a chat message, it goes to the API Gateway. Based on a route selection expression (e.g., `action: "sendMessage"`), a Lambda function (`sendMessage`) is invoked[cite: 722, 725].
3.  The `sendMessage` Lambda processes the message and then uses the `@connections` endpoint to send the message to all relevant connected `connectionIds` (other users in the chat room)[cite: 723].
4.  When a user disconnects, a Lambda function (`onDisconnect`) is triggered to clean up the `connectionId` from storage[cite: 720].