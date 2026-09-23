# Adaptive Reasoning 0.2.0

**Automatically adjust Codex reasoning levels such as medium, high, and xhigh during development, then continue the current task.**

Adaptive Reasoning is a Codex Skill. It escalates medium, high, and xhigh on your chosen model as work demands. When a stage warrants ultra, it recommends an available Astra or Sol option and waits for your choice. It saves progress before switching and verifies the actual setting afterward, reducing manual setting changes and repeated handoffs.

## What's new in 0.2.0 · 2026-09-23

- **Start from your chosen model:** no default switch to Sol or Astra; ordinary escalation stays on that model.
- **Skip every low tier:** including 5.6 Sol low, 6 Luna low, 6 Sol low, 6 Astra low, and other models. Filtering uses effort values, not a model blacklist; example names do not imply account availability.
- **Use a medium floor:** a lower starting effort is normalized to the lowest supported medium/high/xhigh on the same model and verified. Restoration never targets low. Missing capabilities are reported without changing models.
- **Ask before ultra:** pause the proposed stage, explain the reason, recommend an available Astra/ultra or Sol/ultra, and wait. No settings preparation or queuing before a choice; staying at the current effort is an option.
- **Recommend for the actual work:** lean toward Astra for critical architecture reviews and Sol for well-scoped independent module implementation. These are workflow preferences, not performance guarantees; delegation requires its own capability and authorization.
- **Keep ordinary escalation automatic:** medium → high → xhigh; max is not an intermediate step to ultra. Explicit caps and manual choices win. Old route authorization does not replace the current ultra choice.

See [CHANGELOG.md](CHANGELOG.md) for release details.

## When it is useful

| Scenario | What the skill does |
|---|---|
| Debugging a complex bug after two distinct, evidence-backed fixes fail | Reassesses whether reasoning is the bottleneck; escalates to the next supported level when justified and continues debugging |
| Refactoring across files or analyzing complex state logic with multiple constraints | Assesses whether to escalate before deeper work, based on actual complexity rather than task labels |
| Continuing development after resolving a difficult problem | Restores the baseline after a verified fix when substantial ordinary work remains and cooldown conditions permit |
| Setting limits on automatic adjustment | Respects instructions such as “keep this model” or “at most high”; changing models requires separate authorization |
| A bounded system review or independent-module stage warrants ultra | Recommends exact available Astra/Sol variants and waits for the user's selection |

For example, you start at **medium**. If a complex bug meets escalation criteria, the skill can switch to **high** and continue debugging. Once the fix is verified, it can restore **medium** for substantial remaining routine work under its cooldown rules. The default automatic ceiling is **xhigh**, subject to supported levels and your authorization.

Small edits keep the baseline at medium or above. Missing dependencies, network failures, and permission errors are addressed directly; an error alone does not trigger escalation.

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
switching or change global model settings. Skip low and lower efforts; if ultra is
needed, recommend Astra or Sol and wait for my choice. If switching interfaces are missing,
state that limitation instead of claiming automatic switching works.
```

After installation, say in the development task:

> Use $adaptive-reasoning for development on my chosen model. Skip low; if ultra is needed, recommend Astra or Sol and ask me to choose.

No server deployment is needed. Permissions, host capabilities, and skill loading affect availability. This repository link is not an official directory installation link.

A skills-only plugin: Codex assesses development difficulty, uses existing task interfaces, and verifies the new reasoning effort in the next turn of the same task. No executable scripts, MCP server, background monitor, or hooks are bundled.

**The 0.1.0 core switching flow passed live local tests:** prepare next-turn task settings → queue → end the turn → verify the new turn. Astra medium→high, Astra high→Sol medium, and Sol medium→Astra medium all passed, restoring the baseline at test completion. Using `codex queue -c ...` alone did not change effort in testing. Version 0.2.0 has document-based scenario review, not live ultra execution or user-choice continuation validation.

## Features

- Preserve the selected model and initial effort at medium or above; normalize a lower effort to a supported eligible level.
- Discover available models and supported efforts dynamically; do not hardcode model routes.
- Escalate supported levels when justified, with an xhigh default ceiling, cooldowns, duplicate-queue guards, handoffs, and verification.
- Ordinary cross-model switching is off by default. Follow scoped explicit non-ultra routes. Each new ultra stage needs a current user choice; do not repeat the question when that stage's target was already explicitly selected.
- Respect manual choices, pauses, and cancellation. Restore the baseline only when substantial ordinary work remains.

## Usage

After installation or loading, say:

> Use $adaptive-reasoning for this development task. Raise effort when complexity warrants it, keeping the current model.

Optional route authorization:

> For this project, allow Sol to switch to Astra when needed, capped at high. Remember this authorization without asking each time.

The skill resolves names against the current catalog. New catalog entries do not automatically replace the current model.

Implicit invocation is enabled, but installation does not guarantee loading in every task. For ongoing use, add this instruction to the relevant project's AGENTS.md using the installed skill name:

> Use adaptive-reasoning 0.2.0 for development on my chosen model. Skip low and lower efforts; recommend and wait for my current-stage model choice before ultra. Confirm next-turn settings before queuing, end the turn, then verify the new turn before continuing.

The package does not itself modify global configuration or other projects' rules.

## Installation and publication

- Direct skill use: place `skills/adaptive-reasoning` in a user-selected skills directory, or read its SKILL.md by absolute path. Respect storage preferences and avoid overwriting existing skills.
- Local plugin: includes the Codex compatibility manifest `.codex-plugin/plugin.json` and portable root `plugin.json`. Installed locally in the personal marketplace; discovery returned `adaptive-reasoning:adaptive-reasoning` with enabled=true. Elsewhere, add it to your chosen marketplace and follow the current `codex plugin --help`.
- Public directory: not submitted or published. Replace `Unpublished local build` with real verified publisher information before submission. Publisher details and review are still required. No public license, fabricated policy URL, or official endorsement is claimed.

## Runtime boundaries

Requires model capabilities, task-level settings, same-task queuing, readable turn metadata, and execution permissions. Missing capabilities must not be reported as a successful switch. A supported desktop message interface can prepare next-turn settings, but does not replace queued continuation.

Version 0.1.0 was tested on Windows with Codex CLI 0.155.0-alpha.9.2. A temporary read-only stdio connection returned five models and supported efforts with no inference requests. Three-stage continuation passed. Version 0.2.0 startup normalization, ultra user-choice continuation, xhigh/ultra execution, macOS/Linux, long-term difficulty assessment, and comprehensive fault/concurrency scenarios remain untested live. Paths are resolved per environment. New models can be discovered through a compatible catalog; new effort semantics or protocol changes may require maintenance.

A pure skill cannot guarantee invocation every time or reduced cost. Escalation, model changes, and continuation turns can increase usage.

## Data

The workflow reads current-task identity, model capabilities, and turn metadata; handoffs and state are saved in user-approved locations. No telemetry or log-export service is bundled. It does not modify session databases or global model settings. Existing Codex services still process model interactions.

## Package

The root contains both manifests and Chinese/English READMEs. `skills/adaptive-reasoning` contains [SKILL.md](skills/adaptive-reasoning/SKILL.md), agent UI metadata, and preference/runtime references. Agent instructions remain in English to avoid duplicated loaded context.
