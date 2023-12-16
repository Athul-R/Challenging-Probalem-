
**User Management Service:**
As the name suggests, the User Management Service is a crucial component of our user management system. This backend service, implemented in Python, is responsible for handling both read and write workflows related to user management.

**Challenges:**
When I assumed ownership of this system, it posed a significant bottleneck in our entire backend. Three main challenges emerged:

1. **High Throughput:** The system needed to support high-throughput Input/Output Operations Per Second (IOPS) for both read and write queries, with a Query Per Second (QPS) of around 10k.

2. **Exactly Once Processing of Events:** Ensuring the exactly-once processing of events, including re-processing dropped messages and preventing out-of-order processing.

3. **Backward Compatibility of API and Contracts:** The system required a revamp to maintain backward compatibility and avoid any breakages in existing API contracts.

**Approach:**
1. Understand the full scope of the problem, addressing the three main challenges.
2. Divide each challenge into sub-problems to ensure mutually exclusive solutions for each.
3. Make trade-offs based on the constraints of each sub-problem.
4. Resolve blockers by consulting the source code (for coroutines and async libraries), searching, or seeking assistance on Stack Overflow.

**Solutions:**

1. **High Throughput:**
   To address high throughput concerns, especially for read and write queries, concurrency was essential. While Python is single-threaded, coroutines were employed to effectively implement concurrency. Threads waiting for I/O were moved away from the CPU, regaining CPU time only upon receiving the I/O response.

2. **Extensibility of Code:**
   Considering multiple tables with different schemas, the SQLAlchemy Object-Relational Mapping (ORM) system was utilized for database reads, extending it to multiple tables. A cache package (that extended the Aioredis Python package) facilitated cache reads and evictions, while decorators standardized API responses and exception handling.

3. **Systems:**
   Queries were categorized into read and write, further dividing reads into frequent and non-frequent queries. Frequent queries were directed to a distributed cache, while non-frequent queries were served from the read replica of the database.

4. **Exactly Once Processing:**
   Duplicate and out-of-order queries were addressed by reading the last updated timestamp in the row metadata before updating the database. On query execution failure, the database commit was reverted, and an error was thrown or deferred processing via an AWS SNS dead letter queue was implemented. For handling multiple threads competing to update the database row, Redis Locks (aioredis) were employed. Database locks were challenging to debug during deadlocks, which is why I opted for distributed locks.

5. **Backward Compatibility of APIs:**
   To ensure backward compatibility, the current contract was maintained while exposing new ones using versioning. API callbacks for the current contracts were optimized without altering the existing API contracts.

**Others:**
The service was designed to be stateless. It was implemented using Python’s asynchronous ReST API framework, Sanic, with 12 replicas of the service running in a Kubernetes cluster to achieve high concurrency.
