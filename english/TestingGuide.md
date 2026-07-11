# Testing Mods Without Signing

A special Null's Brawl APK exists for testing mods during development,
allowing you to load unsigned mods.

The latest link is posted in the announcements of the Telegram topic
[t.me/nb_mods](https://t.me/nb_mods) under #Announcements.

> **Important:** the APK is intended only for testing your own mods.

### Method 1: Manual Installation

1. Create a folder for mods, e.g. `NullsBrawlMods`.
2. Inside it, create a subfolder named exactly as the mod's UUID.
3. Unpack the mod files (`content.json`, resources) into that folder.
4. In the Developer APK: mod settings → Add folder → select `NullsBrawlMods`.
5. Restart the game — the mod will appear in the list.
Starting from version 65.165, a separate APK is available for loading unsigned mods.

### Method 2: Nulls TestMods utility (by darkmean)

The utility automates mod installation for testing.

1. Install Nulls TestMods (link available in the modding chat).
2. Grant the app access to files.
3. Open your `.zip`, `.json`, or `.toml` via Share → Nulls TestMods.
4. The utility will create a `NullsBrawlMods` folder and unpack the mod with its UUID.
5. In Null's Brawl, add the `NullsBrawlMods` folder via mod settings
   (gear icon → Add folder), if you haven't done so already.
6. Enable the mod and restart the game.

> [!NOTE]
> The utility generates UUID automatically from the mod name.


Once the folder is added, new mods just need to be placed in UUID-named subfolders —
they will appear automatically on the next launch.
