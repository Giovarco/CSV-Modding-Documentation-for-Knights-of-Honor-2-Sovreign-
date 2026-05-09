# Knights of Honor II: Sovereign — CSV Modding Documentation

## Table of Contents

1. [File Taxonomy](#1-file-taxonomy)
2. [Core Game-Data Files](#2-core-game-data-files)
   - 2.1 [buildings.csv](#21-buildingscsv)
   - 2.2 [units.csv](#22-unitscsv)
   - 2.3 [kingdoms.csv](#23-kingdomscsv)
   - 2.4 [realms.csv](#24-realmscsv)
   - 2.5 [cultures.csv](#25-culturescsv)
   - 2.6 [factions.csv](#26-factionscsv)
   - 2.7 [CoA.csv](#27-coacsv)
   - 2.8 [papacy.csv](#28-papacycsv)
   - 2.9 [geo_features.csv](#29-geo_featurescsv)
   - 2.10 [properties.csv](#210-propertiescsv)
   - 2.11 [CheapBuildings.csv](#211-cheapbuildingscsv)
   - 2.12 [CheapMilitia.csv](#212-cheapmilitiacsv)
3. [Localisation & Text Files](#3-localisation--text-files)
   - 3.1 [languages.csv](#31-languagescsv)
   - 3.2 [preload.csv and language variants](#32-preloadcsv-and-language-variants)
   - 3.3 [teritories.csv and language variants](#33-teritoriescsv-and-language-variants)
   - 3.4 [character_names.csv and language variants](#34-character_namescsv-and-language-variants)
   - 3.5 [cultures (localisation)](#35-cultures-localisation)
   - 3.6 [factions (localisation)](#36-factions-localisation)
   - 3.7 [kingdom_descriptions.csv](#37-kingdom_descriptionscsv)
   - 3.8 [Vassalage.csv and language variants](#38-vassalagecsv-and-language-variants)
   - 3.9 [UnionQuess.csv and language variants](#39-unionquesscsv-and-language-variants)
   - 3.10 [Building name localisation files](#310-building-name-localisation-files)
   - 3.11 [Unit name localisation files](#311-unit-name-localisation-files)
   - 3.12 [Knight class localisation files](#312-knight-class-localisation-files)
   - 3.13 [Goods localisation files](#313-goods-localisation-files)
   - 3.14 [Opinion localisation files](#314-opinion-localisation-files)
   - 3.15 [Religion localisation files](#315-religion-localisation-files)
   - 3.16 [Religious titles localisation files](#316-religious-titles-localisation-files)
   - 3.17 [Settlement type localisation files](#317-settlement-type-localisation-files)
   - 3.18 [Kingdom type localisation files](#318-kingdom-type-localisation-files)
   - 3.19 [Nobility title localisation files](#319-nobility-title-localisation-files)
   - 3.20 [Character age localisation files](#320-character-age-localisation-files)
   - 3.21 [Fantasy name localisation files](#321-fantasy-name-localisation-files)
   - 3.22 [Inventory item localisation files](#322-inventory-item-localisation-files)
   - 3.23 [Nobility/title extended localisation (de_, pl_)](#323-nobilitytitle-extended-localisation-de_-pl_)
   - 3.24 [de_ miscellaneous files](#324-de_-miscellaneous-files)
4. [Voice-Over (VO) Files](#4-voice-over-vo-files)
   - 4.1 [VO.csv](#41-vocsv)
   - 4.2 [Narrator voice lines.csv](#42-narrator-voice-linescsv)
   - 4.3 [Knights voice lines.csv](#43-knights-voice-linescsv)
   - 4.4 [Units voice lines.csv](#44-units-voice-linescsv)
   - 4.5 [ADDITIONS variants](#45-additions-variants)
   - 4.6 [VIRTUAL variants](#46-virtual-variants)
5. [Duplicate / Backup Files Reference](#5-duplicate--backup-files-reference)

---

## 1. File Taxonomy

The 330 files in this archive fall into five categories.

**Category A — Core game-data files.** These are the files that directly control gameplay mechanics: building stats, unit stats, kingdom setup, province data, voice-over event routing, etc. They have no language prefix. These are the files most relevant to gameplay modding.

**Category B — World and map data.** Files that describe the political and geographic world: provinces, cultures, factions, the papacy roster, coat-of-arms assignments. Also no language prefix, but more static and descriptive.

**Category C — Localisation and text files.** The bulk of the archive. Each carries a two-letter (or longer) language prefix: `en_`, `fr_`, `de_`, `ru_`, `pl_`, `es_`, `tr_`, `ko_`, `ja_`, `zh_`, `zh_tw_`, `hi_`, `ind_`. Files of the same type share identical column structure across all languages. Additionally, `languages.csv` (no prefix) is the master string table containing every UI string across all 14 languages in a single file.

**Category D — Voice-over (VO) files.** `VO.csv` is the master subtitle/script table for all spoken narrator and knight lines. The companion files (`Knights voice lines.csv`, `Narrator voice lines.csv`, `Units voice lines.csv`) map in-game events to line sets. `ADDITIONS` variants are supplementary lines added on top of the base files. `VIRTUAL` variants appear to be generated reference sheets.

**Category E — Duplicate / backup files.** Files named with `(copia)` or `(un'altra copia)` (Italian: "copy" / "another copy") are unmodified originals kept as backups. They are not loaded by the game. See [Section 5](#5-duplicate--backup-files-reference) for the full list.

### The `;N` suffix convention

Several localisation files exist in numbered variants: `de_preload.csv`, `de_preload;2.csv`, `de_preload;8.csv`, `de_preload;9.csv`. These are **split files** — the game loads them as a single logical file, each covering a different subset of string keys. They share the same column structure. When modding, you edit whichever split file contains the key you want to change.

---

## 2. Core Game-Data Files

### 2.1 `buildings.csv`

**Active file:** `buildings.csv` (214 building definitions, ~586 lines including header and comments)

**Purpose:** Defines every buildable structure in the game — its cost, upkeep, bonuses it grants, and what geographical or religious condition is required to build it. The upgrade chain is encoded through the `BasedOn` column, which forms a tree of inheritance: a building inherits all values from its parent and only overrides what is explicitly set.

**Cross-file references:**
- `extend *` values are referenced by `CheapBuildings.csv` (overrides gold cost for specific buildings).
- `Requires` values reference resource/religion token names that the game matches against province slot types and kingdom religion. Religion tokens (`Christian`, `Muslim`, `Pagan`) correspond to religion keys in `realms.csv` and `kingdoms.csv`.
- `BasedOn` forms a self-referential inheritance chain within this same file.
- Building names displayed in-game are resolved via localisation files: `fr_building_names.csv` (French), `fr_building upgrades.csv` (French upgrade names), `ru_buildings.csv` / `ru_building upgrades.csv` (Russian). Other languages use `languages.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `extend *` | String (ID) | Unique identifier for this building. Used as the key in inheritance (`BasedOn`) and referenced by `CheapBuildings.csv`. The value `Building` is the root base type all others extend from. |
| `BasedOn` | String (ID) | The parent building this entry inherits from. If a column is blank, the value from the parent is used. For example, `Upgrade` extends `Building`; `Butcher` extends `Upgrade`. This creates a tree: `Building → Upgrade → Butcher`. |
| `GoldCost` | Integer | Gold paid once when the building is constructed. Blank means inherited from parent. |
| `HammersCost` | Integer | Hammers (production resource) consumed during construction. |
| `BooksCost` | Integer | Books consumed during construction. Blank if not required. |
| `PietyCost` | Integer | Piety consumed during construction. Blank if not required. |
| `GoldUpkeep` | Integer | Gold per turn deducted while the building exists. Blank means no upkeep (or inherited). |
| `Bonuses` | Multi-line String | Newline-separated (`\n`) list of bonuses the building provides. Each bonus follows the pattern `<amount> <resource> in <slot_type>` or `<amount> <stat_key>`. Optional condition suffix: `if <condition>`. Example: `1 Piety in Town\n2 rs_happiness if ChristianRealmOtherKingdom` |
| `Requires` | String | A token the province must have for this building to be constructible. Can be a resource type (`FlaxField`, `Vineyard`), a religion (`Christian`, `Muslim`, `Pagan`), or a geographical feature (`Rivers`, `Pirates`). Multiple requirements are separated by `\n`. |

---

### 2.2 `units.csv`

**Active file:** `units.csv` (128 unit definitions, ~129 lines)

**Purpose:** Defines every military unit type in the game, including all combat statistics, costs, upkeep, and classification. Uses the same inheritance system as `buildings.csv`: each unit extends a parent and overrides only specific values.

**Cross-file references:**
- `extend *` values are referenced by `CheapMilitia.csv` (overrides worker cost).
- `extend *` values are also referenced by `kingdoms.csv` (`Units`, `UnitsPrimary`, `UnitSecondary` columns) and `realms.csv` (`LocalUnits` column) to assign special units to specific kingdoms and provinces.
- `extend *` values map to voice-over unit groups in `Units voice lines.csv` (column `//unit_ids` row).
- Unit display names are resolved through `fr_units.csv` (French) and `languages.csv` (all other languages).
- `militaryUnitType` is a classification string the game uses for UI grouping and battle AI logic.

| Column | Type | Description |
|--------|------|-------------|
| `extend *` | String (ID) | Unique identifier for this unit. The root base type is `Unit`. |
| `based_on` | String (ID) | Parent unit this entry inherits from. Self-referential within this file. |
| `name` | String | Display name of the unit as shown in-game. |
| `militaryUnitType` | String | Broad classification used for UI categorization and AI. Values include `Light Infantry`, `Heavy Cavalry`, `Light Ranged Troops`, `Siege Equipment`, etc. |
| `cost_Gold` | Integer | One-time gold recruitment cost. |
| `cost_Workers` | Integer | Workers consumed on recruitment (format: `N Workers`). |
| `cost_Food` | Integer | Food consumed on recruitment (format: `N Food`). |
| `cost_Levy` | Integer | Levy points consumed on recruitment (format: `N Levy`). |
| `cost_Piety` | Integer | Piety consumed on recruitment. Blank if not required. |
| `cost_merc_Gold` | Integer | Gold cost when recruiting as mercenaries (format: `N Gold`). |
| `upkeep_Gold` | Integer | Gold per turn while unit is active. |
| `upkeep_Food` | Integer | Food per turn while unit is active (format: `N Food`). |
| `upkeep_merc_Gold` | Integer | Gold upkeep per turn when unit is a mercenary (format: `N Gold`). |
| `upkeep_merc_Food` | Integer | Food upkeep per turn when unit is a mercenary. |
| `size` | Integer | Number of soldiers in the unit. Affects how much damage the unit can absorb before routing. |
| `tier` | Integer (0–2) | Unit tier. `0` = militia, `1` = standard, `2` = elite/special. |
| `siege_strength` | Float | Damage output during siege operations (attacking walls, gates, towers). |
| `defense` | Float | Defensive rating against melee attacks. Higher values reduce incoming damage. |
| `CTH` | Float | Chance to Hit — base probability of landing a melee attack. |
| `CTH_shoot_mod` | Float | *Read-only value*. *CTH_shoot_mod* is a multiplier used to calculate the ranged attack of a unit based on its melee attack (CTH). The formula is `ranged attack = CTH * CTH_shoot_mod`. *CTH_shoot_mod* default value is `2.5` |
| `CTH_cavalry_mod` | Float | Modifier to CTH when this unit faces cavalry. . `chance_to_shock` | Float | Probability of causing a morale shock to the enemy on a successful hit. |
| `resilience` | Float | Morale resistance — how well the unit withstands shock and routing pressure. |
| `trample_chance` | Float | Cavalry only. Probability of trampling enemies underfoot during a charge. |
| `attack_interval` | Float | Time in seconds between melee attacks. Lower is faster. |
| `shoot_interval` | Float | Time in seconds between ranged shots. Blank for melee-only units. |
| `attack_range` | Float | Maximum melee engagement distance. |
| `salvo_capacity` | Float | Maximum number of projectiles in a ranged salvo. |
| `salvo_def` | String | Defines the type of ranged salvo (e.g., `ArrowSalvo`). Corresponds to an internal projectile definition. |
| `friendly_fire_mod` | Float | Multiplier for friendly fire damage inflicted by ranged attacks. |
| `move_speed` | Float | Base walking speed on the battlefield. |
| `run_speed_mul` | Float | Multiplier applied to base speed when running. |
| `charge_speed_mul` | Float | Multiplier applied to base speed during a charge. Cavalry typically > 1. |
| `world_speed_mod` | Float | Speed modifier when moving on the campaign map. |
| `stamina_max` | Float | Total stamina pool. Stamina drains during combat and affects performance when depleted. |
| `defense_against_ranged_mod` | Float | Modifier to incoming ranged damage. Values > 1 increase resistance; < 1 decrease it. |
| `CTH_siege_vs_siege` | Float | CTH modifier specifically for siege unit vs. siege unit engagements. |
| `defeat_under_num_units_mul` | Float | Multiplier to rout threshold when unit numbers fall below a critical level. |

---

### 2.3 `kingdoms.csv`

**Active file:** `kingdoms.csv` (~10 rows in the trimmed active version; the full historical data is in `kingdoms (un'altra copia).csv` with 360 rows).

**Purpose:** Defines each playable and AI kingdom: its default culture, religion, government type, starting ruler, starting family members, vassal relationships, nobility tier, and which special units it can field. Rows are grouped by kingdom using a period-suffixed naming convention: the base row (e.g., `Bulgaria`) sets the kingdom's constant properties, while period rows (`Bulgaria.E`, `Bulgaria.M`, `Bulgaria.L`) define the state of that kingdom at each of the three historical start dates (Early, Middle, Late).

**Cross-file references:**
- `NamePeriod` values are used as keys by `realms.csv` (`Owner` column) and `CoA.csv` (`NamePeriod` column).
- `Culture` references culture IDs defined in `cultures.csv`.
- `Religion` references religion tokens used throughout the system (e.g., `Orthodox`, `Catholic`, `Sunni`, `Pagan`). The `.I`, `.E`, `.H`, `.C` suffixes denote sub-variants (Iconoclast, Eastern, etc.).
- `CapitalProvince` references realm names from `realms.csv` (`Name` column).
- `KingNameAge` and `QueenNameAge` reference character name IDs from `character_names.csv`, using the format `Name.Age` or `Name.Index.Age`.
- `KingClass` and implicitly the child columns reference knight class IDs: `Marshal`, `Merchant`, `Spy`, `Cleric`, `Diplomat`.
- `Units`, `UnitsPrimary`, `UnitSecondary` reference `extend *` IDs from `units.csv`.
- `VassalOf` references another `NamePeriod` base ID within this file.
- `CoA` references CoA index numbers in `CoA.csv`.
- Kingdom display names and descriptions are in `kingdom_descriptions.csv`. Nobility title systems for each kingdom are in the `de_titles.csv`, `pl_titles.csv`, and related per-language title files.

| Column | Type | Description |
|--------|------|-------------|
| `NamePeriod` | String (ID) | Kingdom identifier. Base entries use the plain name (e.g., `Bulgaria`). Period-specific entries append `.E` (Early, ~1000 AD), `.M` (Middle, ~1200 AD), or `.L` (Late, ~1350 AD). |
| `Culture` | String (ID) | The kingdom's primary culture. References `culture_*` in `cultures.csv`. Blank on period rows means "inherit from base row". |
| `Religion` | String | The kingdom's religion. Values: `Catholic`, `Orthodox`, `Orthodox.I` (with iconoclast variant), `Sunni`, `Shia`, `Muslim.H`, `Christian.H`, `Pagan`. |
| `GovernType` | String | Government form. Values: blank/`-` (default Monarchy), `Republic`, `Order`, `Papacy`, `Caliphate`, `Confederacy`, `Judicate`, `Sharifate`. |
| `CapitalProvince` | String (ID) | The realm that serves as this kingdom's capital. References `Name` in `realms.csv`. |
| `KingNameAge` | String | Starting ruler's name and age. Format: `Name.Index.AgeGroup` or `Name.AgeGroup`. Age group codes: `Y` (Young), `A` (Adult), `O` (Old), `C` (Child), `I` (Infant), `J` (Juvenile). Index is a disambiguation number when multiple characters share the same name. |
| `KingClass` | String | Starting ruler's knight class. Values: `Marshal`, `Merchant`, `Spy`, `Cleric`, `Diplomat`. |
| `QueenNameAge` | String | Starting queen/consort. Same format as `KingNameAge`. May include `.KingdomOfOrigin` suffix (e.g., `Mary.Y.Hungary`). |
| `Child_1_NameSexAge` through `Child_4_NameSexAge` | String | Up to four children. Format: `Name.Sex.AgeGroup`. Sex codes: `.m` (male), `.f` (female). |
| `VassalOf` | String (ID) | If set, this kingdom starts as a vassal of the named kingdom. References a base `NamePeriod` ID in this same file. |
| `Traditions` | String | Kingdom traditions. Format: `key.Level` where Level is a numeric tier. Multiple traditions separated by `/`. |
| `NobilityTitles` | String | Key referencing the set of nobility titles used for this kingdom (e.g., `Western`, `Byzantine`, `Arabic`). |
| `NobilityLevel` | String | The top-tier nobility level. Values: `Kingdom`, `Empire`, `Empire.H`, `Kingdom.H`, `Duchy`. |
| `NobilityNames` | String | Key for the name pool used when generating noble names for this kingdom. |
| `MapColor` | Integer | Index into the colour palette used to render this kingdom on the campaign map. |
| `UnitsPrimary` | String (ID) | Primary unit set token. References `UnitsSet` token values (e.g., `ArabicUnitSet`, `SteppeUnitSet`). |
| `UnitSecondary` | String (ID) | Secondary unit set token. |
| `CoA` | Integer | Coat of Arms index. References `CoA` column in `CoA.csv`. |
| `UnitsSet` | String | Named unit set this kingdom uses for its standard roster (e.g., `WesternUnitSet`, `SaracenUnitSet`, `EastSlavicUnitSet`). |
| `Units` | String (ID list) | Special unique units available to this kingdom. Multiple units separated by `/`. Each value references `extend *` in `units.csv`. |

---

### 2.4 `realms.csv`

**Active file:** `realms.csv` (small, ~42 lines; the full world data is in `realms (copia).csv` with ~1213 province rows).

**Purpose:** Defines every province (realm) in the game world — its default owner, culture, religion, local population composition, and any unique units that spawn there. Like `kingdoms.csv`, period rows (`.E`, `.M`, `.L`) define state at each start date.

**Cross-file references:**
- `Name` is referenced by `kingdoms.csv` (`CapitalProvince`), `geo_features.csv` (`extend Realms.test.*`), and internally by `Owner`.
- `Owner` references kingdom base IDs from `kingdoms.csv` (`NamePeriod`).
- `Culture` references `culture_*` IDs from `cultures.csv`.
- `Religion` uses the same religion token system as `kingdoms.csv`.
- `CoreOf` and `HistoricalOf` reference kingdom base IDs from `kingdoms.csv`.
- `CoA` references the CoA index from `CoA.csv`.
- `LocalUnits` references `extend *` IDs from `units.csv`.
- Province display names are in `teritories.csv` and its language variants.

| Column | Type | Description |
|--------|------|-------------|
| `Name` | String (ID) | Unique province identifier. Base entries are plain names (e.g., `Karvuna`). Period entries append `.E`, `.M`, `.L`. |
| `TownName` | String | The name of the main settlement (town) within this province. Set on the base row only. |
| `Owner` | String (ID) | The kingdom that controls this province at this start date. References base `NamePeriod` from `kingdoms.csv`. `-` means no owner (independent or rebel). |
| `PopulationPerc` | String | Population composition override. Format: `Kingdom.Percentage` (e.g., `Bulgaria.80` means 80% Bulgarian population). `-` means default. |
| `Culture` | String (ID) | The dominant culture of this province. References `culture_*` in `cultures.csv`. |
| `Religion` | String | The dominant religion of this province. Same token system as `kingdoms.csv`. |
| `CoreOf` | String (ID) | The kingdom for which this province is a "core" territory (has a historical claim). References `kingdoms.csv`. |
| `HistoricalOf` | String (ID) | The kingdom for which this province is a historical territory (softer than a core claim). References `kingdoms.csv`. |
| `CoA` | Integer | Province-level coat of arms index. References `CoA.csv`. |
| `Fame` | Integer | Starting fame value of this province. Affects prestige and desirability. |
| `LocalUnits` | String (ID) | Special unit that can be recruited in this province. References `extend *` in `units.csv`. `-` means no special unit. |

---

### 2.5 `cultures.csv`

**Active file:** `cultures.csv` (120 culture entries)

**Purpose:** The master list of all culture IDs in the game. This is a minimal table that simply declares that a culture key exists and provides its base (default/English) display name. All richer grammatical localisation per language lives in the `[lang]_cultures.csv` files.

**Cross-file references:**
- `culture_*` values are referenced by `kingdoms.csv` (`Culture`) and `realms.csv` (`Culture`).
- Each entry here has corresponding rows in every `[lang]_cultures.csv` file.

| Column | Type | Description |
|--------|------|-------------|
| `culture_*` | String (ID) | Unique culture identifier. This is the key used everywhere else in the game data. |
| `base` | String | Default (base language) display name for this culture. |

---

### 2.6 `factions.csv`

**Active file:** `factions.csv` (8 faction entries)

**Purpose:** Defines the named non-player factions that exist in the game world (rebels, mercenaries, exiles, crusaders, etc.), plus their grammatical forms for the default/English language. Richer grammatical localisation lives in the `[lang]_factions.csv` files.

**Cross-file references:**
- `tn_*` values (with the `tn_` prefix stripped) are referenced throughout game logic as faction identifiers.
- Each entry has corresponding rows in every `[lang]_factions.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `tn_*` | String (ID) | Unique faction identifier. The `tn_` prefix is used by the localisation system; the bare name (e.g., `ExileFaction`) is used in game logic. |
| `base` | String | Default display name (plural form). |
| `ian` | String | Adjective/demonym form (e.g., `Mercenary` from `Mercenaries`). Used in phrases like "a Mercenary army". |
| `anian` | String | Extended adjective form. Used in specific grammatical constructions. |
| `s` | String | Possessive form. Template `{base}'s` by default. |

---

### 2.7 `CoA.csv`

**Active file:** `CoA.csv` (3 rows — this is a small override file; the full CoA data is in `CoA (copia).csv`)

**Purpose:** Assigns Coat of Arms (CoA) artwork indices and colour palette entries to specific kingdoms. The CoA index maps to a sprite sheet of heraldic designs bundled with the game's assets. This file also controls the colour used to represent the kingdom on the map and on army banners.

**Cross-file references:**
- `NamePeriod` references kingdom base IDs from `kingdoms.csv`.
- `CoA` integer index maps to internal artwork assets (not CSV-editable).
- `map_color`, `primary_army_color`, `secondary_army_color` are palette indices into an internal colour table.

| Column | Type | Description |
|--------|------|-------------|
| `NamePeriod` | String (ID) | Kingdom identifier. References the base `NamePeriod` (no period suffix) from `kingdoms.csv`. |
| `CoA` | Integer | Index of the coat of arms artwork to use for this kingdom. |
| `map_color` | Integer | Palette index for the colour used to shade this kingdom's territory on the campaign map. |
| `primary_army_color` | Integer | Primary banner/army colour palette index. |
| `secondary_army_color` | Integer | Secondary banner/army colour palette index. |

---

### 2.8 `papacy.csv`

**Active file:** `papacy.csv` (3 rows — one per start date period)

**Purpose:** Defines the starting Pope and College of Cardinals for each of the three historical start dates. Character names follow the same `Name.Number` or `Name.KingdomOrigin` convention used in `kingdoms.csv`.

**Cross-file references:**
- `Pope` and `Cardinal_N` values reference character name IDs from `character_names.csv`.
- The `.KingdomOrigin` suffix (e.g., `Kuno.Germany`) cross-references a kingdom base ID from `kingdoms.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `Period` | String | Start date period. Values: `E` (Early ~1000 AD), `M` (Middle ~1200 AD), `L` (Late ~1350 AD). |
| `Pope` | String | Name and optional index of the starting Pope. Format: `Name.Number` (e.g., `Honorius.3`). |
| `Cardinal_1` through `Cardinal_8` | String | Names of the starting Cardinals. Format: `Name.KingdomOrigin` or plain `Name`. Up to 8 cardinals; blank if fewer exist for this period. |

---

### 2.9 `geo_features.csv`

**Active file:** `geo_features.csv` (10 rows)

**Purpose:** Assigns specific geographic features to individual provinces. These features affect battle terrain, economy, and trade route generation. Only provinces with non-default feature values need an entry — this file is an override table.

**Cross-file references:**
- `extend Realms.test.*` references province `Name` values from `realms.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `extend Realms.test.*` | String (ID) | Province identifier. References `Name` in `realms.csv`. |
| `has_rivers` | Boolean | Whether the province contains rivers. Affects battlefield terrain generation. |
| `has_river_city` | Boolean | Whether the main settlement is situated on a river. |
| `has_coastal_city` | Boolean | Whether the main settlement is a coastal city. |
| `has_mountains` | Boolean | Whether the province has mountainous terrain. Affects movement and battle generation. |
| `seas_count` | Integer | Number of sea zones adjacent to this province. Relevant for naval access. |
| `has_distant_port` | Boolean | Whether the province has a port not directly adjacent to a sea zone (distant coastal access). |

---

### 2.10 `properties.csv`

**Active file:** `properties.csv` (755 rows)

**Purpose:** The voice-over event scheduling table. For every possible in-game event (e.g., a siege starting, a battle being won, a king being captured), this file defines which type of VO speaker handles it, under what conditions, with what priority, cooldown, and timeout. It is the bridge between game events and the audio/voice system. The game uses this table to decide *whether* to play a voice line and *which* voice line queue to draw from.

**Cross-file references:**
- `event` values are the primary key linking this file to `Narrator voice lines.csv`, `Knights voice lines.csv`, and `Units voice lines.csv` (all of which also have an `event` column).
- `fmod_event` is a path into the FMOD audio middleware event system (e.g., `event:/voice/character`). This is not a CSV-editable asset.
- `condition` values overlap with the `condition` column in the voice line files.

| Column | Type | Description |
|--------|------|-------------|
| `type` | String | Which speaker type handles this event. Values: `Character` (knight voice lines), `Narrator` (narrator voice lines), `Unit` (unit voice lines). |
| `event` | String (ID) | The in-game event identifier. Must match an `event` value in the corresponding voice lines file. |
| `condition` | String | Optional sub-condition that narrows when this entry fires. Blank means "all conditions". Examples: `king`, `not_king`, `our_town`, `ally_army`. |
| `cooldown` | Float | Minimum time in seconds that must pass before this event can fire again. Prevents spamming repeated lines. |
| `que_clearing` | Boolean | If `True`, this event clears the current voice queue before playing, interrupting any queued lines. |
| `fmod_event` | String | Path to the FMOD audio event group that handles playback routing. |
| `//priority text` | String | Human-readable label for the priority level (e.g., `low_priority`, `very_high_priority`). Comment column, not read by the game. |
| `priority` | Integer | Numeric priority (100–500). Higher values override lower-priority queued lines. |
| `bv_priority` | Integer | Battle-view specific priority override. Blank if same as `priority`. |
| `timeout` | Float | Maximum time in seconds the game will wait for this line to play. If the line hasn't played within this window, it is discarded. |
| `skip_queue` | Boolean | If `True`, this event skips the queue and plays immediately regardless of other queued lines. Currently always `False`. |
| `allow_default_condition` | String | Controls fallback behaviour when no condition-specific line matches. `Always` = always allow default; `WhenAllConditionsFail` = only fall back if no specific condition matched. |
| `//comments` | String | Developer notes. Comment column, not read by the game. |

---

### 2.11 `CheapBuildings.csv`

**Active file:** `CheapBuildings.csv` (2 rows)

**Purpose:** A small override table that reduces the gold cost for specific buildings below what `buildings.csv` defines. This is likely applied under specific game conditions (e.g., a tradition, technology, or difficulty setting) rather than as a permanent override.

**Cross-file references:**
- `extend *` references `extend *` IDs from `buildings.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `extend *` | String (ID) | Building identifier to override. References `extend *` in `buildings.csv`. |
| `GoldCost` | Integer | Reduced gold cost to apply. |

---

### 2.12 `CheapMilitia.csv`

**Active file:** `CheapMilitia.csv` (2 rows)

**Purpose:** A small override table that reduces the worker cost for militia units. Same purpose as `CheapBuildings.csv` but for units.

**Cross-file references:**
- `extend *` references `extend *` IDs from `units.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `extend *` | String (ID) | Unit identifier to override. References `extend *` in `units.csv`. |
| `cost_Workers` | String | Reduced worker cost (format: `N Workers`). |

---

## 3. Localisation & Text Files

### 3.1 `languages.csv`

**Active file:** `languages.csv` (~15,462 rows — the master string table for the entire game UI)

**Purpose:** This is the single largest localisation file and contains every UI string, tooltip, event text, and label used in the game — in all 14 supported languages at once. It is a flat key-value table where each row is one string key and each column after the first is one language's translation. When the game needs to display any text, it looks up the relevant ID in this file.

**Note on `languages_proofread.csv`:** A smaller companion file (`languages_proofread.csv`, ~1,697 KB) appears to be a curated subset of strings undergoing quality review. It has the same structure.

| Column | Type | Description |
|--------|------|-------------|
| `ID` | String (key) | Unique string identifier used in game code and other CSV files to look up display text. Uses dot-notation namespacing (e.g., `Battle.caption.OpenField`, `Title.buttons.continue`). |
| `en` | String | English translation. |
| `fr` | String | French translation. |
| `de` | String | German translation. |
| `es` | String | Spanish translation. |
| `pl` | String | Polish translation. |
| `ru` | String | Russian translation. |
| `ko` | String | Korean translation. |
| `ja` | String | Japanese translation. |
| `zh` | String | Simplified Chinese translation. |
| `zh_tw` | String | Traditional Chinese translation. |
| `tr` | String | Turkish translation. |
| `hi` | String | Hindi translation. |
| `ind` | String | Indonesian translation. |

---

### 3.2 `preload.csv` and language variants

**Active files:** `preload.csv` (base/English), plus per-language split files listed below.

**Purpose:** Contains the strings that must be loaded before the main game data (hence "preload") — specifically the main menu button labels, loading screen messages, and error dialogs. Split into numbered sub-files to allow the game engine to load them in stages.

**File variants and their keys:**

| File pattern | Keys covered |
|---|---|
| `preload.csv` / `[lang]_preload.csv` | Main menu buttons only (Continue, New Campaign, Load, Multiplayer, Settings, Credits, Quit, Sign In, Sign Out) |
| `[lang]_preload;2.csv` | Copyright line + all loading screen progress messages |
| `[lang]_preload;8.csv` | Corrupted data error dialog (localised) |
| `[lang]_preload;9.csv` | Corrupted data error dialog (English fallback) |

**Languages with preload files:** `de`, `en`, `es`, `fr` (copia = backup), `hi`, `ind`, `ja`, `ko`, `pl`, `ru`, `tr`, `zh`, `zh_tw`, and the unlanguaged `gbrsh` (appears to be a stub/test locale).

**Column structure (all preload files share this):**

| Column | Type | Description |
|--------|------|-------------|
| `*` | String (key) | String identifier. References IDs in `languages.csv`. |
| `base` | String | The translated string value for this language. |
| `Unnamed: 2` | String | Developer comments (prefixed with `//`). Not read by the game. |

---

### 3.3 `teritories.csv` and language variants

**Active files:** `teritories.csv` (base/English, ~670 rows), plus per-language variants.

**Purpose:** Provides the display names and grammatical forms of every province/territory name. The base file `teritories.csv` defines the English names. Each language variant provides the same province names declined according to that language's grammar.

**Cross-file references:**
- The `tn_*` key (with prefix stripped: e.g., `tn_Aarhus` → `Aarhus`) corresponds to province `Name` values in `realms.csv`. The `tn_` prefix is a namespace marker used by the localisation system.

**Column structure (base file and simple-grammar language variants — en_, es_, ind_, ko_, ja_, zh_, zh_tw_):**

| Column | Type | Description |
|--------|------|-------------|
| `tn_*` / `*` | String (key) | Province identifier prefixed with `tn_`. Corresponds to `Name` in `realms.csv`. |
| `base` | String | The province's base display name in this language. |
| `ian` | String | Adjective/demonym form (e.g., `Aarhusian`). Used in phrases like "the Aarhusian border". |
| `s` | String | Possessive form (e.g., `Aarhus'`). |
| `anian` | String | Extended adjective form (e.g., `an Aarhusian`). |
| `ians` | String | Plural demonym (e.g., `Aarhusians`). |

Languages with additional grammatical cases (de_, ru_, pl_, fr_, hi_, tr_) have extra columns for genitive, dative, accusative, instrumental, prepositional, etc., following the same patterns described for those languages in sections 3.12–3.19.

**The `;N` split variants** (e.g., `de_teritories;2.csv`, `de_teritories;11.csv`) cover specific subsets of province names and follow the same column structure.

---

### 3.4 `character_names.csv` and language variants

**Active file:** `character_names.csv` (base/English, ~4,783 rows), plus per-language variants.

**Purpose:** The name pool for all characters generated by the game (rulers, nobles, knights, family members). Each entry defines a name and its possessive form. Names are tagged by gender using the `.m` and `.f` suffixes in the key, and by cultural origin via the name itself. The game draws from this pool when generating new characters.

**Cross-file references:**
- `cn_*` keys (with prefix stripped) are directly referenced by `kingdoms.csv` in the `KingNameAge`, `QueenNameAge`, and `Child_N_NameSexAge` columns.
- The `;5.csv` and `;6.csv` split variants cover additional name sets (e.g., extended pools or special names).

**Column structure:**

| Column | Type | Description |
|--------|------|-------------|
| `cn_*` | String (key) | Character name identifier. The `cn_` prefix is the localisation namespace. The suffix `.m` indicates male, `.f` female. Example: `_Alaq.m` for the male name "Alaq". |
| `base` | String | The name as displayed in-game. |
| `s` | String | Possessive form (e.g., `{base}'s` by default, or an explicit form for irregular possessives). |

Russian (`ru_character_names.csv`) and other highly inflected languages add multiple case columns: `base_sub_gen`, `base_sub_dat`, `base_sub_acc`, `base_sub_ins`, `base_sub_pre` for genitive, dative, accusative, instrumental, and prepositional cases respectively.

---

### 3.5 Cultures localisation

**Active files:** `[lang]_cultures.csv` for each language. Available for: `de`, `en`, `es`, `fr`, `hi`, `ind`, `ja`, `ko`, `pl`, `ru`, `tr`, `zh`, `zh_tw`.

**Purpose:** Provides culture display names in each language. The base `cultures.csv` already contains the English names; these files override or supplement for each language.

**Cross-file references:**
- `*` keys (with optional `culture_` prefix stripped) correspond to `culture_*` in `cultures.csv`.
- Split variant `;4.csv` (e.g., `de_cultures;4.csv`) covers additional culture keys.

**Column structure (simple grammar languages — en_, ko_, ja_, zh_, zh_tw_):**

| Column | Type | Description |
|--------|------|-------------|
| `*` | String (key) | Culture identifier. |
| `base` | String | Translated culture name. |

Inflected languages (de_, ru_, pl_, fr_, tr_, hi_) add grammatical case columns following the same patterns as other localisation files for those languages.

---

### 3.6 Factions localisation

**Active files:** `[lang]_factions.csv` for each language. Available for: `de`, `en`, `es`, `hi`, `ind`, `ja`, `ko`, `pl`, `ru`, `tr`, `zh`, `zh_tw`, `fr` (implicit via languages.csv).

**Purpose:** Provides faction names in each language with all required grammatical forms.

**Cross-file references:**
- `tn_*` keys correspond to `tn_*` in `factions.csv`.

**Column structure varies by language.** Simple languages (en_) use `base`, `ian`, `anian`, `s`. German (`de_factions.csv`) is the most complete, providing full declension: `base`, `nom` (nominative), `gen` (genitive), `dat` (dative), `akk` (accusative), `movto`, `prepin`, `prepto`, `prepby`, `prepfrom`.

---

### 3.7 `kingdom_descriptions.csv`

**Active file:** `kingdom_descriptions.csv` (~190 rows)

**Purpose:** Contains the lore/history text displayed in the kingdom selection screen for each playable kingdom. One row per kingdom, in English only. This file is not split by language — all translations of kingdom descriptions appear to reside in `languages.csv`.

**Cross-file references:**
- `*.description` key (with suffix stripped) corresponds to kingdom base IDs from `kingdoms.csv` (`NamePeriod`).

| Column | Type | Description |
|--------|------|-------------|
| `*.description` | String (key) | Kingdom identifier with `.description` suffix appended. Corresponds to a `NamePeriod` base value in `kingdoms.csv`. |
| `base` | String | The lore/history text for this kingdom. Plain prose, no markup. |
| `Unnamed: 2` | String | Unused trailing column. Always blank. |

---

### 3.8 `Vassalage.csv` and language variants

**Active file:** `Vassalage.csv` (base/English), plus `[lang]_vassalage;12.csv` variants.

**Purpose:** Defines the display names and tooltip descriptions for the different types of vassal relationships available in the game.

**Note on the `;12` suffix:** This is a split file number, meaning the game loads this as segment 12 of the overall localisation bundle for that language.

**Cross-file references:**
- Keys like `VassalVassalage.name`, `ScuttageVassalage.tooltip` etc. are referenced by `languages.csv` and the game's diplomacy UI.

| Column | Type | Description |
|--------|------|-------------|
| `*` | String (key) | String key for this vassalage entry. Pattern: `[Type]Vassalage.name` or `[Type]Vassalage.tooltip`. Types: `Vassal`, `Scuttage`, `March`, `SacredLand`. |
| `base` | String | The translated name or tooltip text. |

---

### 3.9 `UnionQuess.csv` and language variants

**Active file:** `UnionQuess.csv` (base/English), plus `[lang]_unionquess;11.csv` variants.

**Purpose:** Contains the UI strings for the "Unification Quests" system — the special objectives a kingdom must complete to unify a historical region. The `;11` split number applies here as well.

| Column | Type | Description |
|--------|------|-------------|
| `*` | String (key) | String key for this UI element. Keys: `UnionQuest.tooltip`, `UnionQuest.WindowTitle`, `UnionQuest.NoExist`, `UnionQuest.Religion`, `UnionQuest.Conquer`, `UnionQuest.Conditions`. |
| `base` | String | The translated UI string. |

---

### 3.10 Building name localisation files

**Active files:**
- `fr_building_names.csv` — French names for base buildings
- `fr_building upgrades.csv` — French names for building upgrades
- `ru_buildings.csv` — Russian building names
- `ru_building upgrades.csv` — Russian building upgrade names

**Purpose:** Provides building display names with grammatical forms required by each language. English and most other languages resolve building names through `languages.csv`.

**Cross-file references:**
- `*.name` key (stripped of suffix) corresponds to `extend *` values in `buildings.csv`.

**Column structure (French):**

| Column | Type | Description |
|--------|------|-------------|
| `*.name` / `*` | String (key) | Building ID with `.name` suffix. Corresponds to `extend *` in `buildings.csv`. |
| `base` | String | Base display name. |
| `indef` | String | Indefinite singular article form (e.g., `un entrepôt`). |
| `indefpl` | String | Indefinite plural form (e.g., `des entrepôts`). |
| `def` | String | Definite article form (e.g., `les entrepôts`). |
| `de` | String | Partitive/`de` form (e.g., `d'entrepôts`). |
| `s` | String | Possessive/genitive form (French building_names only). |

Russian adds standard Russian case columns: `lower`, `base_sub_gen`, `base_sub_dat`, `base_sub_acc`, `base_sub_ins`, `base_sub_pre`.

---

### 3.11 Unit name localisation files

**Active files:** `fr_units.csv` (French), `ru_units.csv` is not present — Russian unit names are in `languages.csv`.

**Purpose:** Provides translated unit display names. Only French has a dedicated file; other languages use `languages.csv`.

**Cross-file references:**
- `*` key corresponds to `extend *` values in `units.csv` with `.name` appended.

| Column | Type | Description |
|--------|------|-------------|
| `*` | String (key) | Unit ID with `.name` suffix. Corresponds to `extend *` in `units.csv`. |
| `base` | String | Translated unit name. |

---

### 3.12 Knight class localisation files

**Active files:** `fr_classes.csv`, `ru_classes.csv`, `de_classes.csv`, `ru_classes.csv`.

**Purpose:** Provides grammatical forms for the knight class names (Marshal, Merchant, Spy, Cleric, Diplomat, Prince). These names appear throughout the game UI in different grammatical contexts.

**Cross-file references:**
- Keys like `Marshal.name`, `Merchant.name` correspond to knight class identifiers used in `kingdoms.csv` (`KingClass` column) and throughout the game logic.
- The `Cleric.name` entry uses a template referencing `kingdom.cleric_title`, which means the displayed title is dynamically replaced per kingdom (e.g., Bishop, Imam, Shaman).

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Class key (e.g., `Marshal.name`). |
| `base` | Base display name. |
| `:pl` | Plural form. |
| `:s` | Possessive/genitive singular. |
| `:à` | Dative (`à` + name) form. |
| `:def` | Definite article form. |
| `:indef` | Indefinite article form. |
| `:multiple` | Multiple/collective form. |

Russian adds: `lower`, `base_sub_gen`, `base_sub_dat`, `base_sub_acc`, `base_sub_ins`, `base_sub_pre`.
German (`de_classes.csv`) adds: `defplural`, `nom`, `ourakk`, `ourdat`, `ourgen`, `akk`.

---

### 3.13 Goods localisation files

**Active files:** `fr_goods.csv`, `ru_goods.csv`.

**Purpose:** Provides grammatical forms for trade goods names (Ale, Amber, etc.).

**Cross-file references:**
- `*` keys with `.name` suffix correspond to trade good identifiers used in game logic and `languages.csv`.

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Good key (e.g., `Ale.name`). |
| `base` | Display name. |
| `:def` | Definite article form. |
| `:s` | Possessive/genitive form. |

Russian adds full case declension: `lower`, `base_sub_gen`, `base_sub_dat`, `base_sub_acc`, `base_sub_ins`, `base_sub_pre`.

---

### 3.14 Opinion localisation files

**Active files:** `fr_opinions.csv`, `ru_opinions.csv`, `de_opinions.csv`.

**Purpose:** Provides grammatical forms for the opinion group names (Nobility, Army, Merchants, Clergy, Peasantry). These are the social strata whose opinion of the player is tracked in-game.

**Cross-file references:**
- Keys like `NobilityOpinion.name`, `ArmyOpinion.name` are referenced throughout the game's opinion/happiness UI.

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Opinion key (e.g., `NobilityOpinion.name`). |
| `base` | Base display name. |
| `def` | Definite article form. |
| `s` | Possessive form. |

German adds: `gen`, `genour`. Russian adds full case declension.

---

### 3.15 Religion localisation files

**Active files:** `fr_religions.csv`, `ru_religions.csv`, `de_religions.csv`, plus `[lang]_cultures` files which sometimes include religion strings.

**Purpose:** Provides grammatical forms for religion names (Christian, Catholic, Orthodox, Sunni, Shia, Pagan, Muslim). Religions appear throughout the game in many grammatical contexts.

**Cross-file references:**
- Keys like `Christian.name`, `Catholic.name` correspond to religion token values used in `kingdoms.csv`, `realms.csv`, and `buildings.csv` (`Requires` column).

**Column structure (German — most complete):**

| Column | Description |
|--------|-------------|
| `*` | Religion key (e.g., `Catholic.name`). |
| `base` | Base adjective form (e.g., `Katholisch`). |
| `lower` | Lowercase base form. |
| `ismakk` | Accusative form of the `-ism` noun (e.g., `den Katholizismus`). |
| `ismgen` | Genitive form of the `-ism` noun. |
| `ismprepto` | Dative prepositional form. |
| `ism` | Base `-ism` noun (the religion as a concept, e.g., `Katholizismus`). |
| `ans` | Plural demonym (e.g., `Katholiken`). |
| `they` | Third-person plural pronoun variant (Orthodox only). |

French adds: `ism`, `ismdef`, `ans`, `à`, `s`. Russian provides the most extensive set, including full male/female/plural declension across all cases.

---

### 3.16 Religious titles localisation files

**Active files:** `ru_religious titles.csv`, `de_religious titles.csv`, `pl_character_title_and_name.csv` (Polish overlaps with this), `de_character title and name.csv`, `ru_character title and name.csv`.

**Purpose:** Provides the titles given to clergy based on their religion (e.g., Bishop for Catholic, Patriarch for Orthodox, Imam for Sunni, Shaman for Pagan). These titles substitute for the generic "Cleric" class name depending on kingdom religion.

**Cross-file references:**
- Keys like `Religion.titles.Cleric`, `Catholic.titles.Cleric` are referenced by `de_classes.csv` in the `Cleric.name` entry template.

**Column structure (Russian — most complete):**

| Column | Description |
|--------|-------------|
| `*` | Title key (e.g., `Catholic.titles.Cleric`). |
| `base` | Title display name. |
| `lower` | Lowercase form. |
| `base_sub_gen` through `base_sub_pre` | Full Russian case declension. |

---

### 3.17 Settlement type localisation files

**Active files:** `fr_settlement types.csv`, `ru_settlement types.csv`, `de_settlements.csv`.

**Purpose:** Provides grammatical forms for settlement slot type names (Village, Farm, Town, Monastery, Mosque, Keep, Mine, etc.). These names appear in building UI and province management screens.

**Cross-file references:**
- Keys like `Village.name`, `Farm.name` correspond to settlement slot type identifiers used in `buildings.csv` (`Bonuses` column, e.g., `in Town`, `in Village`).

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Settlement type key. |
| `base` | Display name. |
| `multiple` | Plural form. |
| `indefpl` | Indefinite plural. |
| `def` | Definite singular. |
| `s` | Possessive. |
| `pl` | Plain plural. |
| `de` | Partitive `de` form. |

German (`de_settlements.csv`) provides: `gen`, `akk`, `anom`, `aakk`, `prepin`, `nom`, `datour`, `nomour`, `akkour`, `multiple`, `lower`. Russian adds full case declension.

---

### 3.18 Kingdom type localisation files

**Active files:** `fr_kingdom types.csv`, `ru_kingdom types.csv`, `de_kingdomtypes.csv`.

**Purpose:** Provides grammatical forms for the kingdom tier names (Duchy, Kingdom, Empire, etc.). These correspond to the `NobilityLevel` values in `kingdoms.csv`.

**Cross-file references:**
- Keys like `Nobility.Duchy`, `Nobility.Kingdom` correspond to values in the `NobilityLevel` column of `kingdoms.csv`.

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Kingdom type key. |
| `base` | Display name. |
| `s` | Possessive/genitive form. |
| `son` | Possessive pronoun form (`son Duché`). |
| `def` | Definite article form. |

Russian and German add full case declension.

---

### 3.19 Nobility title localisation files

**Active files:** `fr_nobility titles.csv`, `ru_nobility titles.csv`. Extended variants: `de_titles.csv`, `pl_titles.csv`, `ru_nobility titles;10.csv`, `de_titles;10.csv`, `pl_titles;10.csv`.

**Purpose:** Provides grammatical forms for the individual nobility rank titles (Knight, Baron, Count, Duke, King, Emperor, etc.). Different kingdoms can use different title sets based on their `NobilityTitles` value in `kingdoms.csv`.

**Cross-file references:**
- Keys like `Nobility.Knight`, `Nobility.Prince` correspond to nobility rank identifiers. The `NobilityTitles` column in `kingdoms.csv` selects which title set a kingdom uses.

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Nobility title key. |
| `base` | Display name. |
| `s` | Possessive/genitive form. |
| `def` | Definite article form. |
| `à` | Dative (`à` + title) form. |

Russian adds full case declension. German (`de_titles.csv`) provides extensive declension including definite/indefinite article forms and possessive constructions.

---

### 3.20 Character age localisation files

**Active file:** `fr_character_ages.csv`.

**Purpose:** Provides French translations for character age group labels (Infant, Child, Juvenile, Young, Adult, Old). These labels appear in character panels and tooltips.

**Cross-file references:**
- Keys like `Character.age.Infant` correspond to the age group codes (`I`, `C`, `J`, `Y`, `A`, `O`) used in `kingdoms.csv` character columns.

| Column | Description |
|--------|-------------|
| `*` | Age group key (e.g., `Character.age.Infant`). |
| `base` | Translated age label. |

---

### 3.21 Fantasy name localisation files

**Active file:** `ru_fantasy names.csv`.

**Purpose:** Provides Russian-language grammatical forms for the "fantasy" place names used in the Trade Expedition system — exotic destinations that appear as flavour text during trade missions.

**Cross-file references:**
- Keys follow the pattern `TradeExpeditionStatus.fantasyNames.[Name]`. These are referenced by the trade expedition UI strings in `languages.csv`.

| Column | Description |
|--------|-------------|
| `*` | Fantasy location key. |
| `base` | Display name. |
| `base_sub_gen` through `base_sub_pre` | Full Russian case declension (genitive, dative, accusative, instrumental, prepositional). |

---

### 3.22 Inventory item localisation files

**Active files:** `fr_inventory items.csv`, `ru_inventory items.csv`.

**Purpose:** Provides grammatical forms for inventory/supply item names (e.g., Additional Troops, Food Wagon).

**Cross-file references:**
- Keys with `.name` suffix correspond to inventory item identifiers used in game logic and `languages.csv`.

**Column structure (French):**

| Column | Description |
|--------|-------------|
| `*` | Item key. |
| `base` | Display name. |
| `indef` | Indefinite singular form. |
| `indefpl` | Indefinite plural form. |

Russian adds full case declension.

---

### 3.23 Nobility/title extended localisation (de_, pl_)

**Active files:** `de_titles.csv`, `de_titles;10.csv`, `pl_titles.csv`, `pl_titles;10.csv`, `de_character title and name.csv`, `ru_character title and name.csv`, `pl_character_title_and_name.csv`.

**Purpose:** German and Polish have particularly complex grammatical requirements for nobility and character titles, requiring more case forms than French or simple languages. These files provide the full declension tables for title display. The `character title and name` files combine title and name display logic for use in character panels.

**Cross-file references:**
- Same key references as section 3.19.

Column structure follows the standard German/Polish declension pattern with `base`, `lower`, `nom`, `gen`, `dat`, `akk`, `ins`, `pre` (Polish), or `base`, `defplural`, `nom`, `ourakk`, `ourdat`, `ourgen`, `akk` (German).

---

### 3.24 `de_` miscellaneous files

These small German-language files cover specific localisation needs:

**`de_formatting.csv`**
One row. Defines locale-specific formatting rules.

| Column | Description |
|--------|-------------|
| `*` | Format key (e.g., `percent_after_number`). |
| `base` | The format value (e.g., `%` — meaning the percent sign goes after the number in German). |

**`de_preload.csv` / `;2` / `;8` / `;9`**
Covered in section 3.2.

**`de_opinions.csv`**
Covered in section 3.14.

**`de_religions.csv`**
Covered in section 3.15.

**`de_kingdomtypes.csv`**
Covered in section 3.18.

---

## 4. Voice-Over (VO) Files

### 4.1 `VO.csv`

**Active file:** `VO.csv` (~2,785 rows)

**Purpose:** The master subtitle and script database for all spoken voice lines in the game. Every line spoken by the narrator or knights has an entry here, with the spoken text in four languages (English, French, German, Simplified Chinese). This file also serves as the authoritative list of voice line IDs that the game engine maps to audio files.

**Cross-file references:**
- `id` values follow the pattern `voice_[speaker]_[event]_[sequence]`. The `[event]` segment corresponds to `event` values in `Narrator voice lines.csv` and `Knights voice lines.csv`.
- `en`/`fr`/`de`/`zh` text columns represent the spoken/subtitled script. The `id` field maps to actual audio files in the game's audio assets (not CSV-editable).

| Column | Type | Description |
|--------|------|-------------|
| `id` | String (ID) | Unique voice line identifier. Format: `voice_[narrator/knight/unit]_[event]_[NN]` where `NN` is a two-digit sequence number. |
| `comment` | String | Context note for voice actors and translators (e.g., "plays when you start a campaign"). Not shown in-game. |
| `en` | String | English script/subtitle for this line. |
| `fr` | String | French script/subtitle. |
| `de` | String | German script/subtitle. |
| `zh` | String | Simplified Chinese script/subtitle. |

---

### 4.2 `Narrator voice lines.csv`

**Active file:** `Narrator voice lines.csv` (~741 rows)

**Purpose:** Maps in-game events to the pool of narrator voice lines that can play for each event, with optional conditions that narrow which line variant fires. Each row is one possible narrator line for one event, allowing multiple lines per event (the game picks one).

**Cross-file references:**
- `event` values are matched by `properties.csv` (`event` column, `type = Narrator`) to determine scheduling and priority.
- `narrator` text content corresponds to `en` text in `VO.csv` (used to cross-reference the audio).
- `//fmod event` is a developer comment indicating the FMOD audio event path (not read by the game from this file — routing is in `properties.csv`).

| Column | Type | Description |
|--------|------|-------------|
| `event` | String (ID) | The in-game event that triggers this line. Examples: `greetings`, `open_field_battle_won`, `our_castle_lost`, `crusade_started`. |
| `condition` | String | Optional sub-condition. If blank, the line plays for all instances of this event. Examples: `king`, `prince`, `knight`, `white_peace`, `with_kingdom`. |
| `narrator` | String | The English script text of this narrator line. Cross-referenceable with `en` in `VO.csv`. |
| `comments` | String | Developer/translator context notes. Not shown in-game. |
| `//fmod event` | String | Comment-column reference to the FMOD event path. Not read by the game from this file. |
| `//audio file old` | String | Comment-column reference to the legacy audio file name. Not read by the game. |

---

### 4.3 `Knights voice lines.csv`

**Active file:** `Knights voice lines.csv` (~426 rows)

**Purpose:** Maps in-game events to pools of voice lines for each knight class. Each column after `condition` is a different knight class/role, and each row is one event. Multiple lines can exist per event (the game selects one). This file defines what a Marshal says versus what a Merchant says when the same event fires.

**Cross-file references:**
- `event` values are matched by `properties.csv` (`event` column, `type = Character`).
- Column headers (knight classes) correspond to class identifiers in `kingdoms.csv` (`KingClass`) and `fr_classes.csv`/`ru_classes.csv`.

| Column | Type | Description |
|--------|------|-------------|
| `event` | String (ID) | In-game event that triggers this line pool. Examples: `greet`, `greet_in_battle`, `exile`, `imprisoned`, `action_confirmation`, `send_abroad`. |
| `condition` | String | Optional condition narrowing when this row applies. Examples: `king`, `not_king`, `prince`. |
| `marshal` | String | Line spoken by a Marshal knight for this event. |
| `merchant` | String | Line spoken by a Merchant knight. |
| `spy` | String | Line spoken by a Spy knight. |
| `cleric` | String | Line spoken by a Cleric knight. |
| `diplomat` | String | Line spoken by a Diplomat knight. |
| `own_prisoner_in_foreign_dungeon` | String | Line spoken when the player's own knight is imprisoned abroad. |
| `prisoner_in_our_dungeon` | String | Line spoken by a knight who is an enemy prisoner held in the player's dungeon. |
| `puppet_pope` | String | Line spoken by a knight who has been installed as a puppet Pope. |

---

### 4.4 `Units voice lines.csv`

**Active file:** `Units voice lines.csv` (~39 columns, multiple rows per event)

**Purpose:** Maps in-game battle events to voice lines for each unit archetype. The columns represent different unit voice groups (e.g., `peasant_european`, `mounted_arabic`, `highlander`), and the rows represent different battle events. The first two rows are comment/metadata rows explaining accents and listing which `units.csv` IDs map to each column.

**Cross-file references:**
- `event` values are matched by `properties.csv` (`event` column, `type = Unit`).
- The `//unit_ids` comment row maps column headers to `extend *` IDs from `units.csv` (multiple unit IDs per column, newline-separated).

| Column | Type | Description |
|--------|------|-------------|
| `event` | String (ID) | In-game battle event. Examples: `selected`, `move`, `attack`, `retreat`, `rally`. |
| `default` | String | Fallback line used when no specific archetype line is defined. |
| `battering_ram` | String | Lines for siege ram operators. |
| `peasant_european` | String | Lines for European peasant/militia units. |
| `peasant_arabic` | String | Lines for Arabic peasant units. |
| `army_leader_european` | String | Lines for European noble army leaders. |
| `army_leader_arabic` | String | Lines for Arabic noble army leaders. |
| `guard_european` through `teutonic_champion` | String | Lines for each named unit archetype. See the `//unit_ids` comment row in the file for the full mapping of which `units.csv` IDs belong to each column. |

---

### 4.5 ADDITIONS variants

**Files:** `Knights voice lines ADDITIONS.csv`, `Narrator voice lines ADDITIONS.csv`, `Narrator voice lines ADDITIONS 2.csv`, `Narrator voice lines ADDITIONS 3.csv`, `Narrator voice lines ADDITIONS 4.csv`, `Units voice lines ADDITIONS.csv`

**Purpose:** Supplementary voice line tables that extend the base voice line files with additional lines for existing events, or lines for new events not present in the base files. They share identical column structures with their base counterparts. The game merges these with the base files at runtime, expanding the available line pool for each event.

**Cross-file references:** Same as their base counterparts (sections 4.2, 4.3, 4.4).

---

### 4.6 VIRTUAL variants

**Files:** `VIRTUAL Knights voice lines.csv`, `VIRTUAL Narrator voice lines.csv`

**Purpose:** These are large (~597 KB and ~7 KB respectively) generated reference files that appear to be the fully expanded/merged result of combining the base voice line files with all their ADDITIONS variants. They likely serve as a developer reference or are used by an external tool to validate completeness. They should not be edited directly — edits should be made to the base or ADDITIONS files.

---

## 5. Duplicate / Backup Files Reference

Repository Owner Note:
*Since several files had the same name but exists in different folders, I just duplicated them (they have `(copia)` appended to the name - meaning "copy" - and `(un'altra copia)` - meaning "another copy"). This might be a source of confusion that, hopefully, someone will fix in this documentation.*

*For quite understandable reasons, Claude categorized those files as backups.*

| Backup file | Active file to edit |
|---|---|
| `CoA (copia).csv` | `CoA.csv` |
| `cultures (copia).csv` | `cultures.csv` |
| `cultures (un'altra copia).csv` | `cultures.csv` |
| `en_preload (copia).csv` | `en_preload.csv` |
| `en_teritories (copia).csv` | `en_teritories.csv` |
| `geo_features (copia).csv` | `geo_features.csv` |
| `kingdoms (copia).csv` | `kingdoms.csv` |
| `kingdoms (un'altra copia).csv` | `kingdoms.csv` (this copy contains the full dataset; the active `kingdoms.csv` appears to be a subset/work-in-progress) |
| `languages (copia).csv` | `languages.csv` |
| `realms (copia).csv` | `realms.csv` (this copy contains the full dataset; the active `realms.csv` appears to be a work-in-progress subset) |

> **Note on `kingdoms (un'altra copia).csv` and `realms (copia).csv`:** These two "backup" files are actually more complete than the current active files. The active `kingdoms.csv` contains only ~10 rows (possibly a work-in-progress), while the backup contains 360 kingdom definitions. Similarly, `realms (copia).csv` contains ~1,213 province rows vs. ~40 in `realms.csv`. If these CSVs are used for modding, you likely want to start from the backup copies.