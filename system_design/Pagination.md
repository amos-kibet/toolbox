## API Pagination

- API pagination refers to a technique used in API design and development to retrieve large data sets in a structured and manageable manner.
- Pagination typically involves the use of parameters, such as `offset` and `limit`, or `cursor`-based tokens, to control the size and position of the data subset to be retrieved.
- These parameters determine the starting point and the number of records to include on each page.
- Generally, pagination is done by specifying to the user how many results to display per page, and which page of results they are currently viewing.

### Common API Pagination Techniques

**Offset and limit pagination**

- This technique involves using two parameters: `offset` and `limit`.
- `offset` determines the starting point in the dataset, while `limit` specifies the maximum number of records to include on each page.
- Using `limit` `offset` doesn’t scale well for large datasets. As the offset increases the farther you go within the dataset, the database still has to read up to offset + count rows from disk, before discarding the offset and only returning count rows.
- Also, if items are being written to the dataset at a high frequency, the page window becomes unreliable, potentially skipping or returning duplicate results.
- Example (returns the first 10 records): `GET /api/posts?offset=0&limit=10`

**Cursor-based pagination**

- Instead of relying on numeric offsets, cursor-based pagination uses a unique identifier or token to mark the position in the dataset.
- The cursor can be based on various criteria, such as timestamp, a primary key, or an encoded representation of the record.
- Example (returns the next page of records after the last fetched record, marked by the cursor): `GET /api/posts?cursor=eyJZHsg8D`

**Page-based pagination**

- It involves using a **_page_** parameter to specify the desired page number.
- The API consumer requests a specific page of data, and the API responds with the corresponding page, typically along with metadata such as the total number of pages or total record count.
- This technique simplifies navigation and is often combined with other parameters like `limit` to determine the number of records per page.
- Example (returns the second page, where each page contains 20 posts): `GET /api/posts?page=2&limit=20`

**Time-based pagination**

- It involves using time-related parameters, such as `start_time` and `end_time` to specify a time range for retrieving data.
- Example: `GET /api/events?start_time=2023-01-01T00:00:00Z&end_time=2023-01-31T23:59:59Z`

**Keyset pagination**

- Relies on sorting and using a unique attribute or key in the dataset to determine the starting point for retrieving the next page.
- Example: `GET /api/products?last_key=XYZ123`

### API Pagination Best Practices

- Use common naming conventions for pagination parameters, like `offset` and `limit`, or `page` and `size`.
- Always include pagination metadata in API responses. Example:

```JSON
{
    "data": [],
    "pagination": {
        "total_records": 100,
        "total_pages": 10,
        "current_page": 1,
        "next_page": 2,
        "prev_page": null
    }
}
```

- Determine an appropriate page size:
  - Understand the data characteristics - size of records (small or large), structure (nested structures, etc).
  - Consider network latency and bandwidth of your API consumers.
  - Consider user experience and usability of your APIs.
  - Evaluate perfomance impact of your pagination values.
  - Provide flexibility with pagination parameters - users determine page sizes to fetch data.
  - Provide ways to gather user feedback from your API consumers, regarding the page size.
- Implement sorting and filtering options:
  - Common parameters to use include:
    - `page` - 1, 2, etc.
    - `per_page` - 10, 15, etc.
    - `sort_by` - "id", "updated_at", etc.
    - `sort_order` - "asc", or "desc".
    - `category` - "electronics", "consumer goods", etc.
    - `min_price` - 0, 10, etc.
    - `max_price` - infinity, 10,000, etc.
  - Example (returns the first page of products sorted by price in descending order): `GET /api/products?page=1&per_page=10&sort_by=price&sort_order=desc`
- Preserve pagination stability.
  - Use a stable sorting mechanism - This means that when multiple records have the same value for the sorting field, their relative order should not change between requests.
  - Avoid changing data order - Avoid making any changes to the order or positioning of records during pagination, unless explicitly requested by the API consumer. If new records are added or existing records are modified, they should not disrupt the pagination order or cause existing records to shift unexpectedly.
  - Use unique and immutable identifiers.
  - Handle record deletions gracefully.
  - Use deterministic pagination techniques - Techniques like cursor-based pagination or keyset pagination, where the pagination is based on specific attributes like timestamps or unique identifiers, provide stability and consistency between requests.
- Handle edge cases and error conditions
  - Account for end of dataset pages.
  - Account for out-of-range page requests.
  - Validate the pagination paramaters provided by the API consumer.
  - Handle empty result sets.
  - Handle server errors.
  - Implement rate limiting and throttling.
  - Handle errors using a consistent approach, use consistent HTTP status codes and error response formats to ensure uniformity and ease of understanding for API consumers.
- Consider caching strategies:
  - page-level caching - Cache the entire paginated response for each page. This means caching the data along with the pagination metadata. This strategy is suitable when the data is relatively static and doesn't change frequently.
  - result set caching - Cache the result set of a specific query or combination of query parameters. This is useful when the same query parameters are frequently used, and the result set remains relatively stable for a certain period. Cache the result set and serve it directly for subsequent requests with the same parameters.
  - time-based caching - Set an expiration time for the cache based on the expected freshness of the data. For example, cache the paginated response for a certain duration, such as 5 minutes or 1 hour. Subsequent requests within the cache duration can be served directly from the cache without hitting the server.
  - conditional caching - Use conditional caching mechanisms like HTTP ` ETag`` or  `Last-Modified`` headers. The server can respond with a `304 Not Modified`` status if the client's cached version is still valid. This reduces bandwidth consumption and improves response time when the data has not changed.
  - reverse-proxy caching - Implement a reverse proxy server like Nginx or Varnish in front of your API server to handle caching. Reverse proxies can cache the API responses and serve them directly without forwarding the request to the backend API server. This offloads the caching responsibility from the application server and improves performance.
