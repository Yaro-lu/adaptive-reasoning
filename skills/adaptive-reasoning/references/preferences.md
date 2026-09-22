# Preferences and routing state

## Scope

Current explicit user choices override applicable project/user instructions, then plugin defaults. Preserve provider, environment, permission policy, service tier and billing channel.

Read the preference path the user/project provides. Otherwise use project-local `.codex/adaptive-reasoning.json` only when persistent project preferences are requested; task-only choices belong with task state. User-wide preferences require explicit user-wide scope and a permitted location. Follow storage rules and per-process temporary-environment initializers. Do not write untouched defaults just to create a file.

Default interpretation:

```json
{
  "schema_version": 1,
  "enabled": true,
  "baseline": "current",
  "max_effort": "xhigh",
  "cooldown_minutes": 15,
  "restore_baseline": true,
  "cross_model": { "enabled": false, "routes": [] }
}
```

These are plugin-owned preferences, not Codex configuration keys. Unknown schema versions or invalid bounds never broaden consent. Preserve unrelated fields. Do not change global config.toml model settings.

Natural interactions:

- “Only this model, at most high.” Task-only ceiling high, no cross-model routing. An explicit lower cap can require a supported downward transition; without such a request, a default ceiling only limits future automatic escalation.
- “For this project, allow Sol to upgrade to Astra when stuck, at most high.” Resolve exact IDs from the live catalog; save that route and ceiling. Do not switch merely because consent was saved.
- “Disable automatic switching.” Stop new routing and reconcile only your own pending continuation. This does not itself cancel development.
- “Always allow stronger models.” Broad consent supplies no reliable ranking. Use an agreed route; otherwise stay within-model while asking once for a permitted target if necessary. Never sort model names or version numbers.

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

Validate exact target availability and supported entry effort on the current host/provider. If entry effort is omitted, use the target's live default within the ceiling, or the highest known supported level below that default and within the ceiling. No permitted known tier means no switch. Equal effort labels do not imply equal compute across models.

Routes must be explicit and acyclic for the unresolved problem. A renamed/unavailable model must be re-resolved, not silently substituted. Catalog upgrade metadata is a migration suggestion, not route authorization. Baseline restoration after a verified fix is distinct from escalating along a route.

## State

Use the designated task/project temporary root, with `model-routing/<host>/<thread-id>.json`; a projectless task uses its own approved intermediate directory. If no convention exists, use an allowed task-local workspace area. Never hardcode a drive, username, account home or shared task ID. Installed files stay read-only.

Record operational metadata only:

| Field | Meaning |
|---|---|
| schema_version | Initially 1 |
| host_id, thread_id, cwd | Exact owner and location |
| baseline_model, baseline_effort | Initial or explicitly revised user choice |
| observed_model, observed_effort, observed_turn_id | Runtime observation |
| target_model, target_effort, from_turn_id | Intended transition |
| transition_id, queue_id | Unique intent and server acknowledgement |
| settings_status, settings_source | Next-turn configuration preparation acknowledgement/source |
| status, pending | Preparing / queued / verified / failed / unknown / superseded |
| reason, problem_key | Evidence and problem identity |
| last_switch_utc, progress_checkpoint | Cooldown and validated segment |
| handoff_path, active_sessions | Resume information |
| catalog_observed_at, cli_version | Freshness and compatibility |

Write atomically within the permitted directory. Validate ownership. One task has one unresolved routing intent. Stale preparing/unknown does not permit replay: inspect queue or later turns first. If delivery cannot be established, retain unknown and do not resend. No credentials or full logs.

A newer explicit user choice wins over a queued target. A runtime mismatch alone does not prove a manual change; it may be a failed transition.
