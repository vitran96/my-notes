---
para: resource
tags: [frontend]
---
# Pseudo classes

```css
/* example */
a:focus { }
a:active { }
/* focus ring only for keyboard users */
a:focus-visible { }
```
# clip-path vs transform

`clip-path` clips everything outside the shape, including border, outline and box-shadow; to get a shaped button that keeps its border/outline, shape it with `transform` (e.g. skew) instead. See also: [[Tailwind CSS]]
