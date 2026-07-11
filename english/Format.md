# Null's Brawl Modifications

Specification version: v0 (unstable)

### Basic Concepts

**Modification** — a special package that can be loaded by the mod loader.

Initially represented as a directory. The modification ID is strictly the name of
that directory.

**Modification ID** — a valid UUID that must be unique for each modification.

### Directory Structure

The directory must always contain a **content.json** file in JSON format, containing
a [Modification](#Modification) object.

## Loading a Modification: Main Algorithm

![Activity Diagram](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/nulls-mods-community/docs/refs/heads/main/russian/FormatLoading.puml)

Within a single modification, data is guaranteed to be processed in the following order:

1. The modification itself.
2. Its individual features in order of their **@priority** from lowest to highest;
   features with equal priority values are loaded in lexicographic order by their ID.

## Loading a Modification: Table Patching

Mods often need to modify table files, such as `skin_confs.csv`. However, such files
cannot be changed directly — the mod must describe the **changes** (delta), so the
loader can patch the game's tables itself. This approach minimizes conflicts between
mods and simplifies updating mods to newer game versions.

### Changes: General Format

Changes for each individual table are described by a [TablePatch](#TablePatch) object.

A simple example:

```json
{
  "PiggyLevel_0_0": {
    "State": 6,
    "ShownLevelInCounter": 5
  }
}
```

Applying this to the **club_piggy_levels.csv** table will change the first row
(**PiggyLevel_0_0**): the **State** column becomes 6 and **ShownLevelInCounter** becomes 5.

The list of tables with their changes is described by a [TablePatches](#TablePatches) object:

```json
{
   "club_piggy_levels": {
      "PiggyLevel_0_0": {
         "State": 6,
         "ShownLevelInCounter": 5
      }
   }
}
```

### Changes: Value Types

The loader expects the JSON value type to match the type defined in the table header.

| Table type            | JSON type                                   |
|-----------------------|---------------------------------------------|
| String, StringArray   | String literal (example: **"MyText"**)      |
| Int, IntArray         | Number without quotes (example: **123**)    |
| Boolean, BooleanArray | **true** or **false**                       |

A **null** value in JSON is valid regardless of the table type. It fully clears the
corresponding cell (equivalent to **""** for text data).

If the loader detects a type mismatch, it may attempt to fix it — for example, using
`Integer.parseInt` to convert a string to a number. This behavior is not guaranteed.

### Changes: Arrays in Values

Arrays are allowed as values.

This is useful when a single object in a table (under one Name) can span multiple rows.

<details>
<summary>Example of such a table</summary>

| Name      | Colors   | Speed | Scale |
|-----------|----------|-------|-------|
| String    | String   | Int   | Int   |
| FreeOffer | FF9FFF72 | 40    | 100   |
|           | FF68E524 |       |       |
|           | FF68E524 |       |       |
|           | FF9FFF72 |       |       |
|           | FFE0FFA0 |       |       |
| ...       | ...      | ...   | ...   |

In this table, the **FreeOffer** object spans five rows, semantically describing
an array of **Colors** values.

In JSON, this can be written as an array:

```json
{
  "FreeOffer": {
    "Colors": [
      "FF9FFF72",
      "FF68E524",
      "FF68E524",
      "FF9FFF72",
      "FFE0FFA0"
    ]
  }
}
```

</details>

If the number of rows for an object in the table (X) differs from the array size (Y)
in JSON, the loader behaves as follows:

1. Y > X (size increased): the loader adds new CSV rows to accommodate the new array values.
2. X > Y (size decreased): the loader writes the first Y values, and all subsequent cells
   below are cleared. If a row becomes completely empty after clearing, it is deleted.

Additional properties that follow from the above:

1. An array `[X, Y, Z, null]` is processed the same as `[X, Y, Z]`.
2. A single-element array `[X]` is processed the same as a plain value `X`.

### Changes: Adding New Objects

If the changes JSON references an undefined object, it will be created. New rows will
be added at the end of the table.

If there are multiple such objects, their exact order in the table is not defined.

## Reference: Core JSON Objects

### Modification

Object. Describes a modification.

| Key                                  | Type                            | Description                                                        |
|--------------------------------------|---------------------------------|--------------------------------------------------------------------|
| @title <sup>(required)</sup>         | [IntlString](#IntlString)       | Modification name                                                  |
| @description <sup>(required)</sup>   | [IntlString](#IntlString)       | Modification description                                           |
| @author                              | [IntlString](#IntlString)       | Modification author                                                |
| @version                             | [Version](#Version)             | Modification version; defaults to "1.0.0" if not specified         |
| @gv <sup>(required)</sup>            | Integer                         | Game version the modification is compatible with                   |
| @spec                                | Integer                         | Specification version the modification was created with            |
| @ilib <sup>(internal)</sup>          | Boolean                         | Whether the modification is published in the library               |
| @categories <sup>(internal)</sup>    | String[]                        | Categories for library publication                                 |
| @features                            | [Features](#Features)           | Optional (toggleable) modification features                        |
| @feature_groups                      | [FeatureGroups](#FeatureGroups) | Feature groups                                                     |
| @root                                | String                          |                                                                    |
| @patches                             | String[]                        |                                                                    |

Extends [TablePatches](#TablePatches) — all other keys belong to that object.

### Features

Object with arbitrary keys.

Key: text ID of the feature.

Value: a [Feature](#Feature) object.

### Feature

Object. Describes optional mod features.

| Key                           | Type                      | Description                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------|
| @name <sup>(required)</sup>   | [IntlString](#IntlString) | Feature name                                                                                             |
| @description                  | [IntlString](#IntlString) | Feature description                                                                                      |
| @enabled                      | Boolean                   | Default state (enabled: if true or key absent; disabled: if explicitly set to false)                     |
| @conflicts                    | String[]                  | List of feature IDs that cannot be enabled simultaneously with this one                                  |
| @priority                     | Integer                   | Priority (defaults to 0 if not specified)                                                                |
| @root                         | String                    |                                                                                                          |
| @patches                      | String[]                  |                                                                                                          |

Extends [TablePatches](#TablePatches) — all other keys belong to that object.

### FeatureGroups

Object with arbitrary keys.

Key: text ID of the feature group.

Value: a [FeatureGroup](#FeatureGroup) object.

### FeatureGroup

| Key                           | Type                      | Description                                        |
|-------------------------------|---------------------------|----------------------------------------------------|
| @name <sup>(required)</sup>   | [IntlString](#IntlString) | Feature group name                                 |
| @description                  | [IntlString](#IntlString) | Feature group description                          |
| @type                         | String                    | Type: DEFAULT or RADIO_GROUP                       |
| @features                     | String[]                  | List of feature IDs included in this group         |

### IntlString

Localized string.

The value of this type can be either:

1. A plain string;
2. A dictionary where the key is a language code and the value is a text string.

The language code follows the [ISO 639](https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes)
standard, written in uppercase. Examples: EN, RU, ZH, FR.

When using a dictionary, the `EN` key must be present. It is used for English and as
a fallback when the user's language is not in the dictionary.

### Version

A string in the format "X.Y.Z", where X, Y, Z are numeric version values. Example: "1.0.0".

### TablePatches

Object with arbitrary keys. Describes a list of tables and their patches.

Key: table filename without extension; cannot start with @ (example: skin_confs).

Value: a [TablePatch](#TablePatch) object.

### TablePatch

Object with arbitrary keys. Describes changes to a specific table.

Key: object name in the table.

Value: object with arbitrary keys (key = column name, value = cell values).

See [Loading a Modification: Table Patching](#loading-a-modification-table-patching)
for more details.
