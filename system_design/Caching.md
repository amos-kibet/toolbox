## Caching

- Caching is a process of storing data in a temporary storage location called **cache** to improve the speed of data access.
- Cache is a high-speed storage that stores a small proportion of critical data so that future requests for that data can be served faster.

### How caching works

When the user requests some data:

- The system will first check the cache memory to see if the requested data is already there. If data is present (**cache hit**), the system will return the data directly from the cache.
- If the requested data is not present (**cache miss**) in the cache, the system will retrieve the data from the original source (database, external API, etc).
- Now, after fetching the data from the database, the system will store the fetched data in the cache for future use.

![cache-aside strategy illustration](https://cdn-images-1.medium.com/max/1080/1*KNlUFMg72Ziy4QbbPpOwcw.png)

- The above idea is one of the commonly used caching strategies, called **cache-aside strategy**.
- Other caching strategies include:

  - read-through strategy
  - write-around strategy
  - write-back strategy
  - write-through strategy

- **NOTE**: Cache hits and misses are critical metrics for measuring cache performance.
  - A high cache hit rate means the cache is effectively storing and retrieving the data.
    A high cache miss rate implies that the cache is not used effectively,and we should either adjust the cache size, or use a different replacement policy, or some other techinque to minimize cache miss.

### Real-life examples of caching

- **Web browsers** use caching to store frequently accessed HTML, CSS, JavaScript, and images.
- **CDNs** store static files like images and videos and serve them from locations closer to the user.
- **DNS cahing** helps improve the speed of accessing web pages by storing IP the address of a domain name in a cache. Instead of performing a DNS query every time, an IP address can be quickly retrieved from the cache.

### Cache eviction policy

These are algorithms that manage data stored in a cache. When the cache is full, some data needs to be removed in order to make room for new ones. So, cache eviction policy determines which data to remove based on certain criteria. There are several cache eviction policies:

- **Least Frequently Used (LFU)** - Removes least frequently used data.
- **Least Recently Used (LRU)**
- **Most Recently Used (MRU)**
- **Random Replacement (RR)**
- **First In First Out (FIFO)**

### Cache Invalidation

- **Write-through cache** - data is first written in the cache, then the database. Every read done in the cache follows the most recent write. Suitable for read-heavy applications.
- **Write-around cache** - Almost similar to **write-through caching**, in that you write to the database, but in this case you don't write to the cache. Suitable for write-heavy applications.
- **Write-back cache** - old data is first flushed from the cache, then new data is written to the cache alone. Once this is done, the data is marked as modified, and background workers start writing the data into the database asynchronously.

### Referrence

1. enjoy algorithms https://www.enjoyalgorithms.com/blog/caching-system-design-concept
