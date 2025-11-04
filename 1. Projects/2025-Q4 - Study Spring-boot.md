---
start: 2025-10-02
end: 2025-11-10
---

# Scope
- Spring config
- Spring security & filter chain & Authorization
- Spring life-cycle
- Spring component
- Spring error handling
- Spring with migration tool
- Migration flow for DEV and PROD
- JPA & Hibernate
- JUnit & Mokito
- TestContainer

# Course 1

- Session 1 — [[REST API]], Validation, Error Handling
	- Design a small [[REST API]] surface (resource modeling, versioned paths, idempotent verbs).	    
	- Apply [[Jakarta]] Bean Validation on inputs and surface clean 400 errors.
	- Shape a consistent [[JSON]] error envelope (validation, not-found, conflict).
	- Structure controller–service–repository cleanly for testability.
	- Decide when to return 201 vs 200 and how to set `Location` headers.
- Session 2 — Security Filter Chain (no redirects), Public Docs
	- How [[Spring Security]] chooses a filter chain (first matching chain/rule wins).
	- Configure APIs to return **401/403 [[JSON]]** instead of redirecting to a login page.
	- Open specific endpoints ([[OpenAPI]] `/v3/api-docs`, Scalar UI `/scalar`, health) while securing the rest.
	- Use method-level guards (`@PreAuthorize`) for fine-grained authorization.
	- Create clear auth semantics for anonymous, user, and admin roles.
- Session 3 — Actuator, Observability, [[OpenAPI]] Polish
	- Enable and safely expose Actuator endpoints in dev (health, metrics, info, env, thread dump).
	- Read and reason about [[HTTP]] metrics (e.g., `http.server.requests`) to spot latency/errors.
	- Attach app metadata to `/actuator/info` for release diagnostics.
	- Keep API docs public while the API remains protected.
	- Verify [[OpenAPI]]/Scalar paths and understand where the spec/UI live.
- Session 4 — [[MySQL]] Profile & [[Testcontainers]] Integration
	- Switch from [[H2]] to [[MySQL]] via [[Spring profiles]] without code changes.
	- Use [[Testcontainers]] to run real [[MySQL]] in [[Integration Test|integration tests]] (ephemeral, reproducible).
	- Inject container [[JDBC]] properties at test runtime (`@DynamicPropertySource`).
	- Validate persistence guarantees (identity, constraints, sorting, cascading).
	- Handle and assert on database exceptions (e.g., unique constraint violations).
- Session 5 — SPA Concerns: [[CORS]], [[CSRF]], Login Flow Choices
	- Configure [[CORS]] so a React dev server can call your APIs safely.  
	- Understand when [[CSRF]] applies (cookie-based sessions) vs when it doesn’t (stateless [[JWT]]).
	- Compare session/form-login vs stateless [[JWT]] for [[SPA]]s and choose trade-offs.
	- Ensure unauthenticated [[SPA]] calls get **[[JSON]] 401/403** (no HTML redirects).
	- Define preflight behavior and verify the right `Access-Control-*` headers are returned.
- Session 6 — Checkstyle & Formatting:
	- Maven check style.
	- Maven formatting.

## Session 1
1. Create base code
2. Create base migration with [[Liquibase]]
3. Create / Generate JPA's Entity
	1. Generation of JPA is not worth it if the scope is small & we want customized Entity instead of the DEFAULT generation
	2. Generation of changelog might be worth to check
4. Customer CRUD
	1. Start with simple base CRUD
5. [[Unit Test]]

## Session 2
1. Create User CRUD
2. Auto create ADMIN account
3. Setup Spring-security
4. Setup Authentication Filter chain
5. [[Unit Test]] User CRUD & Security Filter chain & Security (where possible)
6. [[Integration Test]] Security & Security Filter chain

## Session 3

# Course 2