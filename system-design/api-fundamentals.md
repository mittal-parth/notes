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

