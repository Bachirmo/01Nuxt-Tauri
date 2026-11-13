# Project Specification & Development Blueprint: Tauri + Nuxt 4 Learning Sandbox

## 1\. Project Overview & Objective

-   **Application Purpose:** An ultra-simple, purely educational learning sandbox designed to help the developer understand and master the integration between **Nuxt 4** and **Tauri**.
-   **App Concept:** The AI agent has full freedom to choose a minimal, straightforward concept (e.g., a simple notepad, a basic counter/calculator, or a random quote generator)—the specific domain does not matter. The core priority is **clarity, learning the architecture, and understanding how Tauri bridges a Nuxt 4 web app into a desktop window**.

## 2\. Technology Stack

-   **Framework:** Nuxt 4 (configured for SPA / static output suitable for desktop wrapping)
-   **Runtime & Package Manager:** Bun
-   **Database & ORM (Optional/Minimal):** Prisma ORM v7 with Prisma PostgreSQL Cloud (or local SQLite if simpler for a learning sandbox—let's keep it minimal and educational).
-   **Desktop Wrapper:** Tauri (the primary learning objective: packaging Nuxt into a native Windows app).

## 3\. Step-by-Step Development Roadmap & Git Commit Strategy

To ensure the process is completely transparent and easy to follow step-by-step, the AI agent must execute a `git commit` at the end of each clear development phase.

### Phase 1: Project Initialization & Nuxt 4 Setup

-   **Tasks:**
    
    -   Initialize the Nuxt 4 project using Bun.
    -   Set up a minimal layout and clean structure so the user can easily see how Nuxt runs.
-   **Git Commit Message:** `chore: initialize Nuxt 4 project with Bun`

### Phase 2: Core Learning Example UI

-   **Tasks:**
    
    -   Build a very simple, single-view educational UI (e.g., interactive state or basic feature) to serve as the application body.
-   **Git Commit Message:** `feat: add simple learning UI component and basic state`

### Phase 3: Tauri Desktop Wrapper Integration (The Core Learning Goal)

-   **Tasks:**
    
    -   Initialize Tauri within the Nuxt project.
    -   Configure Tauri settings (`tauri.conf.json`) to point to the Nuxt build output.
    -   Explain/configure how the web frontend communicates with the Tauri native window/backend if needed.
-   **Git Commit Message:** `build: integrate Tauri to wrap Nuxt 4 into a desktop app`

### Phase 4: Documentation & Learning Explanations (`README.md`)

-   **Tasks:**
    
    -   Write a detailed `README.md` explaining _how_ Tauri and Nuxt 4 were connected, what each configuration file does, and how to run/build the project step-by-step using Bun.
-   **Git Commit Message:** `docs: add comprehensive guide explaining Nuxt and Tauri integration steps`