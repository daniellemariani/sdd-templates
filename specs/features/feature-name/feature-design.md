# [Feature Name] — Design

**Version:** 0.0.1
**Status:** Draft
**Phase:** [1 (Android) / 2 (Backend) / 3 (Web)]
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

This document defines the technical solution design for the [Feature Name] feature on [platform] (Phase [N]). It covers the component structure, data models, screen-level composable and ViewModel design, shared components, navigation implementation, dependency injection, and testing strategy.

This document is the implementation reference for the [Feature Name] feature. All decisions here must be consistent with `specs/features/[feature]/requirements.md` (functional requirements) and `specs/design/design.md` (visual design system). Where this document references design tokens, those tokens are defined in `specs/design/design.md` and mapped to [Material 3 / platform design system] via the app's theme.

---

## Related Documents

| Document | Purpose |
|---|---|
| specs/features/[feature]/requirements.md | Functional requirements and acceptance criteria |
| specs/design/design.md | Design system — color tokens, typography, spacing, elevation |
| specs/design/navigation.md | Navigation flows and [Activity/page] structure |
| specs/technical/data-model.md | Canonical schema for [entities used by this feature] |
| ARCHITECTURE.md | [Platform] stack, MVVM + Clean Architecture, package structure |

---

## Architecture Overview

[Describe the high-level architecture of this feature. Cover:]
- What Activity/Fragment/Page/Component hosts this feature
- Whether it has its own NavHost or participates in the main one
- Whether all operations are offline-only or network-dependent
- The relationship between this feature's activity/component and the main shell

```
[FeatureActivity / FeaturePage]
    └── [FeatureNavHost / Router]
            ├── [Screen1]        ──► route: "[route_1]"
            ├── [Screen2]        ──► route: "[route_2]"
            └── [Screen3]        ──► route: "[route_3]"

[Local Storage — e.g. SharedPreferences / DataStore]
    ├── [flag_key]: Boolean
    └── [value_key]: String

[Local Database — e.g. Room / SQLite]
    ├── [EntityDao]     ──► [Entity] entity
    └── [EntityDao]     ──► [Entity] entity

[Main Shell]  ◄── started via [Intent / navigation] on feature completion
```

**Key architectural constraints:**
- [Constraint 1 — e.g. "Feature checks completion flag on create; if already done, navigates directly to main shell without inflating any UI."]
- [Constraint 2 — e.g. "All [local storage] reads and writes go through `[DataSource]` — no direct access anywhere in the feature."]
- [Constraint 3 — e.g. "Initialization runs once, on a background coroutine, before the first screen is shown."]
- [Constraint 4 — e.g. "All screens share a single ViewModel scoped to the [Activity]. This avoids re-running initialization and allows state to persist across screen transitions."]

---

## Component Structure

```
feature/[feature-name]/
├── ui/
│   ├── [Feature]Activity.kt              # Activity shell, gate check, NavHost
│   ├── [Feature]NavGraph.kt              # NavHost destinations and route constants
│   ├── [Feature]ViewModel.kt             # Single ViewModel scoped to Activity
│   ├── screens/
│   │   ├── [Screen1].kt
│   │   ├── [Screen2].kt
│   │   └── [Screen3].kt
│   └── components/
│       ├── [SharedComponent1].kt
│       └── [SharedComponent2].kt
├── di/
│   └── [Feature]Module.kt                # Hilt @Binds and @Provides for this feature
├── domain/
│   ├── [Feature]Repository.kt            # Interface
│   ├── [InitializeUseCase].kt
│   ├── [CreateEntityUseCase].kt
│   └── [GetEntityUseCase].kt
└── data/
    ├── local/
    │   └── [Feature]LocalDataSource.kt   # Wraps DAOs
    └── [Feature]RepositoryImpl.kt        # Implements [Feature]Repository
```

**Notes:**
- [Any use case extraction note — e.g. "CreateEntityUseCase may be extracted to core/domain/ when the [Entity] feature is implemented if logic is identical."]
- [Any shared composable note — e.g. "[SharedForm].kt is a shared composable defined in `core/ui/`."]
- [NavGraph route constants note — e.g. "NavGraph.kt defines route constants as an object to avoid stringly-typed navigation."]
- [Any core/data dependency note — e.g. "[DataSource] lives in `core/data/` and is not feature-specific."]

---

## Constants

### [Local Storage Keys, e.g. SharedPreferences Keys / DataStore Keys]

All [local storage] keys are defined as constants in a single object in `core/data/`. This prevents typos and ensures that any feature referencing these values uses the same key string.

```kotlin
// core/data/[PreferenceKeys / Constants].kt
object [PreferenceKeys] {
    const val [FLAG_KEY] = "[flag_key]"
    const val [VALUE_KEY] = "[value_key]"
}
```

### String Resources

All user-facing copy is defined in `res/values/strings.xml`. No hardcoded strings in composables.

```xml
<!-- [Feature] strings -->
<string name="[feature]_[screen]_title">[Title copy]</string>
<string name="[feature]_[screen]_subtitle">[Subtitle copy]</string>
<string name="[feature]_[cta_label]">[CTA label]</string>
<string name="[feature]_error_[scenario]">[Error message]</string>
<!-- Add one entry per user-visible string. -->
```

---

## Data Models

### [Feature] UI State

```kotlin
data class [Feature]UiState(
    // Initialization
    val isInitializing: Boolean = true,
    val initializationError: Boolean = false,

    // [Screen 2 state — e.g. input field]
    val [fieldName]: String = "",
    val is[Field]Valid: Boolean = false,

    // [Screen 3 state — e.g. entity form]
    val [formField1]: String = "",
    val [formField2]: String = "",
    val [conditionalField]: String = "",
    val show[ConditionalField]: Boolean = false,
    val is[Form]Valid: Boolean = false,

    // Error states
    val [field1]Error: String? = null,
    val [field2]Error: String? = null,
    val [form]SaveError: String? = null,

    // Dialog
    val show[Dialog]: Boolean = false,

    // Counters / metadata
    val saved[Entity]Count: Int = 0,
)
```

### [Feature] Events

```kotlin
sealed class [Feature]Event {
    // [Screen 1 events]
    data object SkipSlides : [Feature]Event()
    data object GetStarted : [Feature]Event()

    // [Screen 2 events]
    data class [Field]Changed(val value: String) : [Feature]Event()
    data object ContinueWith[Field] : [Feature]Event()

    // [Screen 3 events — entity form]
    data class [FormField1]Changed(val value: String) : [Feature]Event()
    data class [FormField2]Changed(val value: String) : [Feature]Event()
    data class [ConditionalField]FocusLost(val value: String) : [Feature]Event()
    data object Save[Entity] : [Feature]Event()
    data object AddAnother[Entity] : [Feature]Event()
    data object GoToHome : [Feature]Event()

    // Error recovery
    data object InitializationRetried : [Feature]Event()
}
```

### [Feature] Navigation Side Effects

```kotlin
sealed class [Feature]NavigationEvent {
    data object NavigateTo[Screen2] : [Feature]NavigationEvent()
    data object NavigateTo[Screen3] : [Feature]NavigationEvent()
    data object NavigateToHome : [Feature]NavigationEvent()
}
```

> Navigation side effects are emitted via a `Channel<[Feature]NavigationEvent>` collected as a `Flow` in the Activity. The Activity handles navigation imperatively — no navigation logic in composables.

---

## Screen Designs

### [Screen 1, e.g. Feature Slides Screen]

**Route:** `"[route_1]"`

**Composable tree:**

```
[Screen1]
    ├── Box (fillMaxSize, background = color.accent.primary / color.background.primary)
    │   ├── Text — "[App Name]" (BrandFontFamily, centered, top)  ← spacing.xl top padding
    │   ├── HorizontalPager (pageCount = N)
    │   │   └── [SlideComponent]
    │   │       ├── [Illustration placeholder]
    │   │       ├── Text — slide title (Heading 2, white/primary, bold, centered)
    │   │       └── Text — slide message (Body, white/secondary, centered)
    │   ├── [PageIndicator component]
    │   └── [ActionArea]
    │       ├── [Skip] TextButton — visible on pages 0..N-2 only
    │       └── [PrimaryAction] Button — visible on page N-1 only
```

**Design token reference:**

| Element | Token | Value |
|---|---|---|
| Background | `color.accent.primary` | [hex] |
| [App name] top padding | `spacing.xl` | 32dp |
| Illustration area | — | [description of proportions] |
| Title typography | `Heading 2` | [size, weight] |
| Body typography | `Body` | [size, weight] |
| Page indicator spacing | `spacing.lg` | 24dp above action area |
| [CTA button] bottom padding | `spacing.lg` | 24dp |

**Back navigation:** [Describe back behavior per page. E.g. "Page 0 → BackHandler exits the app. Pages 1+ → no BackHandler, Compose Navigation defaults to pager scroll back."]

---

### [Screen 2, e.g. Set Your Name Screen]

**Route:** `"[route_2]"`

**Composable tree:**

```
[Screen2]
    ├── Box (fillMaxSize, background = color.background.primary)
    │   ├── Text — "[App Name]" (BrandFontFamily, centered, top)   ← spacing.xl top padding
    │   ├── Text — [screen label/prompt] (Body, secondary color, centered)
    │   ├── [InputField] (transparent background, large font, max N chars)
    │   └── [CTA Button] (enabled when is[Field]Valid, full-width, bottom)
```

**Design token reference:**

| Element | Token | Value |
|---|---|---|
| Background | `color.background.primary` | [hex] |
| [App name] top padding | `spacing.xl` | 32dp |
| Prompt bottom margin | `spacing.lg` | 24dp |
| Input field font size | `Heading 1` | [size] |
| CTA bottom padding | `spacing.lg` | 24dp |

**Button state logic:**
- Disabled: field empty or fewer than [N] characters (after trim)
- Enabled: field contains between [min] and [max] characters (inclusive)

**Back navigation:** [Describe back behavior. E.g. "BackHandler exits the app — previous screen is not shown."]

---

### [Screen 3, e.g. Entity Creation Form]

**Route:** `"[route_3]"`

**Composable tree:**

```
[Screen3]
    ├── Column (fillMaxSize, verticalScroll, background = color.background.primary)
    │   ├── Text — "[App Name]" (BrandFontFamily, centered, top)   ← spacing.xl top padding
    │   ├── Text — [encouraging copy] (Body, secondary, centered)  ← spacing.lg bottom margin
    │   ├── [EntityFormFields] composable
    │   │   ├── [Field1] — OutlinedTextField
    │   │   ├── [Field2] — [DropdownMenu / Selector]
    │   │   ├── [Field3] — OutlinedTextField (conditional — shown when [condition])
    │   │   └── [error texts below each field]
    │   ├── [Save Button] (enabled: is[Form]Valid, full-width)
    │   │   └── [save error text below if non-null]
    │   └── [Skip TextButton] (always enabled, centered)
```

**Design token reference:**

| Element | Token | Value |
|---|---|---|
| Background | `color.background.primary` | [hex] |
| [App name] top padding | `spacing.xl` | 32dp |
| Form field spacing | `spacing.md` | 16dp between fields |
| Save button top margin | `spacing.lg` | 24dp |
| Skip button bottom padding | `spacing.lg` | 24dp |

**[Conditional field] visibility:** Shown only when [condition, e.g. `accountType == CREDIT_CARD`]. Hidden and value cleared when condition is false.

**Save button enabled when:**
1. [Field 1] is non-empty
2. [Field 2] is a valid selection
3. [Amount field] is valid (≥ 0, ≤ max)
4. [Conditional field]: if [condition], must be valid AND [cross-field constraint]
5. No pending cross-field validation errors

**Back navigation:** [Describe back behavior.]

---

## Shared Components

### [EntityFormFields]

**File:** `core/ui/components/[EntityFormFields].kt`

A reusable composable shared between the [feature] and [other feature] flows. It renders all form fields for creating or editing a [Entity], with externally-provided values and callbacks — no ViewModel dependency.

```kotlin
@Composable
fun [EntityFormFields](
    [field1]: String,
    on[Field1]Change: (String) -> Unit,
    [field1]Error: String?,
    [field2]: [Type],
    on[Field2]Change: ([Type]) -> Unit,
    [conditionalField]: String,
    on[ConditionalField]Change: (String) -> Unit,
    on[ConditionalField]FocusLost: () -> Unit,
    show[ConditionalField]: Boolean,
    [conditionalField]Error: String?,
    [crossFieldError]: String?,
    modifier: Modifier = Modifier,
)
```

> Defining this as a pure composable (no ViewModel) ensures it can be reused in both [feature] and [other feature] without coupling to either ViewModel.

---

### [PageIndicator / Progress Dots]

**File:** `feature/[feature]/ui/components/[FeaturePageIndicator].kt`

```kotlin
@Composable
fun [FeaturePageIndicator](
    pageCount: Int,
    currentPage: Int,
    modifier: Modifier = Modifier,
)
```

Renders `pageCount` dots. Active dot: filled circle, `color.accent.on` (or white on accent background). Inactive dots: outline/dimmed circle. Updates in sync with pager state.

---

### [ConfirmationDialog]

**File:** `feature/[feature]/ui/components/[FeatureDialog].kt`

```kotlin
@Composable
fun [FeatureDialog](
    [entityName]: String,
    onAddAnother: () -> Unit,
    onPrimaryAction: () -> Unit,
)
```

Shown after a successful save. Offers "Add another [entity]" and "[Primary action, e.g. Go to Home]" options.

---

## ViewModel

### [Feature]ViewModel

**File:** `feature/[feature]/ui/[Feature]ViewModel.kt`

Scoped to [Activity] (not individual destinations) via `hiltViewModel()`. All screens resolve to the same instance.

**State:**
```kotlin
private val _uiState = MutableStateFlow([Feature]UiState())
val uiState: StateFlow<[Feature]UiState> = _uiState.asStateFlow()
```

**Navigation side effects:**
```kotlin
private val _navigationEvents = Channel<[Feature]NavigationEvent>(Channel.BUFFERED)
val navigationEvents = _navigationEvents.receiveAsFlow()
```

**Initialization:** Runs once in `init {}` block. Calls `[InitializeUseCase]` on a background coroutine. Sets `isInitializing = false` and `initializationError = false/true` based on result. Does not re-run on screen transitions because the ViewModel is Activity-scoped.

**Key event handlers:**

| Event | Handler logic |
|---|---|
| `[Field]Changed` | Trim, validate, update `uiState.[field]` and `is[Field]Valid` |
| `ContinueWith[Field]` | Call `[PersistUseCase]`; emit `NavigateTo[Screen]` on success |
| `[FormField]Changed` | Update form field in state; recompute `is[Form]Valid` |
| `[ConditionalField]FocusLost` | Run cross-field validation; set/clear `[crossField]Error` |
| `Save[Entity]` | Validate all fields; call `[CreateUseCase]`; on success: set `show[Dialog] = true`, increment counter; on duplicate: set `[field]Error`; on failure: set `[form]SaveError` |
| `AddAnother[Entity]` | Clear form fields and errors; set `show[Dialog] = false` |
| `GoToHome` | Emit `NavigateToHome` |
| `InitializationRetried` | Set `isInitializing = true`, `initializationError = false`; re-run `[InitializeUseCase]` |

**`is[Form]Valid` computation:** Derived synchronously from `uiState` on every state update. Returns `false` if any required field is empty, any amount is invalid, any cross-field error is non-null, or the [conditional field] condition is unmet.

---

## Navigation

### Route Constants

```kotlin
// feature/[feature]/ui/[Feature]NavGraph.kt
object [Feature]Routes {
    const val [SCREEN1] = "[screen_1_route]"
    const val [SCREEN2] = "[screen_2_route]"
    const val [SCREEN3] = "[screen_3_route]"
}
```

### [Feature]Activity

```kotlin
@AndroidEntryPoint
class [Feature]Activity : ComponentActivity() {

    @Inject lateinit var [dataSource]: [DataSource]

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Gate: if already completed, skip directly to main shell
        if ([dataSource].[isCompleted]()) {
            startActivity(Intent(this, MainActivity::class.java))
            finish()
            return
        }

        setContent {
            AppTheme {
                val navController = rememberNavController()
                val viewModel: [Feature]ViewModel = hiltViewModel()

                // Collect navigation side effects
                LaunchedEffect(Unit) {
                    viewModel.navigationEvents.collect { event ->
                        when (event) {
                            [Feature]NavigationEvent.NavigateTo[Screen2] ->
                                navController.navigate([Feature]Routes.[SCREEN2])
                            [Feature]NavigationEvent.NavigateTo[Screen3] ->
                                navController.navigate([Feature]Routes.[SCREEN3])
                            [Feature]NavigationEvent.NavigateToHome -> {
                                startActivity(Intent(this@[Feature]Activity, MainActivity::class.java))
                                finish()
                            }
                        }
                    }
                }

                NavHost(
                    navController = navController,
                    startDestination = [Feature]Routes.[SCREEN1]
                ) {
                    composable([Feature]Routes.[SCREEN1]) {
                        [Screen1](viewModel = viewModel)
                    }
                    composable([Feature]Routes.[SCREEN2]) {
                        [Screen2](viewModel = viewModel)
                    }
                    composable([Feature]Routes.[SCREEN3]) {
                        [Screen3](viewModel = viewModel)
                    }
                }
            }
        }
    }
}
```

---

## Dependency Injection

### DatabaseModule (core)

```kotlin
// core/data/di/DatabaseModule.kt
@Module
@InstallIn(SingletonComponent::class)
object DatabaseModule {
    @Provides @Singleton
    fun provideDatabase(@ApplicationContext ctx: Context): AppDatabase =
        Room.databaseBuilder(ctx, AppDatabase::class.java, "[app-name].db").build()

    @Provides
    fun provide[Entity1]Dao(db: AppDatabase): [Entity1]Dao = db.[entity1]Dao()

    @Provides
    fun provide[Entity2]Dao(db: AppDatabase): [Entity2]Dao = db.[entity2]Dao()
}
```

### [Feature]Module

```kotlin
// feature/[feature]/di/[Feature]Module.kt
@Module
@InstallIn(SingletonComponent::class)
abstract class [Feature]Module {

    @Binds
    abstract fun bind[Feature]Repository(
        impl: [Feature]RepositoryImpl
    ): [Feature]Repository

    companion object {
        // Add @Provides here if any dependencies require factory methods
    }
}
```

Use cases use `@Inject constructor(...)` — no explicit `@Provides` needed when all dependencies are injectable.

---

## Implementation Notes

### [DB Initialization Idempotency]

The initialization use case must be idempotent — safe to call multiple times without creating duplicate records:

```
1. Query [EntityDao] for any existing non-deleted [Entity]
2. If found: skip creation, return success
3. If not found: create [Entity], then seed all default records in a single @Transaction
```

[Seeding] is wrapped in a Room `@Transaction` to ensure all records are inserted atomically. If any insert fails, the entire seed is rolled back and the error is surfaced to the ViewModel.

---

### [Amount / Currency Input Parsing]

[Describe any custom input parsing logic. Example:]

Input is collected as a raw string and parsed to integer [cents/units] before saving:

1. Strip currency symbols and formatting characters (e.g. `$`, `,`)
2. Parse to `Double`
3. Multiply by [100], round to nearest integer
4. Validate: result must be ≥ [0] for [amount field], > [0] for [required positive field], ≤ [max] for both
5. Cross-field validate: if [condition], [field A] must be [comparison] [field B]. Run on [field blur] and on Save.

Parsing runs in the ViewModel on Save, not on every keystroke. Field-level formatting is handled by a custom `VisualTransformation`.

---

### [Single ViewModel Scope — if applicable]

The ViewModel is scoped to the [Activity] (not individual destinations). All screens call `hiltViewModel<[Feature]ViewModel>()` — Compose resolves this to the Activity-scoped instance. This ensures:

- Initialization state is computed once and survives screen transitions
- [Entity] count and other cross-screen state persists if the user navigates within the flow
- No repeated database reads between screens

---

### [Back Behavior on First Screen — if applicable]

The [pager / first screen] does not consume back events by default. A `BackHandler` is conditionally registered only when `[condition, e.g. pagerState.currentPage == 0]` to exit the app. On [subsequent pages / screens], no `BackHandler` is registered — Compose Navigation handles back normally.

---

### [Atomic State Write — if applicable]

`[flag_key]` and `[value_key]` are written atomically through `[DataSource].[writeMethod]()`. No caller writes these keys directly — the atomic write is centralized:

```kotlin
// core/data/[DataSource].kt
fun [writeMethod](value: String) {
    prefs.edit {
        putString([Keys].[VALUE_KEY], value)
        putBoolean([Keys].[FLAG_KEY], true)
    }
}
```

---

## Error Handling

| Error scenario | Detection point | UiState change | User-facing behavior |
|---|---|---|---|
| [Initialization failure] | `[InitializeUseCase]` throws | `initializationError = true` | Full-screen error with message and Retry button. First screen not shown. |
| [Duplicate name] | Room `SQLiteConstraintException` | `[field]Error = "[message]"` | Inline error below [field]. Dialog not shown. |
| [Write failure] | Unexpected exception from DAO | `[form]SaveError = "[message]"` | Inline error below Save button. |
| [Amount exceeds max] | Validation in ViewModel on Save | `[amount]Error = "[message]"` | Inline error below amount field. |
| [Cross-field error] | Validation on field blur and on Save | `[crossField]Error = "[message]"` | Inline error below relevant field. Save stays disabled. |

All errors are surfaced as fields in `[Feature]UiState` — no exceptions thrown to the UI layer. The ViewModel catches all exceptions from use cases and maps them to state. No Snackbars — all errors are inline.

**Full-screen error state anatomy:**

```
[FullScreenErrorState]
    ├── Icon — error / warning (48dp)
    ├── Text — "[Primary message]" (Title style)
    ├── Text — "[Secondary message]" (Body style, secondary color)
    └── Button — "Retry" (filled, accent)
```

Tapping Retry dispatches `[Feature]Event.InitializationRetried`, re-runs the use case, resets `initializationError = false, isInitializing = true`.

---

## Testing Strategy

### Unit Tests

| Class | What to test |
|---|---|
| `[Feature]ViewModel` | `is[Field]Valid` transitions (empty → min chars → max chars → cleared). `is[Form]Valid` with all field combinations. [Conditional field] visibility logic. `Save[Entity]` happy path. `Save[Entity]` with duplicate name. `Save[Entity]` with write failure. `AddAnother[Entity]` clears form state. Navigation side effects emitted correctly. Cross-field validation on blur (set error / clear error). `is[Form]Valid` false when cross-field error is non-null. `InitializationRetried` re-runs use case. |
| `[InitializeUseCase]` | Creates [Entity] and [N] seeded records on empty DB. Idempotent on second call (no duplicates). Returns error on DAO failure. |
| `[PersistValueUseCase]` | Delegates correctly to `[DataSource].[method]`. Trims whitespace before delegating. |
| `[CreateEntityUseCase]` | Happy path: entity persisted with correct field values. Duplicate name: returns `Result.failure`. Amount boundary values (0, 1, max) handled correctly. |

### Integration Tests

| Scope | What to test |
|---|---|
| Room (in-memory DB) | `[InitializeUseCase]` creates exactly the expected records. Second call is a no-op. `[CreateEntityUseCase]` persists entity with correct foreign key. Duplicate name rejected by unique constraint. FK constraint violation fails as expected. |
| [DataStore / SharedPreferences] | `[DataSource].[writeMethod]` writes all keys atomically. `[isCompleted]` returns correct value. Values survive a simulated process restart (read back from a fresh instance). |

### UI Tests

Deferred — consistent with the global testing strategy in `ARCHITECTURE.md`. UI tests for [feature] are not required for Phase 1.

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |
