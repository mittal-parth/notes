# Caching

- A cache is a fast storage layer that keeps copies of frequently used data.
- We know how its used and what is cache hit or miss, not covering
- Why use? Reduced latency, reduced load and higher throughput 

## Caching Layers

1. Browser Cache
    - Cache images, stylesheets, JS, even API requests.
    - Closest to the user.
    - Controlled via HTTP headers like `Cache-control` and `ETag` (tracks which version is stored currently. Subsequent API call can send this and compare with server to not receive the entire data again)
2. CDN Cache
    - Stored on the edge network, closer to the user
    - Mainly for static content, media files and public pages
3. Application Cache
    - Direct in-memory in the application server process. Fast but each instance has its own-cache.
4. Distributed Cache (Redis / Memcached)
    - Separate, used by multiple servers, reading the same cache copy.
5. DB with an internal cache
    - Even DBs maintain internal cache for frequently accessed query data.

## What not to cache

- Huge, rarely accessed data 
- Data that changes constantly 
- Data that must be fresh

## Designing Cache Keys

- Identifier for the cached value. Caches are essentially key-value pairs. 
- Too broad: Eg: `cart` covers everyone's cart. Diff requests can accidentally return same data
- Too specific: `product:123:<timestamp>` - defeats the purpose of cache
- Good patterns: 
    - Include namespaces: product, user, session
    - Versioning so that entire old versions can be invalidated

## TTL

- Cache value should not live forever
- TTL (Time to Live) defines the time before which it expires

## When to add

- High read frequency
- Expensive reads or computation
- Repeated access to the same data




