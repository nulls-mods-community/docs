# Advanced Patching — @patches and @root

`@patches` and `@root` simplify maintaining large mods. They work both at the mod root
and inside individual features.

### @patches

Moves the patches JSON object into a separate file, keeping `content.json` clean.

```json
{
  "@title": "My mod",
  "@patches": "patches/skins.json"
}
```

`patches/skins.json`:
```json
{
  "skins": { ... },
  "skin_confs": { ... }
}
```

The path is relative to the archive root — `@root` does not affect it.

> **Important:** if `@patches` is specified, no other patches are allowed in the same
> object — the mod will not be signed.

### @root

Specifies the folder inside the archive where the mod's resources are located.

```json
{
  "@root": "my_res"
}
```

The game will look for files relative to `my_res` instead of the archive root.

> Works stably starting from `@gv:64`. May not work in version 63.286.

### Combined Usage

```json
{
  "@title": "Large mod",
  "@root": "assets",
  "@features": {
    "feature1": {
      "@name": "Feature 1",
      "@root": "feature1_res",
      "@patches": "patches/feature1.json"
    }
  }
}
```

Paths in `@patches` are always relative to the archive root, regardless of `@root`.
