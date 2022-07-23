# API Design Considerations

## Summary
When deciding an architecture style to build your APIs, start with the question `who will be the client (consumer) of your APIs?`. Picking the "right" architecture or design pattern is about moving forward with the tradeoffs that works best for your use case and your organization. There is no silver-bullet or perfect approach. 

Sections below provide food for thought.

## Determine API Architecture Style

<details>
<summary>click to expand</summary>

For each architecture style shown in the table below, determine which tradeoffs works best for your use-cases.

![api-architecture-style-comparison](./diagrams/api-architecture-style-comparison.png)

> diagram credit: https://youtu.be/IvsANO0qZEg?t=2025

</details>

## Style: Remote Procedure Call (RPC) 

<details>
<summary>click to expand</summary>

1. RPC helps build list of functions - Command or Action oriented APIs - which are callable remotely.
1. This style works well for implementing pre-determined use-cases.
1. RPC follows contract-first approach: functions and messages are strongly typed which are shared among client/server.

### **Net Gains**

1. RPC APIs are simple and easy to understand.
1. Strongly typed messages types leads to lightweight payloads exchange.
1. High performance over network, helps achieve low latency for service-2-service communication.

### **Mindfulness**

1. Lack of abstraction leads to tight coupling between client/server: new use case will likely require new function implementation.
1. You could end up with "function explosion": create new set of functions to accomplish slightly different actions.
1. Lack of RPC API introspection could lead to poor discoverability: clients must know specific endpoint to call and downstream side-effects aren't always clear.

</details>

## Style: Representational State Transfer (REST)

<details>
<summary>click to expand</summary>

1. REST architecture style helps build resource management oriented APIs.
1. Unlike RPC, REST models resources (e.g. Person, Account), resource actions (e.g. CRUD operations), relationship (i.e. links) between these resources.
1. APIs that follow REST style are considered RESTFul.

### **Net Gains**

1. REST style leads to decoupling client/server which helps both evolve independently.
1. Clients are likely to fulfil their new use cases with existing RESTFul APIs.
1. RESTFul APIs are self-describing: provides metadata to help clients understand how to interact with the RESTFul APIs.

### **Mindfulness**

1. RESTFul APIs can be chatty: clients may need to fetch data from multiple APIs for their unique use cases. 
1. Payloads are bigger than RPC style message; payloads are loaded with helpful metadata.
1. Clients may end up getting more info than they need.

</details>

## Example - RPC and REST

<details>
<summary>click to expand</summary>

### **Base Use Case**

Assume you're building a chat system. Up-front, you have well defined use-cases:

1. Client can acquire single/collection of conversations.
1. For given conversation, client can list its messages.
1. For given conversation, client can send (reply) new messages.

Then later on, you get an additional business use-case

1. Client wants the ability to fetch message's author information.

#### **RPC Style Implementation**

Support the pre-determined use-cases

```csharp
// Service contract exposed via HTTP APIs
listConversations();
listMessages(int converationId);
sendMessages(int converationId, string body);

// RPC API interaction
GET /listConversations
GET /listMessages?id=123
POST /sendMessages?id=123 { "body": "my new message" }    

```

Accommodate a new use-case.

```csharp
// Service contract exposed via HTTP APIs
getAuthorInfo(int messageId)

// RPC API interaction
GET /getAuthorInfo(int messageId)
```

#### **REST Style Implementation**

Support the pre-determined use-cases

```csharp
// Service contract exposed via HTTP APIs
listConversations();
listMessages(int converationId);
sendMessages(int converationId, string body);

// RPC API interaction
GET /listConversations
GET /listMessages?id=123
POST /sendMessages?id=123 { "body": "my new message" }    

```

Accommodate a new use-case.

```csharp
// Service contract exposed via HTTP APIs
getAuthorInfo(int messageId)

// RPC API interaction
GET /getAuthorInfo(int messageId)
```

</details>