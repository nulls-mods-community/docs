# UUID and Mod Versions

### UUID (Modification-Id)

**For unpacked mods (folders):**
The UUID is determined by the folder name. Create a folder named after the UUID
and place the mod files inside:

```
📁 bd850738-67cb-4583-92c4-a12f4f6080d1
└─ 📝 content.json
```

**For signed files (.NullsBrawlAssets):**
The UUID is specified in `META-INF/MANIFEST.MF` in the `Modification-Id` field.

**How to generate:**
- Browser: [uuidgenerator.net](https://www.uuidgenerator.net)
- Linux/macOS: `uuidgen`
- PowerShell: `[guid]::NewGuid()`

When updating a mod, the UUID stays the same. For a new mod — generate a new UUID.

> **Note:** automatic signing via the bot generates a UUID based on the
> `@author` + `@title` combination, so it will be consistent for the same mod.

### Versions (@version)

The version is specified in `content.json` in the `@version` field, format `X.Y.Z`:

```json
{
  "@title": "My mod",
  "@version": "1.2.0"
}
```

If the field is absent, the default version is `"1.0.0"`.

Strict SemVer compliance is not required — the only rule is that the new version's
numbers (left to right) must be higher than the previous one:

`1.0.0` → `1.0.1` → `1.1.0` → `2.0.0`

The mod loader considers the version with the higher number in the most significant
position to be newer. In the library, users see the latest version of the mod.
