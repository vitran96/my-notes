# Auto redirect

We can use `navigate` from [[React-router]] to change route.

# Re-render

Everything in React is about re-render. If not structure `useEffect` properly, it will re-render too many time.

## Avoid continuous re-render

~~If a component has too many component, move them into 1 component -> This will likely avoid re-rendering on every change.~~
Reduce `useEffect` to avoid too many re-render

# Search & Result page

Note:
- We can do sub-route to auto get search result. But this is not recommended since it can cause performance issue on [[Front-end]].
	- At least, I don't how to make it performance well.
	- My latest build at most use sub-route to auto-fill the search input -> mainly to make testing faster.

```javascript
// Define both main and sub-route for 1 page
route = {
	route: '/main-route',
	component: <PageComponent>,
	children: {
		route: '/child-route',
		component: <PageComponent>,
	}
}
```

```javascript
// Then in the PageComponent, we can check route if we are in the sub-route
// NOTE: is just snippet, not correct code
const l = useLocation();
const isSubPath = l.endWiths('child-route');
```

# [[eslint]] & [[prettier]] config

%% TODO: get config from TSS project %%
[[eslint]] config integrate with [[prettier]]

[[eslint#Config]] `eslintrc.json`
```json
{
}
```

[[eslint#ignore]] `.eslintignore`
```
```

[[prettier#Config]] `prettierre.js`
```
```

[[prettier#ignore]] `.prettierignore`
```
```

# [[Vite]] & [[Typescript]] config

%% TODO: get config from TSS project %%
[[Vite]] config integrate with [[Typescript]] route alias.

[[Vite#Config]] `vite.js`
```
```

[[Typescript#Config]] `tsconfig.json`
```
```

# [[a11y]]

Accessibility or [[a11y]]

## Tools/Plugins

## Rules

# [[l10n]]

Localization or [[l10n]]

## Tools/Plugins

## Rules

# [[i18n]]

Internationalization or [[i18n]]

## Tools/Plugins

`i18n-next`

## Rules