---
name: ergili-ai-design
description: >-
  Ergili AI Design — Autonomous 3-agent UI replication and full-functional app
  pipeline. Use when the user supplies UI screenshots, annotated mockups, or
  screen-recording videos and wants a 1:1 interactive Electron desktop or web app
  analyzed frame-by-frame/section-by-section, implemented cleanly, visually
  verified via live screenshots side-by-side against references, and iteratively
  refined until 1:1 parity passes.
---

# Ergili AI Design (3-Agent Analyze → Build → Visual Verify Loop)

Turn reference UI screenshots or videos into a full-functional, pixel-faithful application using three coordinated specialized subagents (`ui-analyzer`, `app-builder`, `visual-verifier`) with an objective visual diff and fix loop.

## When to Use

- User uploads one or more UI screenshots, marked/annotated images, or UI demo videos and asks to replicate them closely ("birebir yap", "frame frame böl", "ekran görüntüsüyle test et", "3 agent çalıştır").
- User wants a desktop (`Electron`) or web application where every visible control, popover, drawer, overlay, and secondary view actually works rather than being static HTML.

## Workflow Overview

```text
Reference Media (Images / Video Frames)
   │
   ▼
[Phase 0] Prep & Project Bootstrap (optional video keyframe split + npm init)
   │
   ▼
[Agent 1: ui-analyzer] ──► Inspects all frames ──► Writes UI_SPEC.md (sections + localization + 10-pt checklist)
   │
   ▼
[Agent 2: app-builder] ──► Builds modular functional app + syntax & smoke checks
   │
   ▼
[Agent 3: visual-verifier] ──► Runs E2E + captures state PNGs ──► Compares side-by-side via read_file
   │
   ├── Any section < 95/100 or layout/functional defect?
   │      └── Send concrete pixel deltas to Agent 2 ──► Fix ──► Re-run Agent 3
   │
   └── All checks pass & visual parity ≥ 95/100
          └── Parent reads final PNGs, removes stray test files, launches app & summarizes
```

## Step-by-Step Procedure

### Step 1: Collect Inputs & Prepare Workspace

1. **Media Preparation**:
   - Collect all uploaded image paths directly.
   - If the user supplied a **video**, extract clean keyframes (plus scene-change frames) into `<artifactDir>/scratch/frames/` before invoking Agent 1 so every animation state and sub-screen is readable via `read_file`.
2. **Target Parameters**:
   - **Runtime**: Electron frameless rounded desktop app (`transparent: true`, `frame: false`, subtle window controls) or standard Web app.
   - **UI Language**: Confirm localization (e.g., Turkish UI preserving brand/model/code names while keeping label widths proportional so pills/badges never wrap awkwardly).
   - **Directory**: Initialize `<projectDir>` (under active workspace or `~/.gemini/antigravity/scratch/<slug>`). Pre-install core packages (`electron`, offline `@fontsource-variable/inter` / `@fontsource-variable/geist` if offline-first Electron) in parallel with Agent 1.

### Step 2: Define the 3 Subagents

Use `define_subagent` to register the three distinct roles (templates in [agent-prompts.md](./references/agent-prompts.md)):

1. **`ui-analyzer`** (`EnableWriteTools: true`): Reads every reference image/frame with `read_file`, decomposes the layout section-by-section (chrome, sidebars, grids/textures, hero visuals, composers, popovers, overlays, secondary routes), extracts exact tokens (`px`, colors, radii, shadows, typography), maps concise target-language copy, and writes `<projectDir>/UI_SPEC.md`.
2. **`app-builder`** (`EnableWriteTools: true`): Implements `<projectDir>` cleanly following `UI_SPEC.md`. Keeps Electron `preload.js` context-isolated, avoids duplicated helpers, makes every tab/popover/modal/drag-drop/voice/persistence control genuinely interactive, and runs syntax checks (`node -c`) + launch smoke test before reporting.
3. **`visual-verifier`** (`EnableWriteTools: true`): Writes an isolated verification harness in `<artifactDir>/scratch/verify_app.js` (never polluting `<projectDir>`), executes real UI transitions + E2E assertions, captures screenshots of the main screen and every interactive overlay/popover into `<artifactDir>/scratch/`, inspects all captured PNGs side-by-side against the reference images with `read_file`, and outputs scored section tables + concrete fix deltas.

### Step 3: Run & Enforce the 1:1 Refinement Loop

1. **Invoke Agent 1** (`ui-analyzer`) -> wait for `<projectDir>/UI_SPEC.md`.
2. **Invoke Agent 2** (`app-builder`) -> implement all sections, verify syntax & smoke boot.
3. **Invoke Agent 3** (`visual-verifier`) -> test live app, capture state screenshots, score 1:1 visual parity against reference images.
4. **Refinement Pass (Required if any visual or functional delta exists)**:
   - Message Agent 2 via `send_message` with Agent 3's exact CSS/HTML/JS deltas (e.g., wrapping badges, unexpected default indicators, SVG sharpness vs glass blur, vertical spacing/height mismatches, clipped text).
   - Message Agent 3 via `send_message` to re-run verification with a clean `userData` profile, refresh PNGs, and re-inspect every updated image via `read_file`.
   - Repeat until `0` console/page errors, `0` horizontal overflow, all E2E assertions pass, and visual parity reaches `>= 95/100` across all sections.

### Step 4: Final Parent Verification & Delivery

1. Directly view the final captured screenshots via `read_file` to verify visual fidelity yourself.
2. Ensure no temporary capture/test scripts remain inside `<projectDir>`.
3. Verify syntax and launch the desktop/web app cleanly, reporting only directly observed paths, scores, and verified results.

## Reference Templates

- Prompt templates & screenshot verification harness guidelines: [agent-prompts.md](./references/agent-prompts.md)
