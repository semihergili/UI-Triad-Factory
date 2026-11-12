# Ergili AI Design (`ergili-ai-design`)

An Antigravity Agent Skill built for autonomous **3-agent UI replication & full-functional desktop/web app creation**:

1. **Agent 1 (`ui-analyzer`)**: Breaks down reference UI screenshots, annotated mockups, or extracted video keyframes section-by-section into a pixel-faithful design, localization, and interaction spec (`UI_SPEC.md`).
2. **Agent 2 (`app-builder`)**: Implements the full interactive application (Electron desktop or web app) cleanly with syntax and smoke checks.
3. **Agent 3 (`visual-verifier`)**: Drives live E2E checks, captures state screenshots, scores every section side-by-side against references, and feeds exact CSS/HTML/JS deltas back into Agent 2 until visual parity (`>= 95/100`) and zero runtime errors pass.

## Workflow Architecture

```text
Reference Media (Screenshots / Video Keyframes)
   │
   ▼
[Agent 1: ui-analyzer] ──► UI_SPEC.md (Tokens, Layout, Localization, 10-Pt Checklist)
   │
   ▼
[Agent 2: app-builder] ──► Modular Functional App + Syntax & Smoke Verification
   │
   ▼
[Agent 3: visual-verifier] ──► Live E2E + State Screenshots + Visual Diff Audit
   │
   ├── Parity < 95/100 or defect? ──► Exact Fix Deltas to Agent 2 ──► Re-Verify
   └── Pass (0 errors, parity ≥ 95/100) ──► Launch & Deliver
```

## Installation

Copy this folder into either your global Antigravity skills directory or your project's workspace `.agents/skills/` folder:

### Global Installation (All Workspaces)
- Windows: `%USERPROFILE%\.gemini\config\skills\ergili-ai-design\`
- macOS / Linux: `~/.gemini/config/skills/ergili-ai-design/`

### Workspace Installation (Project-Specific)
```text
<your-repo>/
└── .agents/
    └── skills/
        └── ergili-ai-design/
            ├── SKILL.md
            └── references/
                └── agent-prompts.md
```

## Repository Contents

- [`SKILL.md`](./SKILL.md) — Core skill definition, YAML frontmatter, 3-agent orchestration steps, and 1:1 visual refinement loop rules.
- [`references/agent-prompts.md`](./references/agent-prompts.md) — Reusable subagent prompt templates, video keyframe extraction snippet (`ffmpeg`), and visual E2E checklist.
- [`LICENSE`](./LICENSE) — MIT License.
