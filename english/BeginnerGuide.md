# How to start creating mods for Null's Brawl?

### Join our chat and install the special client

Currently, all important discussions regarding modifications and their development take place in the Telegram chat: [t.me/nb_mods](https://t.me/nb_mods).

There, in the #Annoucements section, you will also be able to download a special APK. Install it to unlock the next step.

### Create a mods folder

Create an empty folder with any name on the external storage. For example, NBMods.

Open Null's Brawl, go to the mod loader settings, and select this folder as the external source.

### Create your first mod

Each new mod must have its own unique ID in UUID format. Go to the website [uuidgenerator.net](https://uuidgenerator.net) to generate one.

Navigate to the mods folder you created in the previous step. Inside it, create another folder whose name exactly matches the generated UUID of the mod.
This folder is now your mod's folder.

Inside it, create a file named **content.json** (lowercase — this is important). Place the following code inside this file:

```json
{
  "@title": "My first mod",
  "@description": "And its description",
  "@gv": 65,
  "en": {
    "TID_JESTER": {
      "EN": "CLOWN"
    }
  }
}
```

<details>
<summary>
How the file and folder structure should look
</summary>

```
╔ External storage
╚╦ 📁 NB Mods
 ╚╦ 📁 bd850738-67cb-4583-92c4-a12f4f6080d1
  ╚═ 📝 content.json
```

But this is just an example. Folders can be named differently.
</details>

### Enable your first mod and test it

Open Null's Brawl, go back to the mod loader settings, and you should see your modification in the list of mods.

Enable it and return to the game. If everything is done correctly, Chester will now be named "CLOWN".

### Next Steps

To create more complex mods, you should:

1. Learn how the game's files are structured;
2. Study the [mod format](Format.md) in more detail.