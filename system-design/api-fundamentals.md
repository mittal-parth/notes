# APIs

An API (Application Programming Interface) is that agreed way of talking. It tells one piece of software _what it can ask for, what data it must send, what it will get back, and what errors can happen_.

## Types:

1. Public: used by external devs / customers
2. Partner: used by selected orgs, not open to everyone
3. Internal: internal to the company, inside 1 org.
4. Library: inside the same running program

## Network APIs

### Request Parts

1. Method: HTTP Verbs
2. Path
3. Query String
4. Headers: Authorization, Accept, Content-Type
5. Body

### Response Parts

1. Response Code
2. Body
3. Headers: Content-Type, Cache-Control, RateLimit-Remaining

## Auth, AuthZ, Limits

- Auth: who.
    - API keys, OAuth, Session cookies, mTLS
- Authz: are you allowed
- Rate limits & Quotas: Rate limits protect the API from overload and abuse. Quotas enforce product or tenant usage limits.

## API Reliability

- Timeouts: every API must have a timeout otherwise callers can keep waiting indefinitely.
- Retries: repeat when safe
- Idempotency

# API Gateway

An API Gateway acts as a central server that sits between clients and internal backend services.

Instead of clients interacting with multiple microservices, they send it to a common API gateway.
Internally, it helps in centralizing these tasks, eliminating the need for each individual service to do so.

## Core features of an API Gateway

Typical flow:

Request reception -> Validation -> Auth and AuthZ -> Rate Limiting -> Req Transformation -> Service discovery + Load Balancing -> Response handling / transformation + Caching -> Logging & Monitoring

1. Auth and AuthZ
2. Rate Limiting
3. Load Balancing
4. Caching
5. Request Transformation: modify structure of incoming req to match backend requirement, similarly with responses on the other side.
6. Service discovery: identifying correct backend service to route the req to.
7. _Circuit Breaking_
8. Logging and Monitoring

# REST vs GraphQL

| | REST | GraphQL |
|---|---|---|
| **Overview** | At its core, REST is built around resources. Each resource (such as a user, order, or product) is uniquely identified by a URL (Uniform Resource Locator), and clients interact with these resources using a fixed set of HTTP methods. | Unlike REST, which organizes APIs around fixed endpoints and HTTP methods, GraphQL is a **query language** that allows clients to request exactly the data they need — nothing more, nothing less. The client decides what to fetch, making it more flexible. |
| **Core functionalities** | <ul><li>Resources identified by URLs</li><li>Clients interact via a fixed set of HTTP methods</li></ul> | <ul><li><strong>Queries</strong>: fetch data</li><li><strong>Mutations</strong>: modify data</li><li><strong>Subscriptions</strong>: real-time updates</li></ul> |
| **Benefits** | <ul><li>Simplicity</li><li>Statelessness</li><li>Cacheability</li><li>Scalability</li><li>Good ecosystem</li></ul> | <ul><li>Solves over- and under-fetching</li><li>Single request for multiple resources, <em>solving the N+1 problem</em></li><li>Strong typing</li><li>No versioning</li><li>Real-time data with subscriptions supported natively</li></ul> |
| **Drawbacks** | <ul><li>Over-fetching: you may need only the profile email but it returns much more data, leading to wastage of bandwidth</li><li>Under-fetching: to get desired data, you might need to make multiple calls</li><li>Versioning is hard to maintain</li></ul> | <ul><li>Complex setup and tooling</li><li>Caching challenges — REST APIs leverage HTTP caching but GraphQL queries use POST requests, making caching trickier</li><li>Increased server load, as it is up to the client now</li><li>Security/performance risks: inefficient queries lead to risk of DDoS</li></ul> |