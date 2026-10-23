# Horizontal v/s Vertical Scaling

Covered in [Core Concepts](./core-concepts.md#scalability)

# Concurrency v/s Parallelism

## Concurrency 

![](./images/concurrency.png)

## Parallelism

![](./images/parallelism.png)

## Comparison 

![](./images/concurrency-parallelism.png)

# Websockets v/s long polling

Covered in [API fundamentals](./api-fundamentals.md#websockets)

# Batch Processing v/s Stream Processing

![](./images/batch-stream-processing.png)

# Stateful vs Stateless

Discussed in [Stateful vs Stateless](./core-concepts.md#stateless-vs-stateful-services)

# Strong v/s Eventual Consistency

- Strong: everyone sees the latest value, always
- Eventual: where updates propagate but temp inconsistencies are ok

Most of it covered in [CAP Theorem](./core-concepts.md#cap-theorem)

## Variations

1. Causal Consistency: ensures operations with a cause-and-effect relationship are seen in the same order by every node while independent operations can be observed in diff orders. 
2. Read-Your-Writes Consistency: After a client performs a write, all subsequent reads by that client reflect the write. Other clients might see stale data. Eg: you see your updated profile pic but others seeing it later.
3. Monotonic Reads consistency: If a client reads a value, any future reads by the same client will return the same or a newer value. 
4. Monotonic Writes Consistency: Writes from the same client are executed in the order they were issued by that client. They will not go out of order. This is about write -> write, diff than read-your-writes.

When to choose what is basically CAP theorem.

# Read-Through vs Write-Through Cache

Discussed in [Caching](./caching.md)

# Push vs Pull Architecture

Discussed in [CDN](./caching.md#important-terms)

# Latency vs Throughput

Discussed in [Core Concepts](./core-concepts.md#latency-vs-throughput-vs-bandwidth)

# REST v/s RPC

## REST

Covered in API fundamentals.

- It is resource based, stateless, supports standard HTTP methods, cache-able.
- Use when public facing APIs, caching, HTTP methods needed, map well into CRUD operations, max flexibility.

## RPC

- Remote Procedure Call: A protocol that allows a program to execute a procedure (subroutine) in another address space as if it were local.
- Action based, function call focused, less overhead.
- Choose when internal microservice, max performance needed, actions dont map well into CRUD.