# Signing Mods — Automatic and Manual (Pro)

### Automatic Signing (bot @nulls_mods_bot)

Suitable for simple mods consisting of a single JSON file without resources.

1. Send your `.json` to [@nulls_mods_bot](https://t.me/nulls_mods_bot) or
   to the [#Signing Requests Lite](https://t.me/nb_mods/4827) topic with the `/sign` command.
2. The bot will return a signed `.NullsBrawlAssets` file.

**Limitations:**
- Only `content.json` — no textures, sounds, or icons.
- The signed file is valid for **3 days**.
- `@author` is filled automatically with your nickname and Telegram ID.
- UUID is generated from the `@author` + `@title` combination.
- `icon.png` is not supported.

### Manual Signing (Pro)

Suitable for any mod with resources and for publishing to the library.

**Preparing the ZIP archive:**
- `content.json` in the archive root (required)
- resource files in their respective folders (`sc3d`, `sfx`, `music`, etc.)
- `icon.png` — square PNG, up to 640×640 (optional)

**Process:**
1. Generate a UUID (see UUID guide). New mod — new UUID, update — same UUID.
2. Send the archive to the **#Signing Requests Pro** topic (find the link in the pinned messages of [t.me/nb_mods](https://t.me/nb_mods)).
3. Include in your message: name, description, UUID, version (for updates),
   links to screenshots/videos from @nb_mods (if any).
4. Wait for review — from a few hours to a few days.

**Requirements:**
- Name and description must be error-free, preferably localized in RU and EN.
- Using someone else's UUID without the author's permission is prohibited.
- For updates, the version must be higher than the previous one.

**Publishing to the library:**
Add "Please add to the library" to your message. The mod must introduce substantial changes.

### Before Submitting

- Make sure `content.json` is valid.
- All file paths are correct.
- For mods with `RADIO_GROUP` — no more than one feature enabled by default.
