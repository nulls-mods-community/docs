# Features (@features) and Feature Groups (@feature_groups)

Features let you split a mod into independent toggleable parts.
The user chooses which ones to enable.

### @features

```json
"@features": {
  "feature_id": {
    "@name": "Feature name",             // required
    "@description": "Description",      // optional
    "@enabled": true,                   // enabled by default (default: true)
    "@priority": 0,                     // load order (lower = earlier)
    "@conflicts": ["other_feature"],    // incompatible features
    "@root": "resources_folder",
    "@patches": "path/to/file.json"
    // any table patches, same as in mod root
  }
}
```

**Example:**
```json
{
  "@title": "Mod with features",
  "@features": {
    "extra_skins": {
      "@name": "Extra skins",
      "@enabled": true,
      "skins": {
        "Colt_skin": { "Character": "Colt", "Model": "colt_gold.scw" }
      }
    },
    "custom_music": {
      "@name": "Custom music",
      "@enabled": false,
      "music": { "lobby": "mymusic.mp3" }
    }
  }
}
```

### @feature_groups

Groups features into a visual block. Types:
- `DEFAULT` — checkboxes, multiple features can be enabled.
- `RADIO_GROUP` — radio buttons, only one feature can be enabled.

```json
"@feature_groups": {
  "group_id": {
    "@name": "Group name",
    "@description": "Description",
    "@type": "RADIO_GROUP",
    "@features": ["feature_id1", "feature_id2"]
  }
}
```

> **Important for RADIO_GROUP:** no more than one feature in the group
> should be enabled by default, otherwise the mod will not be signed.

**Example:**
```json
{
  "@title": "Mode select",
  "@features": {
    "mode_easy": { "@name": "Easy mode", "@enabled": true },
    "mode_hard": { "@name": "Hard mode", "@enabled": false }
  },
  "@feature_groups": {
    "difficulty": {
      "@name": "Difficulty",
      "@type": "RADIO_GROUP",
      "@features": ["mode_easy", "mode_hard"]
    }
  }
}
```

### Load Order

1. Root patches from `content.json` are always active.
2. Enabled features are loaded by `@priority` (lower means earlier), with equal priorities sorted lexicographically by ID.

A later feature overrides data from an earlier one.

### Limitations

You cannot use both `@patches` and inline patches in the same feature body — only one or the other.
