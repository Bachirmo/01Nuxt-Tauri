# Learning Notes: Nuxt 4 + Tauri

A deliberately small educational sandbox for learning how a Nuxt 4 single-page app is packaged inside a Tauri 2 desktop window. The app is a local notes screen with two simple examples:

- Nuxt reactive state: a note, character count, word count, and clear action.
- A Nuxt-to-Rust bridge: a button invokes a Tauri command and displays the response from the native backend.

There is no database. Keeping persistence in `localStorage` makes the Nuxt/Tauri boundary the only concept that needs attention.

## How the pieces fit together

The project has two layers:

```text
Nuxt 4 app                           Tauri 2 desktop shell
──────────────────────────           ──────────────────────────
app/pages/index.vue                  src-tauri/src/lib.rs
app/assets/css/main.css       →      Rust command: greet()
.output/public (generated)           native window and packaging
```

Nuxt runs as a client-rendered SPA (`ssr: false`). `bun run generate` creates the static website in `.output/public`. Tauri embeds that directory into its native webview because `src-tauri/tauri.conf.json` sets:

```json
{
  "build": {
    "beforeBuildCommand": "bun run generate",
    "frontendDist": "../.output/public"
  }
}
```

During development, Tauri uses `http://localhost:3000` instead. Its `beforeDevCommand` starts the Nuxt dev server automatically.

## Project structure

```text
.
├── app/
│   ├── app.vue                 # Nuxt application shell
│   ├── assets/css/main.css     # Global styles
│   └── pages/index.vue         # The learning screen and reactive state
├── nuxt.config.ts              # SPA mode and static Nitro output
├── package.json                # Bun scripts and JavaScript dependencies
├── bun.lock                    # Locked Bun dependency graph
└── src-tauri/
    ├── tauri.conf.json         # Window, build paths, and bundle settings
    ├── Cargo.toml              # Rust/Tauri dependencies
    ├── build.rs                # Tauri build-time setup
    ├── capabilities/default.json# Tauri permission capability
    └── src/
        ├── lib.rs              # Native `greet` command and Tauri builder
        └── main.rs             # Desktop entry point
```

## Prerequisites

Install the following tools before running the desktop app:

1. [Bun](https://bun.sh/) for installing JavaScript dependencies and running scripts.
2. [Rust](https://www.rust-lang.org/tools/install), including `cargo`, for compiling Tauri.
3. The platform dependencies listed in the [Tauri prerequisites guide](https://v2.tauri.app/start/prerequisites/). On Windows, this includes Microsoft C++ Build Tools and WebView2.

The Nuxt website can be generated with only Bun. The Tauri desktop executable additionally needs the Rust toolchain and operating-system dependencies.

## Install dependencies

From the repository root:

```bash
bun install
```

## Run the Nuxt app in a browser

Start only the web layer:

```bash
bun run dev
```

Open the URL printed by Nuxt, normally `http://localhost:3000`. The note and counters work in the browser. The **Call native command** button intentionally explains that it needs the Tauri shell because Rust commands are not available in an ordinary browser tab.

## Run the desktop app in development

Once Rust and the Tauri prerequisites are installed:

```bash
bun run tauri dev
```

This runs `bun run dev`, opens a Tauri window pointed at the Nuxt dev server, and watches both layers for changes.

## Build the desktop application

Create the static Nuxt output and package the native app for the current platform:

```bash
bun run tauri build
```

The Tauri CLI runs `bun run generate` first because of `beforeBuildCommand`. The generated `.output/public` folder is then copied into the application bundle. Build artifacts are written under `src-tauri/target/` and are ignored by Git.

You can generate only the web output when you want to inspect it separately:

```bash
bun run generate
bun run preview
```

## Understanding the frontend-to-native bridge

The Rust side exposes a command in `src-tauri/src/lib.rs`:

```rust
#[tauri::command]
fn greet(name: &str) -> String {
    format!("Hello, {name}! This message came from Rust.")
}
```

The Nuxt page imports Tauri’s JavaScript API and invokes that command:

```ts
import { invoke } from '@tauri-apps/api/core'

const message = await invoke<string>('greet', { name: 'Nuxt' })
```

The command name (`greet`) and argument name (`name`) must match between the two layers. Tauri serializes the request from JavaScript, calls Rust, then serializes the returned `String` back to JavaScript. The `try/catch` in the page keeps the browser-only version friendly when no Tauri runtime is present.

`src-tauri/capabilities/default.json` grants the window the default core permissions. Tauri capabilities are the permission layer to revisit when adding filesystem, notification, shell, or other native APIs.

## Important configuration choices

### `nuxt.config.ts`

- `ssr: false` makes the app a client-rendered SPA, which is a straightforward fit for a desktop webview.
- `nitro.preset: 'static'` makes `nuxt generate` emit deployable static files.
- The stylesheet is registered globally so both the browser and Tauri window share the same presentation.

### `src-tauri/tauri.conf.json`

- `devUrl` points Tauri at the Nuxt development server.
- `frontendDist` points Tauri at the generated Nuxt site.
- `beforeDevCommand` and `beforeBuildCommand` connect the two build lifecycles.
- The window configuration defines the initial title and size.
- `bundle.targets: 'all'` asks Tauri to use the platform’s available bundle targets.

### `src-tauri/Cargo.toml`

This is the Rust package manifest. The `custom-protocol` feature enables Tauri’s production asset protocol when the generated Nuxt files are embedded in a release build.

## Phase commits

The history intentionally follows the requested learning roadmap:

1. `chore: initialize Nuxt 4 project with Bun`
2. `feat: add simple learning UI component and basic state`
3. `build: integrate Tauri to wrap Nuxt 4 into a desktop app`
4. `docs: add comprehensive guide explaining Nuxt and Tauri integration steps`

## Validation

The web layer has been validated with:

```bash
bun run generate
```

That command successfully built and prerendered the static Nuxt output. The Tauri CLI is installed and reports its version with:

```bash
bunx tauri --version
```

Native desktop compilation must be run on a machine with Rust and the operating-system Tauri prerequisites installed.
