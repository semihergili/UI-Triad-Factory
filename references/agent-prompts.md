# Agent Prompt Templates & Verification Patterns (`ergili-ai-design`)

Use these structured templates when spawning the 3 subagents via `define_subagent` / `invoke_subagent`.

---

## 1. Agent 1 — `ui-analyzer` Prompt Template

```markdown
Inspect ALL uploaded reference UI screenshots/frames using `read_file`:
- <LIST_IMAGE_PATHS>

Break down the design frame-by-frame / section-by-section and write `<PROJECT_DIR>/UI_SPEC.md` covering:
1. **Window & Layout Geometry**: dimensions, frameless transparent shell (`border-radius: 16px`, subtle window controls `- □ ×`), header height, outer/inner sidebar widths, main fluid canvas sizing.
2. **Exact Visual Tokens**: hex surfaces, hairline borders, background textures (dot-matrix grid, vignettes, mesh gradients), typography scale, radii, box-shadows, multi-layer SVG/CSS hero artwork.
3. **Section-by-Section Breakdown & Localization Table**: map every label cleanly into `<TARGET_LANGUAGE>` while preserving technical brand/model/project names and keeping horizontal width parity so badges/buttons never wrap onto extra lines.
4. **Full Interactive Matrix**: specify real behavior for every route, collapsible sidebar, modal, drawer, attachment/file picker, drag-drop overlay, searchable popover, toggle switch, voice waveform bar, streaming AI/data response view, and local JSON/`localStorage` persistence.
5. **10-Point Verification Checklist**: concrete per-section checks for Agent 3 (`visual-verifier`).
```

---

## 2. Agent 2 — `app-builder` Prompt Template

```markdown
Build the complete, fully functional `<APP_TYPE>` application in `<PROJECT_DIR>` matching `<PROJECT_DIR>/UI_SPEC.md` and the reference screenshots:
- <LIST_IMAGE_PATHS>

Requirements:
- Keep code modular and clean (no duplicated UI builders or helper functions).
- Use offline fonts (`@fontsource-variable/inter`, `@fontsource-variable/geist`) if available.
- Implement every route, popover, drag-and-drop zone, native file dialog / browser picker, voice input simulation/WebAudio, search filter, and persistent workspace state.
- Add clean automated testability (consistent IDs/data attributes on buttons, popovers, dropzone, and modals) without altering visual layout.
- Run `node -c` across all `.js` files and smoke-launch the app cleanly before reporting completion.
```

---

## 3. Agent 3 — `visual-verifier` Prompt & E2E Harness Guidelines

```markdown
Verify `<PROJECT_DIR>` objectively by executing it live, driving real user clicks/events, capturing PNG screenshots into `<ARTIFACT_SCRATCH_DIR>`, and inspecting every screenshot side-by-side against the reference images with `read_file`.

Check and report:
1. **Syntax & Runtime Hygiene**:
   - `node -c` passes on every `.js` file.
   - `0` console errors (`level >= 3`), `0` uncaught `pageerror` exceptions.
   - `scrollWidth === clientWidth` (no horizontal overflow on desktop viewport).
   - Expected font families loaded (`document.fonts`).
2. **State Captures (Saved to `<ARTIFACT_SCRATCH_DIR>`)**:
   - `verify_main.png` (default initial screen — clean isolated `userData` so no leftover state chips/badges pollute first impression)
   - State screenshots for every reference state (e.g., `verify_voice.png`, `verify_attach.png`, `verify_dropzone.png`, `verify_models.png`, `verify_chat.png`).
3. **Interactive Flow Assertions**:
   - Click sidebar toggle, all routes, modal/drawer creation flows, live search filter, toggle switches, real `DragEvent` file drop, prompt submit/streaming output.
4. **Honest Section-by-Section Scoring (`X/100`)**:
   - Score Top Header, Sidebars, Background/Hero Artwork, Main Input/Composer, Overlays/Popovers, Cards, Lists/Tables, Overall Parity.
   - For any section below `95/100`, cite the exact CSS selector/file/line, measured rendered pixels vs expected visual target, and concrete fix instructions.
```

---

## Video Keyframe Extraction Tip (If User Supplies Video)

When a UI reference includes `.mp4`, `.mov`, or `.webm` recordings instead of still screenshots:

```powershell
mkdir "<ARTIFACT_SCRATCH_DIR>\frames" -Force
ffmpeg -i "<VIDEO_PATH>" -vf "select='gt(scene,0.22)',scale=1440:-1" -vsync vfr "<ARTIFACT_SCRATCH_DIR>\frames\scene_%03d.png"
```

Include a fallback interval capture (`fps=0.5`) if `scene` yields fewer than 4 frames, then inspect the extracted frames via `read_file` before passing the curated frame paths to Agent 1.
