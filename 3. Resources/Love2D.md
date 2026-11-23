---
para: resource
tags: [game-dev]
---
 [[Lua]] [[Game Engine]]

See also: [[Game development skill]], [[Math]]

# Set Color

```lua
love.graphics.setColor(r, g, b, a)
love.graphics.setColor({r, g, b, a})
```

Since LÖVE 11.0 colour components are 0–1; convert bytes with `love.math.colorFromBytes(r, g, b, a)` or divide by 255.

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

Prefer "static" for web (love.js) builds; streaming is unreliable there %% verify %%

# [[Web]] build

%% TODO %%

# [[Window OS]] build

%% TODO %%