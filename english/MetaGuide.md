# Mod Metadata

`content.json` has three required fields: `@title`, `@description`, `@author`.
They support localization and HTML formatting.

### Required Fields

```json
{
  "@title": "Mod name",
  "@description": "Mod description",
  "@author": "Author"
}
```

### Localization

Instead of a string, you can pass an object with language codes (ISO 639, uppercase).
The `EN` key is required — it is used when the user's language is not found.

```json
{
  "@title": {
    "RU": "Супер мод",
    "EN": "Super mod",
    "TR": "Süper mod"
  },
  "@description": {
    "RU": "Добавляет крутые скины",
    "EN": "Adds cool skins"
  },
  "@author": "Your nickname"
}
```

### HTML Formatting

`@title`, `@description`, and `@author` support HTML tags
(based on `android.text.Html`):

- `<b>bold</b>`, `<i>italic</i>`, `<u>underline</u>`
- `<s>` / `<del>` — strikethrough
- `<font color='#RRGGBB'>colored text</font>`
- `<a href='URL'>link</a>`
- `<br>` — line break
- `<ul><li>list item</li></ul>`
- `<p>`, `<div>`, `<span>`, `<big>`, `<small>`, `<tt>`, `<code>`, `<sup>`, `<sub>`, `<h1>…<h6>`

```json
{
  "@title": "<b>Super mod</b>",
  "@description": "<p>Description:</p><ul><li><i>Cool feature</i></li></ul><br>Link: <a href='https://t.me/nb_mods'>Chat</a>",
  "@author": "<a href='https://t.me/danyanull'>danyanull</a>"
}
```

> Remember to escape quotes inside JSON.

### Recommendations

- Start sentences with a capital letter, put spaces after punctuation.
- Avoid transliteration — write "default", not "defoltny".
- The description should answer: what changes and how it affects the game.
