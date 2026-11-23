---
para: resource
tags: [frontend]
---
Front-end build tool and dev server (esbuild/Rollup-based). [[NodeJS]] tooling for [[Web development]].
Work with:
- [[ReactJS]]
- [[Preact]]
- etc...

# Proxy
```typescript
export default defineConfig({ server: { proxy: { '/api': { target: 'http://localhost:8080', changeOrigin: true } } } })
```

# Config

`vite.config.ts`

%% TODO %%

# Env

`.env`, `.env.local`, `.env.[mode]`, `.env.[mode].local` (later wins); only `VITE_*` vars are exposed via `import.meta.env`.

# Vi test

%% TODO: Vitest %%