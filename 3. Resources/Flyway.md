---
para: resource
tags: [java, database]
---
Alternative [[Relational database|SQL DB]] migration tool to [[Liquibase]] for [[Java]].
Integrated with [[Spring-boot]]

Note:
- No undo in the free/community edition; undo migrations (`U<version>__x.sql`) are a paid-edition feature
- Can use Java code for migration
- On failure, it might not show clear message

# Filename format

- Version name: `V<version>__<summary>.sql`
- Repeatable: `R__<summary>.sql` — re-applied whenever its checksum changes, after all versioned migrations

# Spring Boot integration

Spring-boot can auto recognize [[Flyway]] dependancies in the class-path so you don't need additional configuration.

## Config

%% TODO: %%
