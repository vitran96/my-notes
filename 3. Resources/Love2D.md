 [[Lua]] [[Game Engine]]

# Set Color

```lua
love.graphics.setColor(r, b, g, alpha)
love.graphics.setColor({r, b, g, alpha})
```

This function currently doesn't support RGB value 0->255. To use this, you have to divide by `255`. Eg: `love.graphics.setColor({160/255, 160/255, 160/255})` or use `love.graphics.setColor(love.math.colorFromBytes(r, g, b, alpha))`

# Load and play music

```lua
bgmSoundSrc = love.audio.newSource(BGM_PATH, "stream")
bgmSoundSrc:play()
```

## Play sound effect

It is better to load as `static` and use `clone()` to clone the obj before play

```lua
uiSoundSrc = love.audio.newSource(UI_SOUND_PATH, "static")
uiSoundSrc:clone():play()
```

## Use static sound for web

[[Gemini AI]] recommend that we use `static` if targeting the [[Web]] build

# [[Web]] build

# [[Window OS]] build