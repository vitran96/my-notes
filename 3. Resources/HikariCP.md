---
para: resource
tags: [java, database]
---
JDBC connection pool; Spring Boot's default

Recommended config:
```properties
# Basic Connection Info
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password

# --- Recommended HikariCP Tuning ---

# 1. Maximum Pool Size: The formula (cores * 2) + 1 is a great starting point.
# Don't set this to 100+ unless you have a high-end DB cluster.
spring.datasource.hikari.maximum-pool-size=10

# 2. Minimum Idle: Matches maximum-pool-size for a "fixed-size" pool (recommended for performance).
spring.datasource.hikari.minimum-idle=10

# 3. Connection Timeout: How long to wait for a connection from the pool (5-10 seconds is safer than 30s).
spring.datasource.hikari.connection-timeout=5000

# 4. Idle Timeout: How long a connection can sit idle before being retired (default is 10 min).
spring.datasource.hikari.idle-timeout=300000

# 5. Max Lifetime: Total life of a connection. Must be shorter than any DB/Firewall timeout.
spring.datasource.hikari.max-lifetime=1800000

# 6. Leak Detection: Log a warning if a thread holds a connection longer than this (great for debugging).
spring.datasource.hikari.leak-detection-threshold=2000
```

Why these specific values?
- Fixed Pool Size (minimum-idle == maximum-pool-size): By keeping the pool size fixed, you avoid the latency "spike" of creating a new physical [[TCP]] connection to the [[Relational database|database]] when traffic suddenly bursts.
- Max Lifetime: It is critical to set this around 2-5 minutes shorter than your [[Relational database|database]] 's global wait timeout (e.g., [[MySQL]]'s wait_timeout) to prevent "Broken Pipe" or "Connection Reset" errors.
- Connection Timeout: 30 seconds (the default) is an eternity for a web user. It is better to fail fast (5 seconds) so your application can handle the error or fallback.

## What happen if connection suddenly spike?

- **Connection Requests are Queued:** Initially, the connection pool tries to satisfy requests using existing idle connections. As the spike continues, all available connections become "active". Subsequent requests are then put into a queue to wait for a connection to be returned to the pool.
- **Threads Block:** The application threads handling the user requests are blocked while waiting for a connection from the pool. This leads to increased latency for those requests.
- **Connection Timeout Exceptions:** If a request remains in the queue for longer than the configured `connectionTimeout` (default is typically around 30 seconds for [[HikariCP]]), it will fail and throw a timeout exception. The user receives an error, and the application's overall error rate increases.
- **Application Unresponsiveness (Cascading Failure):** As more and more threads block waiting for connections, the application's general thread pool (e.g., [[Tomcat]]'s thread pool) can become exhausted. This prevents the application from processing new incoming requests (even those not requiring a DB connection immediately), leading to a complete application freeze.
- **Database Overload:** Even if the application pool size is large, the database itself has a maximum number of connections it can handle. An overwhelming number of simultaneous queries from the application can overload the database server, leading to slow queries, lock waits, and potential database instability, exacerbating the problem.
- **"Connection pool exhausted" Errors:** Eventually, the system will start logging "Connection pool exhausted" or "too many clients" errors, indicating a critical failure in managing database resources.
