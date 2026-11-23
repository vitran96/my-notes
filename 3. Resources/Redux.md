---
para: resource
tags: [frontend]
---
Predictable global state container; write it with Redux Toolkit (RTK). RTK Query is RTK's data-fetching/caching layer.

# Store

%% TODO %%

# RTK Query

Similar to [[Tanstack#Query|TanStack Query]], this allow us to create query to interact with a server/service.
We can do caching or configure caching store and config multiple things.

Advantages compare to direct [[HTTP#Client]] usage:
- Auto caching
- Opinionated -> force dev to follow 1 style
- Auto state management
- Integration with Redux Dev tools

## Auto generate API file

Article: https://redux-toolkit.js.org/rtk-query/usage/code-generation#openapi
TLDR, it is possible to auto-generate API file using [[OpenAPI|Swagger]] (package `@rtk-query/codegen-openapi`)