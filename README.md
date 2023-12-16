

User Management Service:
========================
As the name suggests, the User Management Service is a crucial component of our user management system. This backend service, implemented in Python, is responsible for handling both read and write workflows related to user management.

Challenges:
===========
When I assumed ownership of this system, it posed a significant bottleneck in our entire backend. Three main challenges emerged:

1. High Throughput: The system needed to support high-throughput Input/Output Operations Per Second (IOPS) for both read and write queries, with a Query Per Second (QPS) of around 10k.

2. Exactly Once Processing of Events: Ensuring the exactly-once processing of events, including re-processing dropped messages and preventing out-of-order processing.

3. Backward Compatibility of API and Contracts: The system required a revamp to maintain backward compatibility and avoid any breakages in existing API contracts.


Approach:
=========
1. Understand the full scope of the problem, addressing the three main challenges.
2. Divide each challenge into sub-problems to ensure mutually exclusive solutions for each.
3. Make trade-offs based on the constraints of each sub-problem.
4. Resolve blockers by consulting the source code (for coroutines and async libraries), searching, or seeking assistance on Stack Overflow.


Solutions:
==========

1. High Throughput:
To address 
