[[Lua]] [[Game Engine]]

# Set Color

```lua
love.graphics.setColor(r, b, g, alpha)
love.graphics.setColor({r, b, g, alpha})
```

This function currently doesn't support RGB value 0->255. To use this, you have to divide by `255`. Eg: `love.graphics.setColor({160/255, 160/255, 160/255})` or use `love.graphics.setColor(love.math.colorFromBytes(r, g, b, alpha))`