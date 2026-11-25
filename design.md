# App Overview
* **Developer:** Stuart Keith (2210286)
* **App Title:** Pulse
## Core Functionalities
- **Live Status Dashboard:** Monitors real-time health across major services with search and status filtering.
- **Outage Reporter & 24-Hour History:** Allows users to report service outages they are experiencing and view the 24-hour outage history chart for each service.
- **Offline Access & Alerts:** Stores telemetry and user notes locally for offline use and alerts the user when a pinned service experiences an outage.
#### Must
- Basic single-screen Material 3 UI with informational cards and interactive search, filter, and report controls.
- State management preserving UI state across screen rotations via `ViewModel` and `StateFlow`.
#### Should
- Scrollable lists (`LazyColumn`) and Type-Safe multi-screen navigation.
- MVVM architecture with Retrofit `@GET` status fetching and `@POST` outage reporting.
- Graceful network error handling with offline fallback and `Snackbar` feedback.
#### Could
- Room Persistence for local offline caching of statuses, notes, and outage reports.
- WorkManager Sync (`StatusSyncWorker`) for periodic background updates and offline report uploads.
- Independent Research Features (Android 13+ outage notifications, custom `Canvas` 24-hour spike charts, and adaptive landscape grids).

## UI/UX Design & The View Layer

### Orientation Wireframes
*(Insert screenshots or wireframes of `DashboardScreen` in portrait and landscape, plus `DetailScreen`, inside a `/docs` folder in your repo and link them here:)*
![Dashboard Portrait](docs/dashboard_portrait.png)
![Dashboard Landscape](docs/dashboard_landscape.png)
![Service Detail](docs/detail_screen.png)

### Compose UI Structure
- **Dashboard (`DashboardScreen`):** Uses `Scaffold`, `TopAppBar` (with refresh `IconButton`), `LinearProgressIndicator`, `OutlinedTextField` (search), `FilterChip` (`All`, `Issues`, `Pinned`), `SnackbarHost`, and `LazyColumn` / `LazyVerticalGrid` containing Material 3 `Card` items (`ServiceStatusCard`) with status badges and pin toggles.
- **Detail (`DetailScreen`):** Uses `Scaffold`, `TopAppBar`, `Card` summaries, a custom Jetpack Compose `Canvas` 24-hour outage bar chart, interactive issue-category `Button`s (`Server`, `Login`, `App`, `API`), an `OutlinedTextField` for offline notes, and a `LazyColumn` of sub-components.

### State Hoisting
All screen composables (`DashboardScreen`, `ServiceStatusCard`, `DetailScreen`) are **stateless**, receiving immutable `UiState` objects and event lambdas via parameters. State is hoisted up to `PulseNavHost`, where `DashboardViewModel` and `DetailViewModel` manage `StateFlow` streams across configuration changes.

### Responsive Design
Using `LocalConfiguration.current.orientation`, the dashboard dynamically switches from a single-column `LazyColumn` in portrait to a two-column `LazyVerticalGrid(GridCells.Fixed(2))` in landscape, maximizing horizontal screen real estate while adhering to Material 3 typography, color roles, and `enableEdgeToEdge()` insets.
