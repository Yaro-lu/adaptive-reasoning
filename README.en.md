# Adaptive Reasoning 0.1.0

**Let Codex think deeper when the work gets harder.**

Is medium enough for this problem? Should you switch to high? And after switching, do you need to explain where to pick up again?

Adaptive Reasoning is a lightweight Skill that helps **Codex adapt reasoning effort to development difficulty**. Once invoked, it assesses whether complex problems or evidence-backed repair attempts without progress warrant escalation, saves a handoff, and verifies the actual setting in a new turn of the same task before continuing. You spend less time managing effort levels and repeating context.

| Development situation | How it responds |
|---|---|
| Routine development and ordinary edits | Keeps your chosen model and initial effort |
| A problem calls for deeper reasoning | Assesses escalation and continues in the same task |
| A fix is verified and substantial ordinary work remains | Restores the baseline when cooldown conditions permit |
| You want another model available when needed | Switches only along a route you explicitly authorize |

**Give Codex the repository link and ask it to check and install the skill.** No server deployment is needed, and your current model is preserved by default.

> Early release: the switching and continuation flow has passed local Windows tests. Automatic adjustment requires compatible Codex host interfaces. Installation does not guarantee invocation in every task or lower costs. See runtime boundaries below.

[中文对照 / Chinese](README.md)

## Easiest way to connect Codex

Paste this into your own Codex:

```text
Install adaptive-reasoning from https://github.com/Yaro-lu/adaptive-reasoning.
First read README and skills/adaptive-reasoning/SKILL.md and inspect compatibility.
Prefer your available Skill installer for the skills/adaptive-reasoning directory,
or use a supported local plugin installation flow. Respect my storage preferences
and do not overwrite an existing skill with the same name.
Verify discovery and enabled status, and explain whether a new task is needed.
Keep my current model by default; only adapt effort. Do not authorize cross-model
switching or change global model settings. If switching interfaces are missing,
state that limitation instead of claiming automatic switching works.
```

After installation, say in the development task:

> Use $adaptive-reasoning for development. Raise reasoning effort when needed, keeping the current model.

No server deployment is needed. Permissions, host capabilities, and skill loading affect availability. This repository link is not an official directory installation link.

A skills-only plugin: Codex assesses development difficulty, uses existing task interfaces, and verifies the new reasoning effort in the next turn of the same task. No executable scripts, MCP server, background monitor, or hooks are bundled.

**The core switching flow passed live local tests:** prepare next-turn task settings → queue → end the turn → verify the new turn. Astra medium→high, Astra high→Sol medium, and Sol medium→Astra medium all passed, restoring Astra/medium. Using `codex queue -c ...` alone did not change effort in testing.

## Features

- Preserve the current model and initial effort; do not force medium.
- Discover available models and supported efforts dynamically; do not hardcode model routes.
- Escalate supported levels when justified, with an xhigh default ceiling, cooldowns, duplicate-queue guards, handoffs, and verification.
- Cross-model switching is off by default. Follow explicit authorized routes, saving preferences only at the user-specified scope.
- Respect manual choices, pauses, and cancellation. Restore the baseline only when substantial ordinary work remains.

## Usage

After installation or loading, say:

> Use $adaptive-reasoning for this development task. Raise effort when complexity warrants it, keeping the current model.

Optional route authorization:

> For this project, allow Sol to switch to Astra when needed, capped at high. Remember this authorization without asking each time.

The skill resolves names against the current catalog. New catalog entries do not automatically replace the current model.

Implicit invocation is enabled, but installation does not guarantee loading in every task. For ongoing use, add this instruction to the relevant project's AGENTS.md using the installed skill name:

> Use the adaptive-reasoning Skill for development. Start from the current model and effort, and follow saved cross-model authorization. Confirm next-turn settings before queuing and ending the turn, then verify the new turn before continuing.

The package does not itself modify global configuration or other projects' rules.

## Installation and publication

- Direct skill use: place `skills/adaptive-reasoning` in a user-selected skills directory, or read its SKILL.md by absolute path. Respect storage preferences and avoid overwriting existing skills.
- Local plugin: includes the Codex compatibility manifest `.codex-plugin/plugin.json` and portable root `plugin.json`. Installed locally in the personal marketplace; discovery returned `adaptive-reasoning:adaptive-reasoning` with enabled=true. Elsewhere, add it to your chosen marketplace and follow the current `codex plugin --help`.
- Public directory: not submitted or published. Replace `Unpublished local build` with real verified publisher information before submission. Publisher details and review are still required. No public license, fabricated policy URL, or official endorsement is claimed.

## Runtime boundaries

Requires model capabilities, task-level settings, same-task queuing, readable turn metadata, and execution permissions. Missing capabilities must not be reported as a successful switch. A supported desktop message interface can prepare next-turn settings, but does not replace queued continuation.

Tested on Windows with Codex CLI 0.155.0-alpha.9.2. A temporary read-only stdio connection returned five models and supported efforts with no inference requests. Three-stage continuation passed. xhigh, macOS/Linux, long-term automatic difficulty assessment, and comprehensive fault/concurrency scenarios remain untested. Paths are resolved per environment. New models can be discovered through a compatible catalog; new effort semantics or protocol changes may require maintenance.

A pure skill cannot guarantee invocation every time or reduced cost. Escalation, model changes, and continuation turns can increase usage.

## Data

The workflow reads current-task identity, model capabilities, and turn metadata; handoffs and state are saved in user-approved locations. No telemetry or log-export service is bundled. It does not modify session databases or global model settings. Existing Codex services still process model interactions.

## Package

The root contains both manifests and Chinese/English READMEs. `skills/adaptive-reasoning` contains [SKILL.md](skills/adaptive-reasoning/SKILL.md), agent UI metadata, and preference/runtime references. Agent instructions remain in English to avoid duplicated loaded context.
