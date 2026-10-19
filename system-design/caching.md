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

# Caching Strategies 

1. Cache-Aside

- Application manages the cache explicitly
- Usual cache hit and miss: application checks cache, if present (cache hit), return. If not present (cache miss), application checks DB, stores it in cache and returns.
- Also called lazy loading because cache is only populated when someone requests it
- During write, update DB and delete the cache entry. One could update the cache value also but that can being sync issues so simple delete. Next cache miss will re-populate.
- Pros: 
    - Only requested data gets cached
    - Cache is optional (read from DB will still succeed)
    - Works with any key-value store
- Cons: 
    - Stale data is possible if cache invalidation is delayed
    - First request is slower
    - Caching logic handled by the application
- Best for read heavy systems

2. Read-Through

- If value already cached, return. If not, the cache layer itself fetches it from the DB, stores it and returns.
- Application does not implement the cache-miss logic, simply asks for data from cache.
- Pros: 
    - Cleaner application code
    - One shared loading path for many services
    - Only requested data gets cached

- Cons: 
    - Requires a caching system that knows how to read from the underlying DB
    - First request is slower

3. Write-Through

- Every write goes through the cache and the cache then updates the DB. 
- Write is considered successful only if the DB has also been updated.
- If another request reads the same data afterward, it can be served from the cache directly.
- All writes must use the same cache path however.
- Pros: 
    - good for systems where read follows a write
- Cons: 
    - can fill up cache with data that is never accessed
    - slower writes
    
Best for workloads where reads commonly follow writes and slower writes are acceptable

4. Write-Behind

- Similar to Write-Through but here we don't wait for the DB to acknowledge the update during write
- The database update happens asynchronously later.

Pros: 
    - Faster writes
    - Improved performance as the cache no longer waits for DB update

Cons: 
    - Durability: The cache server fails after accepting the update but before updating the DB. If this was in volatile memory, then it will be lost.
    - the background writer has to handle ordering, retries and duplicate writes.
    - consistency delay between cache and DB

Best where high write throughput is needed and some delay between cache and DB is acceptable

5. Write-Around

- Basically the write strategy when you'd think of cache-aside.
- Write data directly to DB, delete existing cache entry. Read with a cache miss populates the cache. Cache hit reads directly from cache.

Pros: 
    - Only requested data gets cached
Cons: 
    - First read slow
    - not good for read-after-write workloads

## Cache Eviction Strategies

1. Least Recently Used (LRU)

- Evicts the item that hasn't been used for the longest time.
- Access Tracking: keep track of when the item was last accessed. Use doubly linked list or hash map and queue.
- Cache Hit: Move the accessed item to the most recently used position, like the front of a list.
- Cache Miss: If not full, add. If full, remove the least recently used item.
- Pros
    - Simple, intuitive.
    - Efficient
    - Optimized for real world usage.
Cons:
    - Does not adapt.
    - Metadata overhead of tracking (storage)
    - Performance cost

2. Least Frequently Used (LFU)

- Evicts the item that has been least accessed. Tracks frequency instead of recency.
- Access tracking: keep track of how many times an item was accessed.
- Cache Hit: Update the frequency counter, return item.
- Cache Miss: If cache not full, add. If full, evict the item with least frequency counter and add new item. Use another strategy like LRU / FIFO to resolve conflicts.
- Pros:
    - Efficient for predictable patterns.
    - Highly effective for popular data.

- Cons:
    - Not adaptive.
    - High overhead to track
    - Slower updates

3. First in First Out (FIFO)

- The item that was inserted first is evicted first.
- Access tracking: whenever an item is added, its placed at the end of the queue.
- Cache Hit: Return data, no changes
- Cache Miss: If not full, return. If full, remove the item placed at the front of the queue (item that sat for the longest time) and add new at the end.
- Pros:
    - Simple
    - No memory overhead

- Cons:
    - Ignores access patterns
    - May waste cache space
    - Rarely used in real scenario because a hot item might get simply removed

4. Random Replacement (RR)

- Randomly remove an item
- Cache Hit: return the item, no changes
- Cache Miss: If not full, add. If full, evict a random item to make space for the new one.
- Pros: 
    - Simple
    - No computation overhead
    - Fair for unpredictable access patterns
- Cons
    - Inefficient for stable access patterns
    - Useful items might get evicted - risk of poor cache hit rates

5. Most Recently Used (MRU)

- exact opposite of LRU
- idea is that something accessed recently will of temporary need
- Cache Hit: Mark the item as most recently used and return
- Cache Miss: If not full, return. If full, evict the most recently used item.
- Pros: 
    - Effective in specific scenarios
    - Simple
- Cons:
    - Suboptimal for most use cases
    - Poor cache hit rate
    - Rarely used

6. Time To Live (TTL) 

- Assign a fixed lifespan to every item.
- Item insertion: Attach a TTL to every item added.
- Cache Hit / Miss: 
    - Check the expiration time when an item is accessed. If expired, evict and report as cache miss.
    - If not expired, return as cache hit.
- Pros:
    - Ensure freshness
    - Simple to configure
- Cons:
   - Items that haven’t expired but are still irrelevant occupy cache space.
   - Premature eviction
   - Not adaptive

7. Two-tiered system

- Use local cache + distributed cache.
- First check ultra fast local cache - in memory HashMap / LRUCache
- If not present, check globally distributed cache like Redis.
- If still not present, check DB.
- Pros:
    - Ultra fast access if in memory
    - Scalable storage
    - Fault tolerant
- Cons:
    - Complexity, sync issues
    - Network Overhead
    - Stale data with inconsistent updates