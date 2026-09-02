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

