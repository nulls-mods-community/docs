# Null's Brawl Modifications

Specification version: unstable (January 10, 2026)

### Basic Concepts

**Modification** — a special package that can be loaded by the mod loader.

It is initially represented as a directory. The modification ID is strictly the name of this directory.

**Modification ID** — a valid UUID that must be unique for each modification.

### Directory Structure

The directory must always contain a **content.json** file in JSON format, containing a [Modification](#Modification) object.

## Modification Loading: Main Algorithm

![Activity Diagram](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/nulls-mods-community/docs/refs/heads/main/russian/FormatLoading.puml)

## Modification Loading: Table Patching

Modifications often need to change table files, e.g., skin_confs.csv. However, such files cannot be changed directly — the modification must describe the **changes** (delta),
so that the loader can itself "patch" the game tables (apply these changes). This approach is chosen to minimize the number of conflicts between modifications, as well as to simplify
updating modifications to a newer game version.

### Changes: General Representation

Within each individual table, changes are described by a [TablePatch](#TablePatch) object.

A clear example of this object looks like this:

'''json
{
  "PiggyLevel_0_0": {
    "State": 6,
    "ShownLevelInCounter": 5
  }
}
'''

If we apply the described changes to the **club_piggy_levels.csv** table, its first row (**PiggyLevel_0_0**) will change the values in the **State** column (to 6) and
**ShownLevelInCounter** (to 5).

The list of tables with changes in them is described by a [TablePatches](#TablePatches) object.

A clear example of this object for the table above looks like this:

'''json
{
   "club_piggy_levels": {
      "PiggyLevel_0_0": {
         "State": 6,
         "ShownLevelInCounter": 5
      }
   }
}
'''

### Changes: Value Types

The loader expects that the JSON value type specified will match the type defined in the table header.

| Table Type             | JSON Type                                |
|------------------------|------------------------------------------|
| String, StringArray    | String literal (example: **"MyText"**)   |
| Int, IntArray          | Number without quotes (example: **123**) |
| Boolean, BooleanArray  | **true** or **false**                    |

A **null** value in JSON is considered valid regardless of the table type. It allows completely "nullifying" the corresponding table cell (which will be analogous to **""** for
text data).

If the loader detects a data type mismatch, it may attempt to correct it. For example, use Integer.parseInt to convert a string to a number.
But this behavior is not guaranteed.

### Changes: Arrays in Values

Using arrays in values is allowed.

This makes sense in cases where a single object in a table (within one Name) can contain multiple rows.

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

In this table, the **FreeOffer** object consists of five rows that semantically describe an array of **Colors** values.

In JSON, this can be described as an array:

'''json
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
'''

</details>

If the number of rows of the object in the table (X) differs from the size of the array (Y) in the JSON, the loader's behavior will be as follows:

1. Y > X (size increased): the loader will add new CSV rows to allocate new cells for the array values;
2. X > Y (size decreased): the loader will write the first Y values of the array, and all subsequent cells below will be cleared. If after clearing a cell the row becomes completely empty,
   it will be deleted.

From the above information, some properties also follow:

1. An array `[X, Y, Z, null]` will be processed similarly to the array `[X, Y, Z]`;
2. An array with a single element `[X]` will be processed similarly to the plain value `X`.

### Changes: Adding New Objects

If a JSON change object refers to an undefined object, it will be created. Rows will be allocated for it at the end of the table.

If there are several such objects, their exact order in the table is not defined.

## Reference Information: Main JSON Objects

### Modification

Object. Describes a modification.

| Key                                  | Type                             | Value                                                   |
|--------------------------------------|----------------------------------|---------------------------------------------------------|
| @title <sup>(required)</sup>         | [IntlString](#IntlString)        | Modification title                                      |
| @description <sup>(required)</sup>   | [IntlString](#IntlString)        | Modification description                                |
| @author                              | [IntlString](#IntlString)        | Modification author                                     |
| @version                             | [Version](#Version)              | Modification version; if not specified, "1.0.0" is used |
| @gv <sup>(required)</sup>            | Integer                          | Game version the modification is compatible with        |
| @ilib <sup>(internal)</sup>          | Boolean                          | Whether the modification is published in the library    |
| @categories <sup>(internal)</sup>    | String[]                         | Categories for library publication                      |
| @features                            | [Features](#Features)            | Additional (toggleable) modification features           |
| @feature_groups                      | [FeatureGroups](#FeatureGroups)  | Groups of additional features                           |
| @root                                | String                           |                                                         |
| @patches                             | String[]                         |                                                         |

Extends [TablePatches](#TablePatches) — all other keys belong to this object.

### Features

Object with arbitrary keys.

Key: text ID of the feature.

Value: [Feature](#Feature) object.

### Feature

Object. Describes an additional mod feature.

| Key                           | Type                       | Value                                                                                           |
|-------------------------------|----------------------------|-------------------------------------------------------------------------------------------------|
| @name <sup>(required)</sup>   | [IntlString](#IntlString)  | Feature name                                                                                    |
| @description                  | [IntlString](#IntlString)  | Feature description                                                                             |
| @enabled                      | Boolean                    | Default state (enabled: if true or key is absent; disabled: if explicitly set to false)         |
| @conflicts                    | String[]                   | List of feature IDs that cannot be enabled simultaneously with this one                          |
| @root                         | String                     |                                                                                                 |
| @patches                      | String[]                   |                                                                                                 |

Extends [TablePatches](#TablePatches) — all other keys belong to this object.

### FeatureGroups

Object with arbitrary keys.

Key: text ID of the feature group.

Value: [FeatureGroup](#FeatureGroup) object.

### FeatureGroup

| Key                           | Type                       | Value                                                     |
|-------------------------------|----------------------------|-----------------------------------------------------------|
| @name <sup>(required)</sup>   | [IntlString](#IntlString)  | Feature group name                                        |
| @description                  | [IntlString](#IntlString)  | Feature group description                                 |
| @type                         | String                     | Type: DEFAULT or RADIO_GROUP                              |
| @features                     | String[]                   | List of feature IDs included in this group                |

### IntlString

Localized string.

The value of this type can be:

1. Either a plain string;
2. Or a dictionary where the key is the language code, and the value is a text string.

The language code follows the [ISO 639](https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes) standard, but is written in uppercase. For example: EN, RU, ZH, FR.

The dictionary (if used) must contain the EN key. It will be used for English, as well as for cases where the user's language key is not present in the dictionary.

### Version

String in the format "X.Y.Z", where X, Y, Z are numeric version values. For example, "1.0.0".

### TablePatches

Object with arbitrary keys. Describes a list of tables and patches (changes) within them.

Key: table file name without extension; cannot start with @ (example: skin_confs).

Value: [TablePatch](#TablePatch) object.

### TablePatch

Object with arbitrary keys. Describes changes in a specific table.

Key: object name in the table.

Value: object with arbitrary keys (key — column name, value — cell values).

More details about this object are written in the [Modification Loading: Table Patching](#modification-loading-table-patching) section.