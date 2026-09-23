# Ultra stage: recommend, ask, then wait

Ultra is a separate, bounded decision, not the automatic next rung after xhigh. Never run an ultra request merely to test availability or demonstrate the skill.

## Decide whether to propose it

Record the concrete objective, evidence for needing the stage, expected output and completion criterion. Useful candidates are a whole-system review of architecture/invariants or implementation of independent modules with explicit file ownership and integration checks. A task name, error count, slow test or xhigh failure alone is insufficient.

Check explicit user limits first. “At most high/xhigh” or “no ultra” prohibits a proposal that would broaden that limit; do useful work within it. The plugin's default automatic xhigh ceiling is different: it permits asking about a bounded ultra stage. “Only this model” excludes cross-model candidates unless the user explicitly revises it.

## Discover candidates and recommend

Refresh the live catalog on the current host/provider. Offer available Astra and Sol entries that actually support `ultra` and the required inputs. Use exact IDs and display names from metadata. The examples “5.6 Sol”, “6 Sol”, “6 Astra”, and “6 Luna” are not an availability list. Do not invent model IDs or assume future names work.

When multiple versions in a family are available, use an unambiguous host-designated current/default entry or the user's chosen exact model. Otherwise show the actual variants and request a specific selection; do not silently rank by version number. Never offer an unavailable model as selectable. With one eligible candidate, explain which option is missing and still ask before ultra; with none, report that ultra cannot be offered and continue permitted non-ultra work.

Give a brief task-specific recommendation:

- For architecture decisions, cross-module invariants or a separate critical review, lean toward an available Astra/ultra as a review strategy.
- For implementation of well-separated modules with explicit contracts and integration tests, lean toward an available Sol/ultra, especially if it preserves the selected family.
- Explain these as workflow preferences, not measured superiority or guaranteed defect detection. Do not claim relative prices, speed or quality without current evidence. Ultra can increase usage; do not invent savings.

Example question in the user's language:

> 这一阶段需要核对跨模块架构约束，建议使用 Astra/ultra 做专项审查。选择 Astra/ultra、Sol/ultra，还是保持当前档位？本次只用于这段审查，完成后按恢复规则处理。

Replace labels with actual available variants and add the bounded scope. Keep “stay at current effort” available. No model is pre-authorized by being recommended or preselected in a UI.

## Pause and resume

Save `awaiting_ultra_choice` with task ID, stage ID, actual current configuration, eligible exact targets, recommendation and its reason, scope, acceptance criterion and return baseline. Keep queue `pending=false`: an unanswered choice is not a queued continuation. Ask with the available user-input tool or a normal question, and end/pause the dependent stage until an answer arrives. Do not call a task setter, enqueue a handoff, start the proposed stage or launch parallel agents while waiting. Do not poll, requeue the question, or treat elapsed time as consent. Existing unrelated user-owned work need not be canceled.

On reply, recheck newer instructions and reconcile ownership/state. “Yes” without identifying a model is insufficient when the recommendation was ambiguous; ask for the missing selection. A specific current-stage instruction selecting an exact model and ultra is already consent. Refresh capability if stale or changed; if the chosen target disappeared, report it and ask for a new choice rather than substituting.

Record `approved` with the chosen exact model, `effort=ultra`, stage scope and a minimal reference to the user's answer. Follow the normal prepare → queue once → end → verify workflow. Verification marks the entry consumed and the stage active; continuing the same active stage does not need another question. Unexplained mismatches follow the existing failure procedure and never trigger a blind retry.

If the user declines or chooses to stay, mark `declined`, clear the waiting state, continue on the actual setting and do not ask again for that stage. Cancellation clears the proposal and prevents its execution. After completion, expire the approval; a new ultra stage needs a new choice. An existing manual ultra selection can cover the current explicitly selected stage without redundant questioning, but not future entries after leaving ultra.

For parallel work, separately verify that delegation is available and authorized, and define ownership and integration checks. Selecting ultra does not itself grant permission to create agents or parallel tasks.

Restore only a verified baseline at medium or above, subject to cooldown and remaining work. If restoration itself would re-enter ultra, it requires a new stage choice. Never create a new turn solely to restore settings after all authorized work is done.
