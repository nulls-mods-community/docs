# How to Start Creating Mods for Null's Brawl?

### Join our chat and install the special client

All important discussions about mods and their development happen in the Telegram chat:
[t.me/nb_mods](https://t.me/nb_mods).

There, in the #Announcements section, you can download the special APK. Install it to
unlock the next step.

### Create a mods folder

On external storage, create an empty folder with any name, e.g. `NBMods`.

Open Null's Brawl, go to the mod loader settings, and select this folder as the
external source.

### Create your first mod

Every new mod must have a unique ID in UUID format. Go to
[uuidgenerator.net](https://uuidgenerator.net) to generate one.

Go to the mods folder you created in the previous step. Inside it, create another
folder whose name exactly matches the generated UUID. This folder is now your mod's folder.

Inside it, create a file named **content.json** (lowercase — this matters). Put the
following code inside:

```json
{
  "@title": "My first mod",
  "@description": "And its description",
  "@gv": 65,
  "ru": {
    "TID_JESTER": {
      "EN": "CLOWN"
    }
  }
}
```

<details>
<summary>
What the file and folder structure should look like
</summary>

```
╔ External storage
╚╦ 📁 NB Mods
 ╚╦ 📁 bd850738-67cb-4583-92c4-a12f4f6080d1
  ╚═ 📝 content.json
```

This is just an example. Your folders can have different names.
</details>

### Enable your first mod and test it

Open Null's Brawl, go to the mod loader settings again, and you should see your
modification in the mods list.

Enable it and return to the game. If everything is done correctly, Chester will now
be called "CLOWN".

### Next steps

To create more complex mods, you should:

1. Learn how the game's files are structured;
2. Study the [mod format](Format.md) in more detail.
