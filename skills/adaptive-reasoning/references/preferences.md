# Preferences and routing state

## Scope

Current explicit user choices override applicable project/user instructions, then plugin defaults. Preserve provider, environment, permission policy, service tier and billing channel.

Read the preference path the user/project provides. Otherwise use project-local `.codex/adaptive-reasoning.json` only when persistent project preferences are requested; task-only choices belong with task state. User-wide preferences require explicit user-wide scope and a permitted location. Follow storage rules and per-process temporary-environment initializers. Do not write untouched defaults just to create a file.

Default interpretation:

```json
{
  "schema_version": 2,
  "enabled": true,
  "baseline": "current",
  "minimum_effort": "medium",
  "max_effort": "xhigh",
  "automatic_efforts": ["medium", "high", "xhigh"],
  "ultra": { "require_stage_choice": true, "candidate_families": ["astra", "sol"] },
  "cooldown_minutes": 15,
  "restore_baseline": true,
  "cross_model": { "enabled": false, "routes": [] }
}
```

These are plugin-owned preferences, not Codex configuration keys. Unknown schema versions or invalid bounds never broaden consent. Preserve unrelated fields. Do not change global config.toml model settings.

Version 0.2.0 reads schema 1 conservatively: reconcile any pending transition first, preserve explicit caps, model restrictions and unrelated preferences, then apply the medium floor and per-stage ultra choice. An old max_effort=ultra or saved route does not bypass the choice. Validate old queued targets against the new policy; a queued low/ultra transition is not retroactively approved. Mark it superseded when appropriate and cancel only your own item through a documented interface, or report uncertainty without duplicating it. Do not rewrite preferences unless their persistence was requested.

`max_effort` limits ordinary automatic escalation. An explicit user instruction capping all usage also limits ultra proposals; the default xhigh value alone does not. A low-only cap conflicts with this policy: preserve current settings, report the conflict and suspend routing unless the user revises their instruction. Never silently raise the cap. For an initial low/none/minimal setting with no contrary user instruction, normalize to the lowest available permitted medium/high/xhigh level on the chosen model and verify it before adopting that baseline. Restoration must never select the original low baseline.

Natural interactions:

- “Only this model, at most high.” Task-only ceiling high, no cross-model routing or ultra proposal. An explicit lower cap can require a supported downward transition at medium or above; without such a request, a default ceiling only limits future automatic escalation.
- “For this project, allow Sol to upgrade to Astra when stuck, at most high.” Resolve exact IDs from the live catalog; save that route and ceiling. Do not switch merely because consent was saved.
- “Disable automatic switching.” Stop new routing and reconcile only your own pending continuation. This does not itself cancel development.
- “Always allow stronger models.” Broad consent supplies no reliable ranking. Use an agreed route; otherwise stay within-model while asking once for a permitted target if necessary. Never sort model names or version numbers.
- “Use Astra/ultra for this architecture review.” This supplies the current stage's choice if the exact target resolves unambiguously. It does not authorize future ultra stages. See [ultra.md](ultra.md).

Illustrative user-authorized route, NOT a built-in default:

```json
{
  "cross_model": {
    "enabled": true,
    "routes": [
      { "from": "gpt-5.6-sol", "to": "gpt-6-astra", "entry_effort": "medium" }
    ]
  }
}
```

Validate exact target availability and supported entry effort on the current host/provider. For an omitted non-ultra entry effort, use the live default only if it belongs to medium/high/xhigh within the cap; otherwise choose the lowest supported member of that set within the cap. An explicitly requested low entry is a conflict, not a silent substitution. An ultra route must pass the current-stage choice gate. No permitted known tier means no switch. Equal effort labels do not imply equal compute across models.

Routes must be explicit and acyclic for the unresolved problem. A renamed/unavailable model must be re-resolved, not silently substituted. Catalog upgrade metadata is a migration suggestion, not route authorization. Baseline restoration after a verified fix is distinct from escalating along a route.

## State

Use the designated task/project temporary root, with `model-routing/<host>/<thread-id>.json`; a projectless task uses its own approved intermediate directory. If no convention exists, use an allowed task-local workspace area. Never hardcode a drive, username, account home or shared task ID. Installed files stay read-only.

Record operational metadata only:

| Field | Meaning |
|---|---|
| schema_version | 2; read version 1 conservatively as above |
| host_id, thread_id, cwd | Exact owner and location |
| baseline_model, baseline_effort | Initial or explicitly revised user choice |
| original_model, original_effort, normalization_status | Original observation and requested/verified floor adjustment; requested is not active |
| observed_model, observed_effort, observed_turn_id | Runtime observation |
| target_model, target_effort, from_turn_id | Intended transition |
| transition_id, queue_id | Unique intent and server acknowledgement |
| settings_status, settings_source | Next-turn configuration preparation acknowledgement/source |
| status, pending | Preparing / queued / verified / failed / unknown / superseded; awaiting_ultra_choice has pending=false |
| ultra_stage | Stage ID, scope, acceptance criterion, candidates, recommendation, chosen exact model, answer reference, return baseline, and awaiting/approved/active/declined/completed/canceled status |
| reason, problem_key | Evidence and problem identity |
| last_switch_utc, progress_checkpoint | Cooldown and validated segment |
| handoff_path, active_sessions | Resume information |
| catalog_observed_at, cli_version | Freshness and compatibility |

Write atomically within the permitted directory. Validate ownership. One task has one unresolved routing intent. Stale preparing/unknown does not permit replay: inspect queue or later turns first. If delivery cannot be established, retain unknown and do not resend. No credentials or full logs.

A newer explicit user choice wins over a queued target. A runtime mismatch alone does not prove a manual change; it may be a failed transition.
