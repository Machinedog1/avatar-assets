# Agriponic Farms Unity Game Engine Design

## Vision and Player Fantasy
Agriponic Farms is a calm-but-progressive farming simulation that lets players master high-tech agriponics, automate production, and deliver sustainable food to growing cities. The Unity engine foundation focuses on modular gameplay systems that can scale from solo play to small co-op sessions without compromising performance on mid-tier hardware.

## Core Gameplay Loop
1. **Plan** – Survey plots, choose nutrient recipes, and set up irrigation and lighting plans.
2. **Plant** – Seed beds or NFT channels, configure environmental targets, and schedule harvest windows.
3. **Tend** – Monitor dashboards, adjust pumps/lights/fans, and deploy drones for pest control.
4. **Harvest & Deliver** – Package produce, fulfill contracts, and upgrade tech.
5. **Expand** – Reinvest profits into new greenhouses, research, and logistics capacity.

## Major Systems
- **World Streaming & Grid Management** – Chunked grid with quad-tree visibility to keep greenhouses performant. Each greenhouse uses a local coordinate space for placement, with object pooling for frequently spawned props (pipes, trays, lights).
- **Crop Lifecycle System** – ScriptableObject definitions per crop (growth stages, mesh/texture sets, nutrient curves, min/max environment tolerances). Uses `IUpdateable` tick manager to drive growth with time dilation for sleeping/background play.
- **Environment Simulation** – Modular controllers for temperature, humidity, CO₂, airflow, light spectra, and nutrient solution EC/pH. Each controller exposes PID loops and pluggable sensor noise models for realism.
- **Automation Graph** – Node-based automation UI (Blueprint-style) that lets players create rules: triggers (sensor thresholds, schedules) → conditions → actions (pump RPM, valve state, lighting preset, alert). Serialized as JSON for saving/sharing.
- **Logistics & Contracts** – Request boards generate dynamic contracts based on city demand, seasonality, and reputation. Includes cold-chain simulation (temperature drift, spoilage) during delivery mini-games.
- **Research & Technology Tree** – Unlocks better substrates, LEDs, nutrient blends, drones, and autonomous forklifts. Buffs tie back into environment simulation and logistics systems.
- **Economy & Pricing** – Elastic pricing influenced by market demand and supply saturation. Includes fixed costs (energy, water, labor) to encourage efficiency optimizations.
- **Accessibility & UX** – Diegetic tablets for dashboards, colorblind-safe palettes, and haptic-friendly controls. Key flows (planting, recipe tuning, automation) have quick tutorials.

## Scene & Content Structure
- **Master Scene** – Contains lighting, skybox, shared VFX pools, global audio, and persistent managers (save/load, analytics, event bus).
- **Greenhouse Scene Additives** – Loaded additively per facility. Uses baked GI where possible with dynamic overlays for grow lights.
- **Interactables** – Trays, pumps, tanks, sensors, and drones implement a `FarmInteractable` interface (hover outline, context actions, tooltip data source).
- **NPCs & Co-op** – Lightweight NPC workers with behavior trees; co-op uses Unity Netcode for GameObjects with lag-compensated interactions and deterministic automation graph evaluation.

## Data & Persistence
- **Save Format** – Compressed JSON with binary blobs for terrain/voxel edits and automation graphs. Versioned save headers with migration scripts.
- **Telemetry** – Optional privacy-friendly analytics (session length, mission completion, tutorial success) gated behind consent.
- **Modding Hooks** – Addressable asset catalogs for crops, props, and VFX; supports Workshop-style downloads with signature validation.

## Key Technical Pillars
- **Performance Targets** – 60 FPS on mid-tier GPU; GPU instancing for crops; Jobs + Burst for environment calculations; async scene loading.
- **Tooling** – Custom Unity Editor windows for crop definition authoring, automation graph editing, and greenhouse layout previews. Includes playmode smoke tests for growth-cycle regressions.
- **Build & CI** – Unity Cloud Build or GitHub Actions with platform matrices (PC/Mac/Linux). Lint with Rider/roslyn analyzers, static memory leak checks, and automated Addressables bundle validation.

## Roadmap (90-Day)
- **Milestone 1 (Weeks 1–4)** – Graybox greenhouse, crop lifecycle MVP (3 crops), basic environment controllers, save/load scaffold, and offline economy stub.
- **Milestone 2 (Weeks 5–8)** – Automation graph editor, contract generation, delivery mini-game prototype, and GPU-instanced crops.
- **Milestone 3 (Weeks 9–12)** – Co-op netcode pass, research tree integration, modding hooks, and launch-ready tutorials/UX polish.

## Art & Audio Direction
- **Art Style** – Clean stylized realism with readable silhouettes, soft gradients, and emissive lighting on tech surfaces. Modular kitbash sets for pipes, rails, and racks.
- **Audio** – Layered ambiences (fans, water trickle, rain on glass), tactile UI sounds, and positive reinforcement cues on successful automation triggers.

## Risks & Mitigations
- **Simulation Complexity** – Keep environment calculations bounded via capped node counts and batching; provide “arcade” preset for players who prefer simplicity.
- **Netcode Synchronization** – Deterministic automation graphs and server authority for resource changes; prediction + reconciliation for interactables.
- **Content Scope** – Use procedural contract generation and modular props to extend playtime without heavy art budget.
