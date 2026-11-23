---
para: resource
tags:
  - interview
  - backend
  - java
  - sql
verified: 2026-11
---
Question bank for backend (Java / Spring / SQL) interviews. Every answer was re-derived and checked, not copied from old notes. Source for Anki decks `Role::Backend::*` (tag `src::Backend_interview_questions`).

# Java core

## Pass by value?
```java
void change(Person p) { p.name = "B"; p = new Person("C"); }
Person a = new Person("A"); change(a);
```
What is `a.name` afterwards, and is Java pass-by-reference?
- **A:** `"B"`. Java always passes by value; for objects the value is a copy of the reference. Mutating through the copy is visible to the caller; reassigning the parameter only changes the local copy.
- **Pitfall:** "objects are passed by reference" is wrong. A primitive argument is never changed by the callee.

## int vs Integer memory
Where do `int x`, `Integer y` and an `int` field of an object live?
- **A:** A local `int` is stored in the stack frame. A local `Integer` is a reference in the stack pointing to an object on the heap. An `int` field lives inside its object on the heap.
- **Pitfall:** "primitives are always on the stack" is wrong. `Integer` can be `null`.

## Unboxing NPE
```java
Integer count = map.get("missing");
int n = count;
```
What happens, and why?
- **A:** `NullPointerException`. `map.get` returns `null`; assigning to `int` auto-unboxes via `count.intValue()` on `null`.

## equals / hashCode contract
Two `Person(name, age)` objects that look equal both stay in a `HashSet<Person>`. Why, and how do you fix it?
- **A:** Without overriding, `equals` is identity and `hashCode` is identity-based. Override both on the same fields. Equal objects must have equal hash codes; unequal ones may collide.
- **Pitfall:** mutating a field used by `hashCode` after inserting into a hash set makes the object unfindable. Records generate both automatically.

## Abstract class vs interface
When do you pick an abstract class over an interface, and when must an implementer NOT override a method?
- **A:** Abstract class: shared state, constructors, protected members, one parent only. Interface: a capability contract, many per class. A concrete class must implement all abstract methods; it need not override `default` methods; an abstract subclass may leave methods abstract.
- **Pitfall:** since Java 8 interfaces can have `default` and `static` methods, but still no instance state.

## Overloading vs overriding
```java
class A { void f(Object o){ print("obj"); } void f(String s){ print("str"); } }
Object o = "x"; new A().f(o);
```
What prints and why? What is the difference from overriding?
- **A:** `obj`. Overload resolution happens at compile time on the static type. Overriding is resolved at runtime on the object's real type.
- **Pitfall:** there is an `@Override` annotation but no "@Overloading".

## final
```java
final List<String> l = new ArrayList<>();
l.add("a");      // ?
l = new ArrayList<>(); // ?
```
- **A:** First compiles, second does not. `final` on a variable fixes the reference, not the object's state. `final` method: cannot be overridden. `final` class: cannot be extended.

## static
When should something be `static`, and what are the risks?
- **A:** It belongs to the class, not an instance: constants, stateless utilities, factories. A static method cannot touch instance members and is hidden, not overridden.
- **Pitfall:** mutable static state is shared across threads and tests.

## Checked vs unchecked
`IOException` vs `IllegalArgumentException`: what must the caller do, and what is the real "checked or unchecked" answer for `null`?
- **A:** Checked (subclass of `Exception`, not `RuntimeException`) must be caught or declared; unchecked (`RuntimeException`, `Error`) need not. `null` is a value; the exception it causes, `NullPointerException`, is unchecked.
- **Pitfall:** `throw` raises an exception instance; `throws` declares it in the signature.

## HashMap vs TreeMap
You need keys in sorted order and "all keys between A and F". Which Map and what cost?
- **A:** `TreeMap` (red-black tree): O(log n) get/put, sorted iteration, `subMap`/`headMap`/`tailMap`. `HashMap`: O(1) average, unspecified order.
- **Pitfall:** `TreeMap` throws NPE on a `null` key with natural ordering; `HashMap` allows one `null` key. Keys need `Comparable` or a `Comparator`.

## HashMap vs ConcurrentHashMap
Why is `HashMap` unsafe across threads, and why is `if (!m.containsKey(k)) m.put(k, v)` still racy on a `ConcurrentHashMap`?
- **A:** `HashMap` has no synchronization (lost updates, corrupted structure). `ConcurrentHashMap` is thread-safe per operation (CAS plus per-bin locking), but check-then-act is two operations. Use `putIfAbsent` / `computeIfAbsent` / `merge`.
- **Pitfall:** `ConcurrentHashMap` forbids `null` keys and values.

## Set vs Map
Are `HashSet` and `HashMap` from the same interface? How are they related, and `HashSet` vs `TreeSet`?
- **A:** No. `Set` extends `Collection`; `Map` does not. A `HashSet` is backed by a `HashMap` (elements are keys, dummy value). `TreeSet` is backed by `TreeMap`: sorted, O(log n), versus O(1) average.

## ArrayList vs LinkedList
Why is get-by-index fast on an `ArrayList` and slow on a `LinkedList`, and which is usually faster in practice?
- **A:** `ArrayList` is a contiguous array: address = base + index x size, O(1). `LinkedList` must walk nodes: O(n). Append to `ArrayList` is amortized O(1); insert in the middle shifts elements, O(n). `LinkedList` inserts in O(1) only at a node you already hold (or the ends).
- **Pitfall:** `ArrayList` usually wins in practice thanks to CPU cache locality.

## transient and Serializable
After serialize then deserialize, what is a `transient String password`?
- **A:** `null` (default value). `transient` excludes a field from default serialization. `Serializable` is a marker; declare `serialVersionUID` to control version compatibility.

## Getting a result from a thread
`Runnable.run()` returns `void`. How do you get a result back?
- **A:** Submit a `Callable<V>` to an `ExecutorService`; `Future<V>.get()` blocks for the result. Or use `CompletableFuture.supplyAsync`.

## Shallow vs deep copy
```java
int[][] a = {{1},{2}};
int[][] b = a.clone();
b[0][0] = 9;
```
What is `a[0][0]`?
- **A:** `9`. `clone()` is shallow: the outer array is copied but inner arrays are shared. A deep copy duplicates the whole object graph.

## JDK, JRE, JVM
A server only needs to run a built JAR. What is the minimum, and how do the three relate?
- **A:** JVM executes bytecode. JRE = JVM + standard libraries. JDK = JRE + tools (`javac`, `jar`, `jlink`). Running needs only a runtime; compiling needs the JDK.
- **Pitfall:** modern distributions often ship only a JDK, or a trimmed runtime built with `jlink`.

## Stack vs heap errors
Which error, and which memory area, for (a) infinite recursion and (b) an ever-growing list?
- **A:** (a) `StackOverflowError`: per-thread stack, frames with locals. (b) `OutOfMemoryError: Java heap space`: shared heap, objects, garbage collected.

# Concurrency

## Thread pool saturation
A pool is fully busy and request number 1001 arrives. What happens?
- **A:** `ThreadPoolExecutor` order: fewer than core threads, start a thread; otherwise queue it; if the queue is full and below max, start another thread; otherwise reject (default `AbortPolicy` throws `RejectedExecutionException`). `Executors.newFixedThreadPool` uses an unbounded queue, so the request just waits.
- **Pitfall:** an unbounded queue hides overload until memory runs out. Prefer a bounded queue plus `CallerRunsPolicy` (backpressure).

## Deadlock
What four conditions cause a deadlock and how do you break one?
- **A:** Mutual exclusion, hold-and-wait, no preemption, circular wait. Break one: always acquire locks in a fixed order (no circular wait), use `tryLock` with a timeout, or hold fewer locks.
- **Pitfall:** databases detect deadlocks and abort one transaction; the app must retry. Keep transactions short and touch rows in a consistent order.

## Atomic vs plain int
Two threads each run `counter++` 1000 times on a plain `int`. Result, and fix?
- **A:** Anything up to 2000, usually less. `++` is read-modify-write, not atomic. Use `AtomicInteger` (CAS) or `synchronized`.

# HTTP and REST

## Safe retries
A client times out and retries. Which of GET, POST, PUT, PATCH, DELETE can be retried blindly, and why?
- **A:** GET, PUT and DELETE are idempotent: repeating gives the same server state. POST is not (may create duplicates). PATCH is not guaranteed idempotent.
- **Pitfall:** make POST retry-safe with an idempotency key.

## GET for updates
Why must you not use GET to change data, and why not give GET a body?
- **A:** GET is defined as safe and is cached, prefetched and replayed by browsers, proxies and crawlers; URLs also land in logs and history. A GET body has no defined semantics and some servers or proxies drop or reject it.

## REST stateless
Behind a load balancer, login with an in-memory server session breaks. Why, and what does stateless fix?
- **A:** The next request may hit another instance that lacks the session. Stateless means each request carries what the server needs (for example a token), so any instance can serve it and you scale horizontally.
- **Pitfall:** a cookie is only the client-side carrier; the session is the server-side state it points to.

## WebSocket vs HTTP
How does a WebSocket start and when is it worth it?
- **A:** HTTP/1.1 request with `Upgrade: websocket`, answered `101 Switching Protocols`; then one persistent full-duplex TCP connection. Worth it for server push and low latency (chat, live data). Cost: stateful connections that are harder to load-balance and scale.

## Review this API
```
GET  /users
POST /users/new
POST /users/:id/update
DELETE /users/delete?id=:id
```
What would you change?
- **A:** Nouns plus HTTP verbs: `GET /users?name=&page=&size=` (search via query parameters, cacheable), `POST /users` (create), `PUT` or `PATCH /users/{id}` (update), `DELETE /users/{id}`. The id belongs in the path because it identifies the resource.
- **Pitfall:** do not move simple search to POST; reserve POST search for filters too large or complex for a URL.

# Security

## JWT contents
Can a client read a JWT without the key? Is it OK to put sensitive data in it?
- **A:** Yes, anyone can decode it: header and payload are base64url, not encrypted (JWS). The signature proves integrity and origin, verified with the shared secret (HS256) or the issuer's public key (RS256). Never put secrets in the payload. Always check signature, expiry and algorithm.

## Access vs refresh token
The access token expires mid-session. What happens and how is it handled?
- **A:** The API answers 401. The client uses the long-lived refresh token, sent only to the authorization server, to get a new short-lived access token. If the refresh token is expired or revoked, the user logs in again.
- **Pitfall:** the resource server only accepts access tokens; "resource token" is not a standard OAuth term.

## Password storage
Passwords are stored as `md5(password + "fixedSalt")`. What is wrong and what do you use?
- **A:** MD5 is fast (cheap to brute-force) and broken; a single fixed salt means identical passwords give identical hashes and one precomputed table cracks everyone. Use a slow, adaptive hash (bcrypt, Argon2, scrypt, PBKDF2) with a unique random salt per password.
- **Pitfall:** hashing is one-way; encryption is reversible with a key and is for data you must read back. Also use parameterized queries against SQL injection.

# SQL and databases

## LEFT JOIN semantics
`customers LEFT JOIN orders`: how does a customer with no orders appear, and what goes wrong with `WHERE orders.status = 'PAID'`?
- **A:** One row with `NULL` in all order columns (INNER JOIN would drop it). A `WHERE` on the right table filters out the `NULL` rows, turning it into an inner join; put the condition in `ON`.

## Index internals
How does an index make a lookup fast, what does an insert do to it, and B-tree vs hash?
- **A:** A B-tree keeps keys sorted in a balanced tree: O(log n) lookup, plus range scans and ordering. An insert must also update the index (maybe splitting a page). Hash index: O(1) equality only, no ranges or ordering.

## Too many indexes
A query on 5M rows is slow, so someone indexes every column. Why can this make things worse?
- **A:** Every write must update every index (slower inserts/updates, more storage), and the planner still uses only a few. Design indexes from real queries; a composite index (a, b) serves `a` and `a+b`, not `b` alone (leftmost prefix).

## Index not used
Why can `WHERE LOWER(email) = 'x'` or `LIKE '%son'` skip your index, and how do you confirm it?
- **A:** A function on the column, or a leading wildcard, defeats a plain B-tree lookup. Confirm with `EXPLAIN` / `EXPLAIN ANALYZE` (index scan vs full scan). Fix: a functional index, or rewrite the predicate.

## ACID and isolation
Define ACID with a money transfer, and name the three read anomalies.
- **A:** Atomicity (both updates or none), Consistency (constraints still hold), Isolation (concurrent transactions do not see partial effects), Durability (committed data survives a crash). Anomalies: dirty read (uncommitted data), non-repeatable read (same row changes between reads), phantom (a new row appears in a repeated query).
- **Pitfall:** defaults differ: MySQL InnoDB is `REPEATABLE READ`, PostgreSQL is `READ COMMITTED`.

## Lost update
Balance is 100; two concurrent transfers of 80 each run `read balance; if balance >= amount then write balance - amount`. Result and fix?
- **A:** Both read 100 and both pass the check, so the balance ends at 20 and 160 left the account (a lost update). Fix: one transaction plus an atomic conditional update, `UPDATE account SET balance = balance - 80 WHERE id = ? AND balance >= 80`, and check the affected row count. Alternatively `SELECT ... FOR UPDATE`.

## Money type
Why not `float`/`double` or `int` for money, and what instead?
- **A:** `0.1 + 0.2 != 0.3` in binary floating point; `int` overflows at 2,147,483,647 and has no fraction. Use `BigDecimal` (build from a String, not a double) or integer minor units (cents) in a `long`.

## N+1 queries
```java
for (Course c : loadFirst1000Courses()) print(loadStudentsOf(c));
```
What is wrong and what is the cheapest fix?
- **A:** 1 + 1000 round-trips (N+1). One JOIN (or `WHERE course_id IN (...)`, or a fetch join in JPA) loads all students in a single query. Window functions are not needed.

## Second highest salary
Find the second highest distinct salary without `OFFSET` or CTE.
- **A:**
```sql
SELECT MAX(salary) FROM emp WHERE salary < (SELECT MAX(salary) FROM emp);
```
- **Pitfall:** this gives the second highest distinct value; ties at the top are skipped. Add your filter (for example a join date) inside both subqueries.

## Slow as data grows
Queries slow down as a table grows. What do you do, in order?
- **A:** Measure first (slow query log, `EXPLAIN`). Then add or fix indexes, rewrite queries (needed columns only, no functions on indexed columns, avoid N+1, keyset pagination). Then cache, add read replicas, partition or archive.
- **Pitfall:** normalization is a data-integrity tool, not a speed-up; sometimes you denormalize for reads.

## Read replica lag
After a user edits their profile, the page shows old data. Why with read replicas?
- **A:** Replication is asynchronous, so the replica lags the primary. Fixes: read from the primary right after a write (read-your-writes), sticky routing for a short window, or wait for replication.

# Spring

## Spring vs Spring Boot
What does Boot add to Spring, and why is Tomcat the default server, and how do you swap it?
- **A:** Spring is the framework (IoC/DI, MVC, data...). Boot adds opinionated auto-configuration, starters, an embedded server, actuator and executable JARs. Tomcat is the default because `spring-boot-starter-web` depends on `spring-boot-starter-tomcat`; exclude it and add `spring-boot-starter-jetty` or `-undertow` to swap.

## Multiple main classes
A Spring Boot project has several classes with `main()`. How do you choose which one runs?
- **A:** Set it in the build: Maven `spring-boot-maven-plugin` `<mainClass>` (or the `start-class` property), Gradle `springBoot { mainClass = '...' }`.

## @Transactional rollback
```java
@Transactional
public void pay() throws IOException { repo.save(x); throw new IOException(); }
```
Does it roll back?
- **A:** No. Default rollback is only for `RuntimeException` and `Error`; a checked exception commits. Use `@Transactional(rollbackFor = Exception.class)`.
- **Pitfall:** it works through a proxy, so it applies to calls coming from outside the bean.

## JDBC vs JPA vs Hibernate
How do they relate, and when do you still use plain JDBC with JPA?
- **A:** JDBC is the low-level SQL API. JPA is a specification; Hibernate is its most common implementation; Spring Data JPA sits on top. Use JDBC (`JdbcTemplate`, jOOQ) for bulk or batch work, complex native or reporting SQL and DB-specific features.

## Patterns inside Spring
Name three design patterns Spring itself uses and where.
- **A:** Front controller (`DispatcherServlet`), proxy (AOP, `@Transactional`), template method (`JdbcTemplate`), observer (application events), factory (`BeanFactory`).
- **Pitfall:** Spring's "singleton" scope is one instance per container, not the GoF per-JVM singleton.

## DIP vs DI
How does Spring help with the "D" in SOLID, and is DIP the same as DI?
- **A:** Dependency Inversion Principle: high-level code depends on abstractions. In Spring you inject an interface, so you can swap the implementation, for example a mock in tests. DIP is a design principle; DI is the technique, and the IoC container performs it.

# Architecture

## Queue consumer crash
A payment consumer processes a message, then crashes before acknowledging. What happens and how do you protect the system?
- **A:** The broker redelivers (at-least-once delivery), so the payment could be processed twice. Make consumers idempotent with an idempotency key or a unique constraint, and ack after the work is durable.

## Cache-aside
Describe cache-aside reads/writes and the risk.
- **A:** Read: check cache, on a miss read the DB and populate the cache with a TTL. Write: update the DB, then invalidate (or update) the cache.
- **Pitfall:** stale data between write and invalidation, and cache stampede on popular expiries.

# JavaScript and React

## var vs let
```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i));
```
What prints, and what if it is `let`?
- **A:** `3 3 3` with `var` (one function-scoped binding, read after the loop). `let` gives `0 1 2` (new block-scoped binding per iteration). `var` is hoisted as `undefined`; `let` is in the temporal dead zone until declared.

## null vs undefined
Difference, and `typeof null`, `null == undefined`, `null === undefined`?
- **A:** `undefined`: no value assigned. `null`: an intentional "empty" value. `typeof null` is `"object"` (a historical quirk); `null == undefined` is `true`; `null === undefined` is `false`.

## this
```js
const o = { n: 1, f() { return this.n; } };
const g = o.f; g();
```
What happens?
- **A:** `this` depends on how a function is called, not where it is defined. Detached from `o`, `this` is `undefined` in strict mode (TypeError) or the global object in sloppy mode (so `undefined`). Arrow functions capture `this` lexically.

## useState updates
Calling `setCount(count + 1)` twice in one handler increases the count by how much, and how do you fix it?
- **A:** By 1: both calls use the same stale `count` and updates are batched. Use the functional form, `setCount(c => c + 1)`, to get +2.

## useEffect dependencies
What is the difference between no deps array, `[]` and `[x]`?
- **A:** No array: runs after every render. `[]`: after mount (cleanup on unmount). `[x]`: after mount and whenever `x` changes. Return a cleanup function for subscriptions and timers.
- **Pitfall:** in development, Strict Mode runs mount effects twice to expose missing cleanup.

## Redux vs localStorage
When do you use Redux state versus `localStorage`?
- **A:** Redux is in-memory app state with predictable updates and subscriptions that re-render components; it is lost on refresh. `localStorage` persists strings (about 5 MB), is synchronous, has no reactivity, and is readable by any script (XSS), so never store secrets. They are often combined: persist a slice of the store.

See also: [[Java]], [[Spring-boot]], [[JPA]], [[PostgreSQL]], [[ReactJS]], [[Redux]]
