# [Feature Name] — Tasks

**Version:** 0.0.1
**Status:** Draft
**Phase:** [1 (Android) / 2 (Backend) / 3 (Web)]
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

This file defines the implementation task breakdown for the [Feature Name] feature. Tasks are organized into groups that follow the natural dependency order of the [platform] layered architecture: foundation first, then data, then logic, then UI, then tests.

Each task specifies its requirements coverage, acceptance criteria, dependencies on other tasks, files to create, and an effort estimate (S = ~1–2h, M = ~2–4h, L = ~4–8h).

**Group execution order:**

| Group | Name | Can start when |
|---|---|---|
| 0 | Project Foundation | — |
| 1 | [Database / Schema] Foundation | TSK-[FT]-01 done |
| 2 | Data Layer | TSK-[FT]-[last-foundation-task] done |
| 3 | Constants & Resources | TSK-[FT]-01 done (parallel with Group 2) |
| 4 | Navigation Shell | TSK-[FT]-[data-layer-done], TSK-[FT]-[resources-done] done |
| 5 | ViewModel | TSK-[FT]-[data-layer-done] done |
| 6 | Shared Components | TSK-[FT]-[nav-done], TSK-[FT]-[resources-done] done |
| 7 | Screens | Groups 4–6 done |
| 8 | Testing | Groups 2 and 5 done |

> Adjust groups to match your feature's actual dependency structure. Remove groups that don't apply (e.g. no Project Foundation for a non-first feature). Add groups if needed.

---

## Task Summary

| ID | Title | Group | Phase | Effort | Status |
|---|---|---|---|---|---|
| TSK-[FT]-01 | [Create project skeleton / Set up feature scaffold] | Project Foundation | [N] | M | Not Started |
| TSK-[FT]-02 | Set up base package structure | Project Foundation | [N] | S | Not Started |
| TSK-[FT]-03 | Configure theme | Project Foundation | [N] | S | Not Started |
| TSK-[FT]-04 | Set up DI application module | Project Foundation | [N] | S | Not Started |
| TSK-[FT]-05 | Define [primary entity] and DAO | [DB] Foundation | [N] | M | Not Started |
| TSK-[FT]-06 | Define [secondary entity] and DAO | [DB] Foundation | [N] | M | Not Started |
| TSK-[FT]-07 | Implement [Feature]LocalDataSource | Data Layer | [N] | S | Not Started |
| TSK-[FT]-08 | Implement [Feature]Repository interface and implementation | Data Layer | [N] | S | Not Started |
| TSK-[FT]-09 | Implement [InitializeUseCase] | Data Layer | [N] | M | Not Started |
| TSK-[FT]-10 | Implement [PersistValueUseCase] | Data Layer | [N] | S | Not Started |
| TSK-[FT]-11 | Implement [CreateEntityUseCase] | Data Layer | [N] | M | Not Started |
| TSK-[FT]-12 | Create constants and [DataSource] | Constants & Resources | [N] | S | Not Started |
| TSK-[FT]-13 | Define string resources | Constants & Resources | [N] | S | Not Started |
| TSK-[FT]-14 | Implement [Feature]Activity and NavGraph shell | Navigation Shell | [N] | M | Not Started |
| TSK-[FT]-15 | Implement [Feature]ViewModel | ViewModel | [N] | L | Not Started |
| TSK-[FT]-16 | Implement [EntityFormFields] shared composable | Shared Components | [N] | M | Not Started |
| TSK-[FT]-17 | Implement [PageIndicator] and [Slide] components | Shared Components | [N] | S | Not Started |
| TSK-[FT]-18 | Implement [ConfirmationDialog] component | Shared Components | [N] | S | Not Started |
| TSK-[FT]-19 | Implement [Screen1] | Screens | [N] | M | Not Started |
| TSK-[FT]-20 | Implement [Screen2] | Screens | [N] | M | Not Started |
| TSK-[FT]-21 | Implement [Screen3] | Screens | [N] | L | Not Started |
| TSK-[FT]-22 | Unit tests for [Feature]ViewModel | Testing | [N] | M | Not Started |
| TSK-[FT]-23 | Unit tests for use cases | Testing | [N] | M | Not Started |
| TSK-[FT]-24 | Integration tests for [Room / DB] | Testing | [N] | M | Not Started |

> Add or remove rows to match your actual task count. IDs are permanent once assigned — never renumber.

---

## Task Format

Each task follows this structure:

```
**TSK-[FT]-XX — Title**
- Effort: S / M / L
- Phase: N
- Group: Group name
- Requirements: RQ-[FT]-XX, ...
- Acceptance Criteria: AC-[FT]-XX, ...
- Status: Not Started / In Progress / Done
- Depends on: TSK-[FT]-XX, ... / None
- Creates:
  - full/path/to/File.kt
- Details:
  What to implement, key constraints, and any non-obvious decisions.
```

---

## Group 0 — Project Foundation

> This group creates the project skeleton and shared infrastructure. Remove this group if the project already exists and this is not the first feature. Only onboarding / first-feature specs typically include this group.

---

**TSK-[FT]-01 — Create [platform] project skeleton**
- Effort: M
- Phase: [N]
- Group: Project Foundation
- Requirements: —
- Acceptance Criteria: —
- Status: Not Started
- Depends on: None
- Creates:
  - `[platform]/build.gradle.kts`
  - `[platform]/settings.gradle.kts`
  - `[platform]/gradle/libs.versions.toml`
  - `[platform]/app/build.gradle.kts`
  - `[platform]/app/src/main/AndroidManifest.xml`
  - `[platform]/app/src/main/java/[package]/app/MainActivity.kt`
  - `[platform]/app/src/main/java/[package]/app/[AppName]App.kt`
- Details:
  Create a new [platform] project using [tool]. Use the following configuration:

  **Version catalog (`libs.versions.toml`):**
  ```toml
  [versions]
  [dependency] = "[version]"
  [dependency] = "[version]"

  [libraries]
  [library-alias] = { group = "[group]", name = "[artifact]", version.ref = "[ref]" }

  [plugins]
  [plugin-alias] = { id = "[plugin-id]", version.ref = "[ref]" }
  ```

  Package name: `[com.owner.appname]`
  Min SDK: [N] | Target SDK: [N] | Compile SDK: [N]

---

**TSK-[FT]-02 — Set up base package structure**
- Effort: S
- Phase: [N]
- Group: Project Foundation
- Requirements: —
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-01
- Creates:
  - `[platform]/app/src/main/java/[package]/core/ui/` (empty, placeholder)
  - `[platform]/app/src/main/java/[package]/core/domain/` (empty, placeholder)
  - `[platform]/app/src/main/java/[package]/core/data/` (empty, placeholder)
  - `[platform]/app/src/main/java/[package]/feature/` (empty, placeholder)
- Details:
  Create the top-level package directories as defined in `ARCHITECTURE.md`. No code yet — only the folder structure.

---

**TSK-[FT]-03 — Configure theme**
- Effort: S
- Phase: [N]
- Group: Project Foundation
- Requirements: —
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-01
- Creates:
  - `[package]/core/ui/theme/Color.kt`
  - `[package]/core/ui/theme/Type.kt`
  - `[package]/core/ui/theme/Theme.kt`
  - `res/font/[brand_font_regular].ttf` (if brand font used)
  - `res/font/[brand_font].xml` (if brand font used)
- Details:
  Define `AppTheme` wrapping `MaterialTheme`. Map design tokens from `specs/design/design.md` to the platform color scheme:

  - `Color.kt`: define all hex color values for light and dark themes
  - `Type.kt`: define typography scale (Display, Heading1–3, Body, BodySmall, Label, Caption). Define `[BrandFontFamily]` val if a brand/display font is used — single declaration, imported everywhere.
  - `Theme.kt`: assemble light/dark `ColorScheme` and `Typography` into `AppTheme`

  **[Brand font registration — if applicable]:**
  ```kotlin
  // core/ui/theme/Type.kt
  val [BrandFontFamily] = FontFamily(
      Font(R.font.[brand_font_regular], FontWeight.Normal)
  )
  ```
  `[BrandFontFamily]` is declared once here and imported where needed. Never redeclare inline in a composable.

---

**TSK-[FT]-04 — Set up Hilt application module**
- Effort: S
- Phase: [N]
- Group: Project Foundation
- Requirements: —
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-01
- Creates:
  - `[package]/app/[AppName]App.kt` (annotated with `@HiltAndroidApp`)
  - `[package]/feature/[feature]/di/[Feature]Module.kt`
- Details:
  Annotate `[AppName]App` with `@HiltAndroidApp`. Register it in `AndroidManifest.xml`.

  Create `[Feature]Module` as an abstract class with `@Module @InstallIn(SingletonComponent::class)`:
  ```kotlin
  @Module
  @InstallIn(SingletonComponent::class)
  abstract class [Feature]Module {
      @Binds
      abstract fun bind[Feature]Repository(impl: [Feature]RepositoryImpl): [Feature]Repository

      companion object {
          // @Provides for non-injectable dependencies if needed
      }
  }
  ```

---

## Group 1 — [Database / Schema] Foundation

---

**TSK-[FT]-05 — Define [primary entity], DAO, and AppDatabase**
- Effort: M
- Phase: [N]
- Group: [DB] Foundation
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-01
- Creates:
  - `[package]/core/domain/[Entity].kt`
  - `[package]/core/data/[Entity]Entity.kt`
  - `[package]/core/data/[Entity]Dao.kt`
  - `[package]/core/data/AppDatabase.kt`
  - `[package]/core/data/di/DatabaseModule.kt`
- Details:
  **Domain model** (`core/domain/[Entity].kt`): pure Kotlin, no framework annotations.
  ```kotlin
  data class [Entity](
      val id: String,
      val [field1]: String,
      val [field2]: [Type],
      val createdAt: Long,
      val updatedAt: Long,
      val deletedAt: Long? = null,
  )
  ```

  **Room entity** (`core/data/[Entity]Entity.kt`): mirrors schema from `specs/technical/data-model.md`.
  ```kotlin
  @Entity(tableName = "[entities]")
  data class [Entity]Entity(
      @PrimaryKey val id: String,
      val [field1]: String,
      // ... all fields from data-model.md
      val createdAt: Long,
      val updatedAt: Long,
      val deletedAt: Long? = null,
  )
  ```

  **DAO** (`core/data/[Entity]Dao.kt`):
  ```kotlin
  @Dao
  interface [Entity]Dao {
      @Query("SELECT * FROM [entities] WHERE deleted_at IS NULL")
      fun getAll(): Flow<List<[Entity]Entity>>

      @Insert(onConflict = OnConflictStrategy.ABORT)
      suspend fun insert(entity: [Entity]Entity)

      @Update
      suspend fun update(entity: [Entity]Entity)
  }
  ```

  **AppDatabase** (`core/data/AppDatabase.kt`):
  ```kotlin
  @Database(entities = [[Entity]Entity::class], version = 1)
  abstract class AppDatabase : RoomDatabase() {
      abstract fun [entity]Dao(): [Entity]Dao
  }
  ```

  **DatabaseModule** (`core/data/di/DatabaseModule.kt`): provides `AppDatabase` and all DAOs as singletons.

---

**TSK-[FT]-06 — Define [secondary entity] and DAO**
- Effort: M
- Phase: [N]
- Group: [DB] Foundation
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-05
- Creates:
  - `[package]/core/domain/[SecondaryEntity].kt`
  - `[package]/core/data/[SecondaryEntity]Entity.kt`
  - `[package]/core/data/[SecondaryEntity]Dao.kt`
- Details:
  Follow the same pattern as TSK-[FT]-05. Register the new entity in `AppDatabase.kt` and add its DAO to `DatabaseModule`. Increment the database version if the database already existed.

---

## Group 2 — Data Layer

---

**TSK-[FT]-07 — Implement [Feature]LocalDataSource**
- Effort: S
- Phase: [N]
- Group: Data Layer
- Requirements: —
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-05, TSK-[FT]-06
- Creates:
  - `[package]/feature/[feature]/data/local/[Feature]LocalDataSource.kt`
- Details:
  Wraps the DAOs needed by this feature. Provides typed methods used by `[Feature]RepositoryImpl`. No business logic here — pure data access delegation.

---

**TSK-[FT]-08 — Implement [Feature]Repository interface and implementation**
- Effort: S
- Phase: [N]
- Group: Data Layer
- Requirements: —
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-07
- Creates:
  - `[package]/feature/[feature]/domain/[Feature]Repository.kt`
  - `[package]/feature/[feature]/data/[Feature]RepositoryImpl.kt`
- Details:
  Define the repository interface in `domain/`. Implement it in `data/`, injecting `[Feature]LocalDataSource`. Register the binding in `[Feature]Module`.

---

**TSK-[FT]-09 — Implement [InitializeUseCase]**
- Effort: M
- Phase: [N]
- Group: Data Layer
- Requirements: RQ-[FT]-NN, RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-08
- Creates:
  - `[package]/feature/[feature]/domain/[Initialize]UseCase.kt`
- Details:
  Idempotency check first — if records already exist, return success without creating duplicates.

  Seeding (if applicable):
  ```
  a. Check if [parent entity] exists
  b. If not: create [parent entity] with UUID v4, timestamp, defaults
  c. Seed all default [child entities] in a single @Transaction
  d. Each record includes all fields from the canonical table in requirements.md
  ```

  Returns `Result<Unit>`. Catches all exceptions and returns `Result.failure`.

---

**TSK-[FT]-10 — Implement [PersistValueUseCase]**
- Effort: S
- Phase: [N]
- Group: Data Layer
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-12
- Creates:
  - `[package]/feature/[feature]/domain/[PersistValue]UseCase.kt`
- Details:
  Delegates to `[DataSource].[writeMethod]`. Trims input before delegating. Uses `[Keys]` constants — never hardcoded strings.

---

**TSK-[FT]-11 — Implement [CreateEntityUseCase]**
- Effort: M
- Phase: [N]
- Group: Data Layer
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-08
- Creates:
  - `[package]/feature/[feature]/domain/[CreateEntity]UseCase.kt`
- Details:
  Happy path: construct entity with UUID v4, current UTC timestamps, and all provided fields. Persist via repository.

  Amount conversion: strip formatting characters → parse to Double → multiply by 100 → round to integer. This conversion happens here, not in the ViewModel.

  Error propagation: catch `SQLiteConstraintException` for unique violations → wrap in `DuplicateNameException`. Propagate all others as `Result.failure`.

---

## Group 3 — Constants & Resources

---

**TSK-[FT]-12 — Create constants and [DataSource]**
- Effort: S
- Phase: [N]
- Group: Constants & Resources
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-01
- Creates:
  - `[package]/core/data/[PreferenceKeys].kt`
  - `[package]/core/data/[DataSource].kt`
- Details:
  **[PreferenceKeys].kt:**
  ```kotlin
  object [PreferenceKeys] {
      const val [FLAG_KEY] = "[flag_key]"
      const val [VALUE_KEY] = "[value_key]"
  }
  ```

  **[DataSource].kt:** wraps `[SharedPreferences / DataStore]`. Provides `[isCompleted](): Boolean` and `[writeMethod](value: String)`. The write method persists both keys atomically using `prefs.edit { ... }`. No feature writes these keys directly.

---

**TSK-[FT]-13 — Define string resources**
- Effort: S
- Phase: [N]
- Group: Constants & Resources
- Requirements: RQ-[FT]-NN (all screens)
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-01
- Creates:
  - `res/values/strings.xml` (add [feature] string entries)
- Details:
  Define all user-visible strings for this feature. Cover: screen titles, prompts, button labels, dialog copy, error messages, placeholder text. No hardcoded strings in composables — always `stringResource(R.string.[key])`.

  ```xml
  <!-- [Feature Name] -->
  <string name="[feature]_[screen]_title">[value]</string>
  <string name="[feature]_[field]_placeholder">[value]</string>
  <string name="[feature]_[cta]_label">[value]</string>
  <string name="[feature]_error_[scenario]">[value]</string>
  ```

---

## Group 4 — Navigation Shell

---

**TSK-[FT]-14 — Implement [Feature]Activity and NavGraph shell**
- Effort: M
- Phase: [N]
- Group: Navigation Shell
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-11, TSK-[FT]-13
- Creates:
  - `[package]/feature/[feature]/ui/[Feature]Activity.kt`
  - `[package]/feature/[feature]/ui/[Feature]NavGraph.kt`
- Details:
  **[Feature]Activity:**
  - Annotated with `@AndroidEntryPoint`
  - Injects `[DataSource]`; in `onCreate`, if `[isCompleted]()` is true, start `MainActivity` and `finish()` immediately
  - Sets content with `AppTheme`, `rememberNavController`, `hiltViewModel<[Feature]ViewModel>()`
  - Collects `viewModel.navigationEvents` in `LaunchedEffect(Unit)` and handles navigation imperatively
  - `NavHost` with `startDestination = [Feature]Routes.[SCREEN1]`
  - Declare in `AndroidManifest.xml`

  **[Feature]NavGraph:**
  ```kotlin
  object [Feature]Routes {
      const val [SCREEN1] = "[screen_1]"
      const val [SCREEN2] = "[screen_2]"
      const val [SCREEN3] = "[screen_3]"
  }
  ```

  Note: `MainActivity` is a temporary placeholder at this stage — a `TODO()` stub is fine.

---

## Group 5 — ViewModel

---

**TSK-[FT]-15 — Implement [Feature]ViewModel**
- Effort: L
- Phase: [N]
- Group: ViewModel
- Requirements: RQ-[FT]-NN through RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN through AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-09, TSK-[FT]-10, TSK-[FT]-11
- Creates:
  - `[package]/feature/[feature]/ui/[Feature]ViewModel.kt`
- Details:
  `@HiltViewModel`. Scoped to [Activity] — all screens call `hiltViewModel<[Feature]ViewModel>()`.

  **Initialization:** in `init {}`, launch coroutine to call `[InitializeUseCase]`. Set `isInitializing = false` on completion. Set `initializationError = true` on failure.

  **State:** `MutableStateFlow([Feature]UiState())`. Expose as `StateFlow`.

  **Navigation:** `Channel<[Feature]NavigationEvent>(Channel.BUFFERED)`. Expose as `receiveAsFlow()`.

  **Key validation logic:**
  - `is[Field]Valid`: `[field].trim().length in [min]..[max]`
  - `is[Form]Valid`: all required fields non-empty AND all amounts valid AND no cross-field errors AND [conditional field] meets its constraint if applicable

  **`[ConditionalField]FocusLost` handler:**
  ```
  if (accountType == [TYPE] && creditLimit < initialBalance):
      set [crossField]Error = stringResource(...)
  else:
      clear [crossField]Error
  recompute is[Form]Valid
  ```

  **`Save[Entity]` handler:**
  1. Re-run cross-field validation even without prior blur
  2. If any validation fails: set error fields, return early
  3. Call `[CreateEntity]UseCase` → on success: set `show[Dialog] = true`, increment `saved[Entity]Count`, clear form
  4. On `DuplicateNameException`: set `[field]Error`
  5. On other failure: set `[form]SaveError`

  **`InitializationRetried`:** set `isInitializing = true, initializationError = false`, re-invoke initialization logic.

---

## Group 6 — Shared Components

---

**TSK-[FT]-16 — Implement [EntityFormFields] shared composable**
- Effort: M
- Phase: [N]
- Group: Shared Components
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: —
- Status: Not Started
- Depends on: TSK-[FT]-13, TSK-[FT]-03
- Creates:
  - `[package]/core/ui/components/[EntityFormFields].kt`
- Details:
  Pure composable — no ViewModel dependency. All values and callbacks provided externally.

  Fields to implement:
  - `[field1]`: `OutlinedTextField`, label from string resources, error text below if `[field1]Error != null`
  - `[field2]`: [Dropdown / ExposedDropdownMenuBox] with options from [Type enum], label, error text
  - `[conditionalField]`: `OutlinedTextField`, visible only when `show[ConditionalField]`, `onFocusChanged` triggers `on[ConditionalField]FocusLost`

  Amount fields: use a `VisualTransformation` for currency formatting (`$1,234.56` display). Raw string state in ViewModel — transform display only.

  Follow spacing tokens from `design.md`: `spacing.md` between fields.

---

**TSK-[FT]-17 — Implement [PageIndicator] and [Slide] components**
- Effort: S
- Phase: [N]
- Group: Shared Components
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-03
- Creates:
  - `[package]/feature/[feature]/ui/components/[FeaturePageIndicator].kt`
  - `[package]/feature/[feature]/ui/components/[FeatureSlide].kt`
- Details:
  **[FeaturePageIndicator]:** `pageCount` dots. Active dot: filled circle, `color.accent.on`. Inactive: dimmed outline. Row with `spacing.xs` between dots.

  **[FeatureSlide]:** layout for a single slide. Props: `illustrationRes`, `title`, `message`. Full height, illustration in center, text below, all white on accent background.

---

**TSK-[FT]-18 — Implement [ConfirmationDialog] component**
- Effort: S
- Phase: [N]
- Group: Shared Components
- Requirements: RQ-[FT]-NN
- Acceptance Criteria: AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-03, TSK-[FT]-13
- Creates:
  - `[package]/feature/[feature]/ui/components/[Feature]Dialog.kt`
- Details:
  `AlertDialog` with:
  - Title: "[You saved [Entity]!]" (from string resources)
  - Body: "[What would you like to do next?]"
  - Confirm: "[Primary action]" → calls `onPrimaryAction`
  - Dismiss: "Add another [Entity]" → calls `onAddAnother`

  Shown when `uiState.show[Dialog] == true`. Dismissed internally by either action — ViewModel drives visibility via state.

---

## Group 7 — Screens

---

**TSK-[FT]-19 — Implement [Screen1, e.g. FeatureSlidesScreen]**
- Effort: M
- Phase: [N]
- Group: Screens
- Requirements: RQ-[FT]-01 through RQ-[FT]-08
- Acceptance Criteria: AC-[FT]-01 through AC-[FT]-03, AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-14, TSK-[FT]-15, TSK-[FT]-17
- Creates:
  - `[package]/feature/[feature]/ui/screens/[Screen1].kt`
- Details:
  Full-screen composable. Background: `color.accent.primary`.

  Top: `Text(text = stringResource(R.string.app_name), fontFamily = [BrandFontFamily], fontSize = 26.sp, ...)` centered via `Box(Modifier.fillMaxWidth(), contentAlignment = Alignment.Center)`, top padding `spacing.xl`. `[BrandFontFamily]` imported from `core/ui/theme/Type.kt` — never redeclared inline.

  `HorizontalPager(pageCount = N, state = pagerState)`:
  - Page 0: no `BackHandler` registered; back exits app via outer `BackHandler { (context as Activity).finish() }`
  - Actually: register `BackHandler(enabled = pagerState.currentPage == 0) { (context as Activity).finish() }`
  - Pages 1+: Compose Navigation handles back normally by scrolling pager back

  Bottom action area:
  - `[Skip]` `TextButton`: visible on pages 0..N-2; dispatches `[Feature]Event.Skip[Slides]`
  - `[PrimaryAction]` `Button` (filled): visible on page N-1 only; dispatches `[Feature]Event.[PrimaryAction]`

  `[FeaturePageIndicator]` above action area.

---

**TSK-[FT]-20 — Implement [Screen2, e.g. SetYourNameScreen]**
- Effort: M
- Phase: [N]
- Group: Screens
- Requirements: RQ-[FT]-09 through RQ-[FT]-13
- Acceptance Criteria: AC-[FT]-04 through AC-[FT]-07, AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-14, TSK-[FT]-15
- Creates:
  - `[package]/feature/[feature]/ui/screens/[Screen2].kt`
- Details:
  Full-screen composable. Background: `color.background.primary` (signals visual transition into app).

  Top: `Text("[App Name]", fontFamily = [BrandFontFamily], fontSize = 26.sp)` centered via `Box`, top padding `spacing.xl`.

  Below: prompt `Text` (`color.text.secondary`, Body typography, centered), bottom margin `spacing.lg`.

  `OutlinedTextField`:
  - Value: `uiState.[fieldName]`
  - `onValueChange`: dispatch `[Feature]Event.[Field]Changed(it.take([maxLength]))`
  - `keyboardOptions`: appropriate input type
  - Background: transparent
  - Font size: significantly larger than surrounding text (e.g. `Heading 2` scale)

  `Button` ("Continue"): enabled = `uiState.is[Field]Valid`. Full-width, bottom padding `spacing.lg`. On click: dispatch `[Feature]Event.ContinueWith[Field]`.

  Back navigation: `BackHandler { (context as Activity).finish() }`.

---

**TSK-[FT]-21 — Implement [Screen3, e.g. EntityCreationScreen]**
- Effort: L
- Phase: [N]
- Group: Screens
- Requirements: RQ-[FT]-14 through RQ-[FT]-26
- Acceptance Criteria: AC-[FT]-08 through AC-[FT]-NN
- Status: Not Started
- Depends on: TSK-[FT]-14, TSK-[FT]-15, TSK-[FT]-16, TSK-[FT]-18
- Creates:
  - `[package]/feature/[feature]/ui/screens/[Screen3].kt`
- Details:
  Full-screen composable. Background: `color.background.primary`. Scrollable `Column` to handle small screens with all fields visible.

  Top: `Text("[App Name]", fontFamily = [BrandFontFamily], ...)` centered, top padding `spacing.xl`. Below: encouraging copy `Text` (secondary color, Body, centered), bottom margin `spacing.lg`.

  `[EntityFormFields]` composable wired to ViewModel:
  - Pass all field values from `uiState`
  - Pass all change callbacks dispatching the corresponding `[Feature]Event`
  - Pass error strings from `uiState`

  Save `Button` (full-width): enabled = `uiState.is[Form]Valid`. On click: dispatch `[Feature]Event.Save[Entity]`. Display `uiState.[form]SaveError` as inline error text below if non-null.

  "Skip for now" `TextButton` (centered, below Save, bottom padding `spacing.lg`). Always enabled. On click: dispatch `[Feature]Event.GoToHome`.

  `[Feature]Dialog` shown when `uiState.show[Dialog] == true`.

  Back navigation: `BackHandler { (context as Activity).finish() }`.

---

## Group 8 — Testing

---

**TSK-[FT]-22 — Unit tests for [Feature]ViewModel**
- Effort: M
- Phase: [N]
- Group: Testing
- Requirements: [all ViewModel-related RQs]
- Acceptance Criteria: [all ViewModel-related ACs]
- Status: Not Started
- Depends on: TSK-[FT]-15
- Creates:
  - `[test-path]/[Feature]ViewModelTest.kt`
- Details:
  Use `kotlinx-coroutines-test` and `TestCoroutineDispatcher`. Use hand-written fakes for use cases (prefer over Mockito for simplicity).

  Test cases:
  - `[field] blank → is[Field]Valid = false`
  - `[field] [min-1] chars → is[Field]Valid = false`
  - `[field] [min] chars → is[Field]Valid = true`
  - `[field] [max] chars → is[Field]Valid = true`
  - `[field] cleared after valid → is[Field]Valid = false`
  - `[field] whitespace only → is[Field]Valid = false` (after trim)
  - `is[Form]Valid false when [required field] empty`
  - `is[Form]Valid false when [amount field] invalid`
  - `is[Form]Valid true with valid [base fields]`
  - `is[Form]Valid false for [conditional type] when [conditional field] missing`
  - `is[Form]Valid true for [conditional type] with all fields`
  - `[crossField]Error set when FocusLost and [fieldA] < [fieldB]`
  - `[crossField]Error cleared when FocusLost and [fieldA] >= [fieldB]`
  - `is[Form]Valid false when [crossField]Error is non-null`
  - `Save[Entity] blocked when [cross-field constraint] fails even without prior blur`
  - `[conditional type] selected → [conditional field] visible in form state`
  - `[type] changed away from [conditional type] → [conditional field] cleared`
  - `Save[Entity] success → show[Dialog] = true, saved[Entity]Count incremented`
  - `Save[Entity] duplicate name → [field]Error set`
  - `AddAnother[Entity] → form cleared, show[Dialog] = false`
  - `ContinueWith[Field] → NavigateTo[Screen] side effect emitted`
  - `GoToHome → NavigateToHome side effect emitted`
  - `InitializationRetried → isInitializing = true, re-runs use case`

---

**TSK-[FT]-23 — Unit tests for use cases**
- Effort: M
- Phase: [N]
- Group: Testing
- Requirements: [use case RQs]
- Acceptance Criteria: [use case ACs]
- Status: Not Started
- Depends on: TSK-[FT]-09, TSK-[FT]-10, TSK-[FT]-11
- Creates:
  - `[test-path]/[Initialize]UseCaseTest.kt`
  - `[test-path]/[PersistValue]UseCaseTest.kt`
  - `[test-path]/[CreateEntity]UseCaseTest.kt`
- Details:
  **[Initialize]UseCaseTest:**
  - Creates exactly [N] [entity type] and [M] [seeded type] when DB is empty
  - Second invocation is a no-op (idempotent)
  - Returns `Result.failure` when repository throws

  **[PersistValue]UseCaseTest:**
  - Writes all required keys via `[DataSource].[writeMethod]`
  - Trims leading/trailing whitespace before writing
  - Uses `[Keys]` constants — verify key strings match

  **[CreateEntity]UseCaseTest:**
  - Happy path: entity inserted with correct field values (amount conversion)
  - Duplicate name: returns `Result.failure([DuplicateNameException])`
  - Boundary: `[amount] = 0` → accepted (or rejected, per business rule)
  - Boundary: `[amount] = [max]` → accepted
  - Note: [any validation that belongs in ViewModel, not use case] is caught in ViewModel — document explicitly

---

**TSK-[FT]-24 — Integration tests for [Room / DB]**
- Effort: M
- Phase: [N]
- Group: Testing
- Requirements: [DB-related RQs]
- Acceptance Criteria: [DB-related ACs]
- Status: Not Started
- Depends on: TSK-[FT]-09, TSK-[FT]-11
- Creates:
  - `[android-test-path]/[Feature]RoomIntegrationTest.kt`
- Details:
  Use `Room.inMemoryDatabaseBuilder` for a clean database per test. Instrumented tests (run on device/emulator).

  Test cases:
  - `[InitializeUseCase]` creates exactly [N] [entity] and [M] [seeded entity] records
  - Second call to `[InitializeUseCase]` leaves DB unchanged (idempotent)
  - `[CreateEntityUseCase]` persists entity with correct `[tenant_id]`
  - Duplicate entity name with same `[tenant_id]` is rejected (unique constraint)
  - Entity with `[tenant_id]` pointing to non-existent [tenant] fails (foreign key constraint)
  - `saved[Entity]Count` reflects actual persisted count after multiple inserts

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |
