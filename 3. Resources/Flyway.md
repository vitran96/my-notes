Aternative [[Relational database|SQL DB]] migration tool to [[Liquibase]] for [[Java]].
Integrated with [[Spring-boot]]

Note:
- No down migration
- Can use Java code for migration
- On failure, it might not show clear message

# Filename format

- Version name: `V<version>__<summary>.sql`
- Revision: `R__<summary>.sql`