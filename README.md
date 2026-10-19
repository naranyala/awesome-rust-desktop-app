# Awesome Rust Desktop App Development

> A curated catalog of frameworks, libraries, tools, and resources for building desktop applications with Rust — organized by architectural layer.

---

## Table of Contents

- [GUI Frameworks](#gui-frameworks)
  - [WebView-Based](#webview-based)
  - [Native — Immediate Mode](#native--immediate-mode)
  - [Native — Retained / Declarative](#native--retained--declarative)
  - [Bindings to Existing Toolkits](#bindings-to-existing-toolkits)
  - [Platform-Specific](#platform-specific)
- [Architecture Patterns](#architecture-patterns)
- [State Management](#state-management)
- [Async Runtimes](#async-runtimes)
- [System Integration APIs](#system-integration-apis)
- [Data Persistence](#data-persistence)
- [Serialization & Configuration](#serialization--configuration)
- [Logging & Diagnostics](#logging--diagnostics)
- [Error Handling](#error-handling)
- [Testing](#testing)
- [Benchmarking & Profiling](#benchmarking--profiling)
- [Build, Packaging & Distribution](#build-packaging--distribution)
- [Cross-Compilation](#cross-compilation)
- [Known Desktop Apps Built with Rust](#known-desktop-apps-built-with-rust)
- [Framework Selection Guide](#framework-selection-guide)
- [Learning Resources](#learning-resources)
- [Community & Ecosystem Tracking](#community--ecosystem-tracking)

---

## GUI Frameworks

### WebView-Based

These embed a system WebView (WebKitGTK on Linux, WebView2 on Windows, WebKit on macOS) and route UI rendering through HTML/CSS. Rust handles the backend logic via an IPC bridge.

| Project | License | min Rust | Latest Stable | GitHub Stars | Platforms | Binary Size | Notes |
|---------|---------|----------|---------------|--------------|-----------|-------------|-------|
| [Tauri](https://tauri.app/) | MIT/Apache-2.0 | 1.77 | 2.x | 105K+ | Win / macOS / Linux | ~2.5–3 MB (empty app) | Uses system WebView. Capabilities-based security. |
| [Dioxus](https://dioxuslabs.com/) | MIT | 1.79 | 0.7.x (0.7.2: Dec 2025) | 36K | Win / macOS / Linux / Mobile | ~5–10 MB | `rsx!` macro + signals API. Desktop via `dioxus-desktop`. |
| [Wry](https://github.com/tauri-apps/wry) | MIT/Apache-2.0 | — | stable | — | Win / macOS / Linux | — | WebView abstraction layer used by Tauri. Usable standalone. |
| [Neutralinojs](https://neutralino.js.org/) | MIT | — | 5.x | — | Win / macOS / Linux | — | Not Rust-native; Rust backend via plugin system. |
| [Leptos](https://leptos.dev/) | MIT | 1.75 | 0.7.x | 10K+ | Win / macOS / Linux / Web | — | Fullstack Rust. SSR + WASM. Can run desktop via Tauri. |
| [Yew](https://yew.rs/) | MIT/Apache-2.0 | 1.75 | 0.21.x | 26K | Win / macOS / Linux / Web | — | Component-based. Can run desktop via Tauri. |

### Native — Immediate Mode

UI is redrawn every frame. No retained widget tree. Trade-off: constant CPU activity from per-frame repaint.

| Project | License | min Rust | Latest Stable | Crates.io Downloads | Renderer | Platforms | Notes |
|---------|---------|----------|---------------|---------------------|----------|-----------|-------|
| [egui](https://github.com/emilk/egui) + [eframe](https://github.com/emilk/egui/tree/master/crates/eframe) | MIT/Apache-2.0 | 1.81 | 0.33.x (Dec 2025) | ~13M total | wgpu / glow | Win / macOS / Linux / Web | Immediate mode. Each widget computes in µs range for 60 FPS. |
| [iced](https://github.com/iced-rs/iced) | MIT | 1.75 | 0.13.x | ~1.3M | wgpu | Win / macOS / Linux | Elm-style update loop. Retained widget tree on wgpu renderer. |
| [makepad](https://github.com/makepad/makepad) | MIT/Apache-2.0 | — | 0.1.x | — | Custom OpenGL | Win / macOS / Linux | Custom UI DSL via proc macros. Limited documentation. |

### Native — Retained / Declarative

UI is a persistent data structure. Framework diffs state changes. Lower idle CPU usage than immediate mode.

| Project | License | min Rust | Latest Stable | Crates.io Downloads | Renderer | Platforms | Notes |
|---------|---------|----------|---------------|---------------------|----------|-----------|-------|
| [Slint](https://slint.dev/) | GPL-3.0 / Commercial | 1.75 | 1.9.x | ~1.3M | Skia / FemtoVG / Software | Win / macOS / Linux / Embedded | Declarative `.slint` DSL. Two-way binding (`<=>`). Free tier. |
| [Xilem](https://github.com/linebender/xilem) | Apache-2.0 | — | pre-1.0 | — | wgpu (via Masonry) | Win / macOS / Linux | Built on Masonry widget layer. Functional reactive. No stable release. |
| [Masonry](https://github.com/linebender/xilem/tree/main/masonry) | Apache-2.0 | — | pre-1.0 | — | wgpu | Win / macOS / Linux | Widget primitives for Xilem. Not standalone. |
| [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) | GPL-3.0 (in Zed) | — | 0.2.x (Oct 2025) | — | GPU-accelerated (wgpu) | Win / macOS / Linux | Developed for Zed editor. Published on crates.io 2025-10. |
| [Freya](https://github.com/marc2332/freya) | MIT | — | 0.4.x | — | Skia (via Dioxus renderer) | Win / macOS / Linux | Dioxus syntax with native Skia rendering. |
| [Cushy](https://github.com/evanrelf/cushy) | MIT | — | 0.1.x | — | wgpu | Win / macOS / Linux | Pure Rust. Dynamic API. No screen reader support. |
| [Floem](https://github.com/lapce/floem) | MIT | — | — | — | wgpu | Win / macOS / Linux | Built for Lapce editor. Signal-driven. No IME/screen reader support. |
| [Vizia](https://github.com/geom3trik/Vizia) | MIT/Apache-2.0 | — | 0.10.x | — | wgpu (via femtovg) | Win / macOS / Linux | ECS-based data flow. Early stage. |
| [Druid](https://github.com/linebender/druid) | MIT/Apache-2.0 | 1.75 | 0.12.x (maintenance) | — | wgpu | Win / macOS / Linux | Data-oriented GUI. In maintenance mode. |

### Bindings to Existing Toolkits

Rust wrappers around mature C/C++ GUI toolkits. Inherits the toolkit's widget set, accessibility, and platform integration.

| Project | License | min Rust | Latest Stable | Crates.io Downloads | Toolkit | Platforms | Notes |
|---------|---------|----------|---------------|---------------------|---------|-----------|-------|
| [gtk-rs (GTK 4)](https://gtk-rs.org/) | MIT | 1.75 | 0.18+ | — | GTK 4 | Win / macOS / Linux | Official GNOME bindings. Property-based binding. |
| [Relm4](https://relm4.org/) | MIT/Apache-2.0 | 1.75 | 0.9.x | — | GTK 4 (via gtk-rs) | Win / macOS / Linux | Elm-style wrapper over GTK 4. Used in Pop!_OS COSMIC. |
| [libcosmic](https://github.com/pop-os/libcosmic) | GPL-3.0 | — | — | — | iced fork | Linux | COSMIC desktop by System76. Powers Pop!_OS 24.04+. |
| [fltk-rs](https://github.com/fltk-rs/fltk-rs) | MIT | — | 2.0.x | — | FLTK (C++) | Win / macOS / Linux | Lightweight. Requires `fltk-boundgen` for screen readers. |
| [CXX-Qt](https://cxx-qt.io/) | MIT/Apache-2.0 | — | 0.7.x | — | Qt 5/6 (C++) | Win / macOS / Linux | Requires Qt dev headers. Type-safe Rust/Qt interop. |

### Platform-Specific

| Project | License | Platforms | Notes |
|---------|---------|-----------|-------|
| [WinSafe](https://github.com/douglascamata/winsafe) | MIT | Windows only | High-level Win32 GUI wrappers. Full IME/screen reader. Manual widget positioning. |
| [cocoa-rs](https://crates.io/crates/cocoa) | MIT/Apache-2.0 | macOS only | Low-level Objective-C Cocoa bindings. |
| [objc2](https://github.com/madsmtm/objc2) | MIT | macOS only | Modern ObjC interop from Rust. Used by winit, sctk. |

---

## Architecture Patterns

Patterns used to structure desktop applications in Rust.

| Pattern | Description | Adopted By |
|---------|-------------|------------|
| **Elm Architecture (TEA)** | `Model → Update → View` loop. Single state struct. Messages trigger state transitions. | iced, Relm4, Xilem |
| **Signal-Based Reactivity** | Fine-grained reactive primitives (`Signal<T>`, `ReadSignal`, `WriteSignal`). UI subscribes to specific signals. | Dioxus, GPUI, Floem, leptos |
| **Redux / Command Reducer** | Centralized store. State changes via pure reducer functions. Middleware for side effects. | Custom on any framework |
| **MVVM** | View binds to ViewModel with observable properties. Common with Slint and GTK. | Slint, gtk-rs |
| **Immediate Mode** | No retained state in UI layer. Every frame, UI code runs, reads current state, emits events. | egui |
| **Component-Based ECS** | Entities, Components, Systems. UI elements as entities; data as components. | Vizia |

---

## State Management

Libraries for managing application state, reactivity, and data flow.

| Project | License | Pattern | Notes |
|---------|---------|---------|-------|
| [generational-arena](https://crates.io/crates/generational-arena) | MIT | Arena allocation | Stable indices for UI elements. Used by ECS. |
| [slotmap](https://crates.io/crates/slotmap) | BSD-2 | Keyed storage | Efficient handle-based storage. |
| [reactive-graph](https://crates.io/crates/reactive-graph) | MIT | Signal graph | Fine-grained reactive dependency tracking. |
| [signal-hook](https://crates.io/crates/signal-hook) | Apache-2.0/MIT | Unix signals | OS-level signal handling (SIGINT, SIGHUP). |
| [tokio::sync::watch](https://docs.rs/tokio) | MIT | Broadcast channel | Single-producer multi-consumer state broadcasting. |
| [futures::stream](https://docs.rs/futures) | Apache-2.0/MIT | Stream | Async event streams for UI. |
| [state](https://crates.io/crates/state) | MIT | Thread-local | Per-thread state storage. |

---

## Async Runtimes

Event loops and async executors. Desktop apps typically run WebView or UI event loop on main thread; background I/O on async runtime.

| Project | License | Executor Model | crates.io Downloads | Notes |
|---------|---------|---------------|---------------------|-------|
| [Tokio](https://tokio.rs/) | MIT | Work-stealing, multi-threaded | ~58M | De facto standard. Large ecosystem. |
| [async-std](https://async.rs/) | Apache-2.0/MIT | Thread-per-core available | ~12M | API mirrors `std`. |
| [smol](https://crates.io/crates/smol) | Apache-2.0/MIT | Single-threaded by default | ~3M | Minimal binary footprint. |
| [futures](https://crates.io/crates/futures) | Apache-2.0/MIT | Combinators only | ~75M | Not a runtime. Provides `Stream`, `Sink`, `join!`, `select!`. |
| [futures-executor](https://crates.io/crates/futures-executor) | Apache-2.0/MIT | Blocking thread pool | — | Minimal executor for simple cases. |
| [glommio](https://github.com/DataDog/glommio) | Apache-2.0/MIT | IO-uring based | — | Linux-specific. High throughput. |

**Integration note:** WebView-based frameworks (Tauri, Dioxus) embed their own async runtime. Native frameworks (egui, iced) run on `winit` event loop and require explicit bridging to Tokio/async-std for background tasks.

---

## System Integration APIs

Libraries for OS-level features: file dialogs, clipboard, notifications, system tray, directory paths.

### File & Dialog APIs

| Project | License | Platforms | Notes |
|---------|---------|-----------|-------|
| [rfd](https://github.com/PolyMeilex/rfd) | LGPL-3.0 | Win / macOS / Linux | Native file picker, save dialog, message dialog. |
| [native-dialog](https://crates.io/crates/native-dialog) | MIT | Win / macOS / Linux | File open/save, message box, color picker. |
| [tinyfiledialogs](https://crates.io/crates/tinyfiledialogs) | Zlib | Win / macOS / Linux | Minimal C dependency. Single-header. |
| [tauri-plugin-dialog](https://github.com/tauri-apps/plugins) | MIT/Apache-2.0 | Win / macOS / Linux | Tauri dialogs plugin. |

### Clipboard

| Project | License | Platforms | Data Types |
|---------|---------|-----------|------------|
| [arboard](https://crates.io/crates/arboard) | MIT | Win / macOS / Linux | Text, image (RGBA), HTML. |
| [clipboard-rs](https://crates.io/crates/clipboard-rs) | MIT | Win / macOS / Linux | Text, image, rich text, HTML, files, change monitoring. |
| [tauri-plugin-clipboard-manager](https://github.com/tauri-apps/plugins) | MIT/Apache-2.0 | Win / macOS / Linux | Tauri clipboard plugin. |

### System Tray & Notifications

| Project | License | Platforms | Notes |
|---------|---------|-----------|-------|
| [tray-icon](https://github.com/tauri-apps/tray-icon) | MIT/Apache-2.0 | Win / macOS / Linux | Menu items, tooltips, icon updates. |
| [system-tray](https://crates.io/crates/system-tray) | MIT | Win / macOS / Linux | System tray with menu. |
| [stray](https://crates.io/crates/stray) | MIT | Win / macOS / Linux | Minimal system tray. |
| [notify](https://crates.io/crates/notify) | CC0-1.0 | Win / macOS / Linux | Filesystem event watcher. |
| [notify-rust](https://crates.io/crates/notify-rust) | Apache-2.0/MIT | Win / macOS / Linux | Desktop notifications (DBus on Linux, WinRT on Windows). |
| [tauri-plugin-notification](https://github.com/tauri-apps/plugins) | MIT/Apache-2.0 | Win / macOS / Linux | Tauri notifications plugin. |

### Directory & System Info

| Project | License | Notes |
|---------|---------|-------|
| [dirs](https://crates.io/crates/dirs) | MIT | Config, cache, data, home per platform. |
| [directories](https://crates.io/crates/directories) | MIT/Apache-2.0 | Extended `dirs` with project-specific paths. |
| [open](https://crates.io/crates/open) | MIT | Open file/URL in default app. |
| [whoami](https://crates.io/crates/whoami) | MIT/Apache-2.0 | Username, hostname, OS type, arch. |
| [sysinfo](https://crates.io/crates/sysinfo) | MIT | Process list, CPU, memory, disk, network stats. |

### Native OS Wrappers

| Project | License | Platforms | Notes |
|---------|---------|-----------|-------|
| [windows-rs](https://github.com/microsoft/windows-rs) | MIT/Apache-2.0 | Windows | Official Microsoft bindings. Auto-generated. |
| [objc2](https://github.com/madsmtm/objc2) | MIT | macOS | Objective-C runtime interop. |
| [nix](https://crates.io/crates/nix) | MIT | Unix-like | POSIX API wrappers. |
| [libloading](https://crates.io/crates/libloading) | ISC | Win / macOS / Linux | Dynamic library loading. |
| [dbus](https://crates.io/crates/dbus) | MIT | Linux | D-Bus IPC. |
| [zbus](https://crates.io/crates/zbus) | MIT | Linux | Async D-Bus client. |

---

## Data Persistence

### SQLite & Embedded Databases

| Project | License | Type | Async | Notes |
|---------|---------|------|-------|-------|
| [rusqlite](https://github.com/rusqlite/rusqlite) | MIT | SQLite wrapper | No (sync) | Most-used SQLite binding. Parameterized queries. |
| [libsqlite3-sys](https://crates.io/crates/libsqlite3-sys) | MIT | Bindgen | — | Low-level SQLite3 FFI. |
| [SQLx](https://github.com/launchbadge/sqlx) | Apache-2.0/MIT | Compile-time checked queries | Yes | Supports SQLite, PostgreSQL, MySQL. `sqlx::query!` validates SQL. |
| [SeaORM](https://github.com/SeaQL/sea-orm) | Apache-2.0/MIT | ORM on SQLx | Yes | Entity-based. Migration support. |
| [Diesel](https://diesel.rs/) | Apache-2.0/MIT | ORM / Query builder | No (sync) | Compile-time query validation. Schema inference. |
| [Sled](https://github.com/spacejam/sled) | MIT/Apache-2.0 | Embedded key-value | No (sync) | Pure Rust. ACID. Drop-in for `HashMap`. |
| [RocksDB (rust-rocksdb)](https://github.com/rust-rocksdb/rust-rocksdb) | Apache-2.0/MIT | Embedded LSM-tree | No (sync) | Facebook's RocksDB. Requires C++ toolchain. |
| [Redb](https://github.com/cberner/redb) | MIT | Embedded ACID | No (sync) | Pure Rust. MVCC. B+tree storage. |
| [Heed](https://crates.io/crates/heed) | MIT | LMDB wrapper | No (sync) | Lightning Memory-Mapped Database. |
| [Stoolap](https://stoolap.io/) | MIT | Embedded SQL | No (sync) | Pure Rust. ACID, HNSW indexes, vector search. |

### Key-Value / NoSQL

| Project | License | Type | Notes |
|---------|---------|------|-------|
| [sled](https://github.com/spacejam/sled) | MIT/Apache-2.0 | Key-value | Pure Rust embedded. |
| [redb](https://github.com/cberner/redb) | MIT | Key-value | Pure Rust ACID. |
| [surrealdb](https://surrealdb.com/) | Apache-2.0 | Multi-model | In-memory or on-disk. SQL + graph queries. |

### File-Based Storage

| Project | License | Format | Notes |
|---------|---------|--------|-------|
| [serde_json](https://crates.io/crates/serde_json) | Apache-2.0/MIT | JSON | Standard parsing/serialization. |
| [bincode](https://crates.io/crates/bincode) | MIT | Binary | Compact binary format. |
| [rmp-serde](https://crates.io/crates/rmp-serde) | MIT | MessagePack | Smaller than JSON. |
| [csv](https://crates.io/crates/csv) | MIT/Unlicense | CSV | High-performance via serde. |
| [toml](https://crates.io/crates/toml) | Apache-2.0/MIT | TOML | Config files. |

---

## Serialization & Configuration

| Project | License | Purpose | Notes |
|---------|---------|---------|-------|
| [serde](https://serde.rs/) | Apache-2.0/MIT | Serialization framework | `#[derive(Serialize, Deserialize)]`. 20+ formats. |
| [serde_json](https://crates.io/crates/serde_json) | Apache-2.0/MIT | JSON | |
| [toml](https://crates.io/crates/toml) | Apache-2.0/MIT | TOML | Config files. |
| [serde_yaml](https://crates.io/crates/serde_yaml) | Apache-2.0/MIT | YAML | |
| [config](https://crates.io/crates/config) | Apache-2.0/MIT | Hierarchical config | Merge file, env, CLI, defaults. |
| [confy](https://crates.io/crates/confy) | Apache-2.0/MIT | Zero-boilerplate config | One-liner load/save. |
| [figment](https://crates.io/crates/figment) | Apache-2.0/MIT | Configuration | Profile-based, validation-integrated. |
| [dotenv](https://crates.io/crates/dotenv) | MIT | .env files | Environment variables. |

---

## Logging & Diagnostics

| Project | License | Purpose | Notes |
|---------|---------|---------|-------|
| [tracing](https://crates.io/crates/tracing) | MIT | Structured event tracing | Span-based context propagation. |
| [tracing-subscriber](https://crates.io/crates/tracing-subscriber) | MIT | Subscriber implementation | EnvFilter, fmt, json. |
| [tracing-appender](https://crates.io/crates/tracing-appender) | MIT | File appenders | Rolling file writer. |
| [env_logger](https://crates.io/crates/env_logger) | Apache-2.0/MIT | Env-controlled logger | `RUST_LOG=debug cargo run`. |
| [log](https://crates.io/crates/log) | Apache-2.0/MIT | Logging facade | Macro layer. Needs backend. |
| [fern](https://crates.io/crates/fern) | MIT | Logger combinator | Multiple destinations. |
| [slog](https://crates.io/crates/slog) | MIT | Structured logging | Hierarchical loggers. |

---

## Error Handling

| Project | License | Purpose | Notes |
|---------|---------|---------|-------|
| [thiserror](https://crates.io/crates/thiserror) | Apache-2.0/MIT | `#[derive(Error)]` for libs | Zero-cost. Generates `std::error::Error`. |
| [anyhow](https://crates.io/crates/anyhow) | Apache-2.0/MIT | Application error type | `Result<T, anyhow::Error>` + `?` propagation. |
| [miette](https://crates.io/crates/miette) | Apache-2.0/MIT | Diagnostic reporting | rustc-style error output. |
| [eyre](https://crates.io/crates/eyre) | Apache-2.0/MIT | anyhow alternative | Customizable. |

---

## Testing

| Project | License | Purpose | Notes |
|---------|---------|---------|-------|
| [cargo test](https://doc.rust-lang.org/cargo/commands/cargo-test.html) | Built-in | Unit/integration | `#[cfg(test)]` modules. |
| [insta](https://insta.rs/) | Apache-2.0/MIT | Snapshot testing | Golden-file testing. UI regression. |
| [assert_cmd](https://crates.io/crates/assert_cmd) | Apache-2.0/MIT | CLI testing | stdout/stderr/exit code. |
| [cucumber](https://crates.io/crates/cucumber) | MIT | BDD / Gherkin | Given/When/Then. |
| [googletest](https://crates.io/crates/googletest) | Apache-2.0/MIT | Assertions | Google Test-style matchers. |
| [proptest](https://crates.io/crates/proptest) | Apache-2.0/MIT | Property-based testing | Shrinking, arbitrary input. |
| [quickcheck](https://crates.io/crates/quickcheck) | MIT/Apache-2.0 | Property testing | QuickCheck-style. |
| [mockall](https://crates.io/crates/mockall) | Apache-2.0/MIT | Mock generation | `#[automock]` generates mock traits. |
| [tempfile](https://crates.io/crates/tempfile) | Apache-2.0/MIT | Temp file/dir | Test fixtures. |

---

## Benchmarking & Profiling

| Project | License | Purpose | Notes |
|---------|---------|---------|-------|
| [criterion](https://crates.io/crates/criterion) | Apache-2.0/MIT | Statistical benchmarking | Warmup, iteration, statistical analysis. |
| [divan](https://crates.io/crates/divan) | MIT/Apache-2.0 | Benchmarking | Macro-based, low overhead. |
| [cargo-flamegraph](https://github.com/flamegraph-rs/flamegraph) | MIT | CPU profiling | Requires `perf` (Linux), DTrace (macOS). |
| [samply](https://github.com/mstange/samply) | Apache-2.0/MIT | CPU profiler | Firefox Profiler-compatible output. |
| [heaptrack](https://github.com/KDE/heaptrack) | GPL-2.0 | Heap profiling | Linux. Works with Rust binaries. |
| [DHAT](https://valgrind.org/docs/manual/dh-manual.html) | GPL | Heap analysis | Via Valgrind. Linux only. |
| [coz](https://github.com/plasma-umass/coz) | Apache-2.0 | Causal profiler | Identifies throughput-affecting code. |
| [perf](https://perf.wiki.kernel.org/) | GPL-2.0 | Linux HW counters | CPU cycles, cache misses. |

---

## Build, Packaging & Distribution

| Project | License | Output Formats | Platforms | Notes |
|---------|---------|-----------------|-----------|-------|
| [cargo-bundle](https://github.com/burtonageo/cargo-bundle) | MIT | `.app`, `.dmg`, `.exe`, `.msi`, `.deb` | Win / macOS / Linux | Basic bundler. |
| [cargo-packager](https://github.com/nicegram/cargo-packager) | MIT | `.deb`, `.rpm`, `.dmg`, `.msi`, `.exe`, AppImage | Win / macOS / Linux | Configurable via `Cargo.toml`. |
| [Tauri CLI](https://tauri.app/) | Apache-2.0/MIT | Platform-native bundles | Win / macOS / Linux | Tauri projects only. |
| [cargo-wix](https://crates.io/crates/cargo-wix) | MIT | `.msi` (via WiX) | Windows | Requires WiX Toolset. |
| [cargo-deb](https://crates.io/crates/cargo-deb) | MIT | `.deb` | Linux (Debian) | From `Cargo.toml` metadata. |
| [cargo-rpm](https://crates.io/crates/cargo-rpm) | MIT | `.rpm` | Linux (RHEL/Fedora/SUSE) | From `Cargo.toml` metadata. |
| [AppImage](https://appimage.org/) | MIT | AppImage | Linux | Use with linuxdeploy. |
| [cargo-about](https://crates.io/crates/cargo-about) | Apache-2.0/MIT | License report | All | 3rd-party license notices. |
| [tauri-plugin-updater](https://crates.io/crates/tauri-plugin-updater) | Apache-2.0/MIT | OTA updates | Win / macOS / Linux | Auto-update for Tauri. |

---

## Cross-Compilation

| Project | License | Target Platforms | Notes |
|---------|---------|-----------------|-------|
| [cross](https://github.com/cross-rs/cross) | Apache-2.0/MIT | All Tier 1/2 | Docker-based. Pre-built sysroots. |
| [cargo-xwin](https://crates.io/crates/cargo-xwin) | Apache-2.0/MIT | Windows (from Linux/macOS) | Uses `xwin` sysroot. No Docker. |
| [osxcross](https://github.com/tpoechtrager/osxcross) | BSD-2 | macOS (from Linux) | Requires macOS SDK. |

---

## Known Desktop Apps Built with Rust

Publicly documented desktop/client applications shipping to end users.

| Application | Type | Framework / Stack | Developer |
|-------------|------|-------------------|-----------|
| [Glim](https://github.com/NeoPanic/Glim) | File viewer and basic text editor (v0.2.5, MIT) | Rust + GPUI Kit, Swift native previews; macOS only | NeoPanic |
| [Zed](https://zed.dev/) | Code editor | GPUI (custom) | Zed Industries |
| [Lapce](https://lapce.dev/) | Code editor | Floem (custom) | Lapce contributors |
| [Synergy](https://symless.com/synergy) | Mouse/keyboard sharing | Custom Rust native | Symless |
| [AFFiNE](https://affine.pro/) | Knowledge base | OctoBase + web frontend | ToEverything |
| [Thunderbird](https://www.thunderbird.net/) (partial) | Email client | Being ported from C++ | Mozilla |
| [Mullvad VPN](https://mullvad.net/) | VPN client | Custom Rust native | Mullvad |
| [RustDesk](https://rustdesk.com/) | Remote desktop | Custom (formerly sciter) | Purslane Ltd |
| [Pop!_OS COSMIC](https://system76.com/cosmic) | Desktop environment | libcosmic (iced fork) | System76 |
| [PrusaSlicer](https://github.com/prusa3d/PrusaSlicer) (components) | 3D printing slicer | Rust in C++ app | Prusa Research |
| [Alva](https://alva-retro.com/) | Retro console emulator | Custom | Alva team |

---

## Framework Selection Guide

This section maps application requirements to framework characteristics.

### By Binary Size Constraint

| Constraint | Frameworks | Rationale |
|------------|-----------|-----------|
| < 5 MB | Tauri (~2.5–3 MB), egui+eframe (~8–15 MB) | WebView-based minimal Rust binary. Native includes renderer. |
| No constraint | All | Slint, iced, GTK 4, GPUI produce larger binaries. |

### By Memory Budget (Typical)

| Constraint | Frameworks | Measured RAM |
|------------|-----------|---------------|
| < 50 MB idle | Tauri | 30–40 MB (basic app) |
| < 80 MB idle | egui | 62–68 MB (with GPU buffers) |
| No constraint | All | |

### By Power / Battery (Laptop)

| Constraint | Frameworks | Rationale |
|------------|-----------|-----------|
| Low idle CPU | Slint, iced, GTK 4, Xilem | Retained mode: render on state change only. |
| Higher idle OK | egui, GPUI | Immediate mode repaints every frame (60 Hz). |

### By Accessibility (Screen Reader)

| Requirement | Frameworks | Notes |
|-------------|-----------|-------|
| Full support | Tauri (WebView a11y), Slint, WinSafe | Depends on platform backend. |
| Limited/none | egui, iced, GPUI, Floem | |

### By IME Support (CJK Input)

| Requirement | Frameworks | Notes |
|-------------|-----------|-------|
| Full support | Tauri, Slint, Dioxus, WinSafe, CXX-Qt | Tested with Japanese/Chinese/Korean. |
| Issues known | egui (Tab key), iced (activation), GTK 4 (Windows partial) | |

### By Platform Coverage

| Target | Frameworks |
|--------|-----------|
| Win + macOS + Linux | Tauri, Dioxus, egui, iced, Slint, GPUI, Xilem, GTK 4, fltk-rs |
| Linux only | libcosmic, Relm4 |
| Windows only | WinSafe |
| macOS only | cocoa-rs |
| + Mobile (iOS/Android) | Dioxus, egui (via `egui-mobile`), Slint (embedded) |
| + Web (WASM) | egui, Dioxus, Slint, Yew, Leptos |

### By Licensing

| License | Frameworks |
|---------|-----------|
| MIT/Apache-2.0 only | egui, iced, Tauri, Dioxus, GPUI (GPL-3.0 in Zed) |
| LGPL acceptable | GTK 4 |
| GPL acceptable | libcosmic, Slint (free tier available) |

### By Team Background

| Background | Frameworks | Rationale |
|------------|-----------|-----------|
| Web (React/JSX) | Dioxus, Tauri | JSX-like `rsx!` or web frontend. |
| Mobile (SwiftUI/Compose) | Slint, Xilem | Declarative DSL or functional reactive. |
| Systems | GPUI, egui, iced | Direct Rust, minimal layers. |
| Desktop (Qt/WPF) | CXX-Qt, Slint, GTK 4/Relm4 | Widget toolkit patterns. |

---

## Learning Resources

### Official Documentation

| Resource | Type | URL |
|----------|------|-----|
| The Rust Book | Book | https://doc.rust-lang.org/book/ |
| Rust by Example | Examples | https://doc.rust-lang.org/rust-by-example/ |
| Rustonomicon | Book | https://doc.rust-lang.org/nomicon/ |
| Async Rust Book | Book | https://rust-lang.github.io/async-book/ |
| Rust Design Patterns | Patterns | https://rust-unofficial.github.io/patterns/ |
| Tauri Documentation | Docs | https://tauri.app/ |
| Dioxus Guide | Docs | https://dioxuslabs.com/learn/ |
| egui Documentation | Docs | https://docs.rs/egui/ |
| Iced Guide | Docs | https://docs.rs/iced/ |
| Slint Documentation | Docs | https://slint.dev/docs/ |
| Relm4 Book | Docs | https://relm4.org/ |
| GTK 4 Rust API | Docs | https://gtk-rs.org/ |
| GPUI Source | Source | https://github.com/zed-industries/zed/tree/main/crates/gpui |

### Surveys & Analysis

| Resource | Description | URL |
|----------|-------------|-----|
| 2025 Survey of Rust GUI Libraries | Comprehensive survey by boringcactus | https://www.boringcactus.com/2025/04/13/2025-survey-of-rust-gui-libraries.html |
| Rust GUI Observations (2026) | Analysis from production use | https://tritium.legal/blog/desktop |
| State of Rust GUI (2025) | Ecosystem analysis | https://weeklyrust.substack.com/p/the-state-of-rust-gui-the-good-and |
| Tauri vs Dioxus Comparison | Framework comparison | https://medium.com/solo-devs/tauri-vs-dioxus-the-ultimate-rust-showdown-5d8d305497d6 |

---

## Community & Ecosystem Tracking

| Resource | Type | URL |
|----------|------|-----|
| Rust Users Forum | Forum | https://users.rust-lang.org/ |
| This Week in Rust | Newsletter | https://this-week-in-rust.org/ |
| Are We GUI Yet? | Tracker | https://www.areweguiyet.com/ |
| Are We Game Yet? | Tracker | https://arewegameyet.rs/ |
| Rust in Production | Company list | https://github.com/ImplFerris/rust-in-production |
| Awesome Rust | Curated list | https://github.com/LiveDuo/awesome-rust-2 |
| Lib.rs GUI | Package index | https://lib.rs/gui |
| crates.io | Registry | https://crates.io/ |

---

## Ecosystem Maturity Indicators

As of early 2026.

| Framework | Stable Release | First Release | Production Apps | Crates.io Downloads |
|-----------|----------------|---------------|-----------------|---------------------|
| egui | 0.33.x (semver-stable) | 2019 | Several | ~13M |
| iced | 0.13.x (pre-1.0) | 2019 | libcosmic fork | ~1.3M |
| Tauri | 2.x (stable) | 2020 | Multiple | — |
| Slint | 1.9.x (stable) | 2020 | Embedded + desktop | ~1.3M |
| GTK 4 (gtk-rs) | 0.18+ (stable) | 2015 | GNOME apps | — |
| Relm4 | 0.9.x (pre-1.0) | 2021 | COSMIC components | — |
| GPUI | 0.2.x (pre-1.0) | 2023 | Zed | — |
| Dioxus | 0.7.x (pre-1.0) | 2021 | Several | — |
| Xilem | pre-1.0 | 2023 | None public | — |
| Floem | unreleased | 2023 | Lapce | — |

---

## Contributing

Contributions are welcome. Ensure:

1. Entry is relevant to **desktop application development** in Rust.
2. Include: project name, link, license, brief description, current version/status.
3. Avoid subjective claims ("best", "blazing fast") — use measurable data (versions, downloads, licenses).
4. Place entries in the most specific applicable category.

---

## License

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](https://creativecommons.org/publicdomain/zero/1.0/)

To the extent possible under law, the author(s) have dedicated all copyright and related rights to the public domain worldwide.
