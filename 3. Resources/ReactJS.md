---
para: resource
tags: [frontend]
---
See also: [[Redux]], [[Zustand]], [[Tanstack]], [[Tailwind CSS]]

# Re-render

Everything in React is about re-render. If not structure `useEffect` properly, it will re-render too many time.

## Avoid continuous re-render

~~If a component has too many component, move them into 1 component -> This will likely avoid re-rendering on every change.~~
Reduce `useEffect` to avoid too many re-render

# [[eslint]] & [[prettier]] config

%% TODO: get config from a past project %%
[[eslint]] config integrate with [[prettier]]

[[eslint#Config]] `eslint.config.js` (ESLint 9 flat config; `.eslintrc.json` is legacy)
```json
{
}
```

[[eslint#ignore]] `.eslintignore`
```
```

[[prettier#Config]] `.prettierrc.js`
```
```

[[prettier#ignore]] `.prettierignore`
```
```

# [[Vite]] & [[Typescript]] config

%% TODO: get config from a past project %%
[[Vite]] config integrate with [[Typescript]] route alias.

[[Vite#Config]] `vite.config.ts`
```
```

[[Typescript#Config]] `tsconfig.json`
```
```

# [[a11y]]

Accessibility or [[a11y]]

## Tools/Plugins

%% TODO %%

## Rules

%% TODO %%

# [[l10n]]

Localization or [[l10n]]

## Tools/Plugins

%% TODO %%

## Rules

%% TODO %%

# [[i18n]]

Internationalization or [[i18n]]

## Tools/Plugins

`i18next` / `react-i18next`

## Rules

%% TODO %%

# React-routing

## Auto redirect

We can use `navigate` from [[React-router]] to change route.

## Search & Result page

Note:
- We can do sub-route to auto get search result. But this is not recommended since it can cause performance issue on [[Front-end]].
	- At least, I don't how to make it performance well.
	- My latest build at most use sub-route to auto-fill the search input -> mainly to make testing faster.

```javascript
// Define both main and sub-route for 1 page
createBrowserRouter([{ path: '/main-route', element: <PageComponent />, children: [{ path: 'child-route', element: <PageComponent /> }] }])
```

```javascript
// Then in the PageComponent, we can check route if we are in the sub-route
const isSubPath = useLocation().pathname.endsWith('child-route');
// or better
const isSubPath2 = !!useMatch('/main-route/child-route');
```

## NavLink

How does NavLink know if we are in a route?
NavLink compares its `to` with the current location and sets an `active` class; add `end` to match exactly.
