# Runtime discovery and continuation

Instruction-only integration: use existing host tools/commands. No executable, daemon, hook or remote service is bundled. Check capabilities rather than assuming every installation exposes identical commands.

## Identity and actual settings

Resolve current task/host from runtime context, CODEX_THREAD_ID or an unambiguous listing. Never use an ID merely mentioned in a referenced conversation. For remote tasks, inspect and queue on that host through its configured connection, not an unrelated local daemon.

Prefer an API explicitly reporting the active turn's model/effort. Otherwise locate the current rollout by UUID under actual CODEX_HOME/sessions (unset: platform user home/.codex/sessions). Search filenames first; require a unique active path. Read only necessary metadata.

Find the latest complete JSONL record with type=turn_context; extract payload.turn_id, payload.model, payload.effort and payload.cwd when present. Correlate identity with runtime identity. Missing fields are unknown. Conflicts with collaboration_mode.settings prevent declaring success. A partially written line permits one bounded retry; an old turn never proves a new setting.

Do not output entire context records. Never rewrite or relocate logs, databases, account data or CODEX_HOME.

## Live model capabilities

Sources in preferred order:

1. An available read-only host model-list tool. Inspect its schema; never invent a list_models tool.
2. The authenticated existing Codex App Server's model/list method. Initialize, retrieve all nextCursor pages and retain compact metadata. Use the current host/provider. Do not start/restart a daemon, enable remote control, expose a socket, change credentials or launch a model response to inspect capabilities.
3. Explicit current host tool metadata enumerating models and supported efforts. Use only combinations it actually describes and record its limits.

When the current host's shared proxy is unavailable, a **temporary stdio inspection server** (`codex app-server --stdio`) can be used for model/list and skills/list, with the same account/provider context. This was verified on Windows during development. Initialize as below, perform only read-only discovery, and terminate that child process afterward. Do not issue thread/start, thread/resume, turn/start or task-setting mutations through this isolated inspection server: it does not own the desktop's live task. Do not install a persistent daemon, replace CODEX_HOME or infer remote-task capabilities from a local server. There is no model inference request in this discovery workflow.

If none are available, preserve configuration and disclose discovery unavailable once. Do not infer capabilities from stale settings or model names.

Where `codex app-server proxy --help` confirms support, connect a bounded read-only client using that command. Each JSON object is a separate line; wait for successful initialization before sending initialized and further requests:

```json
{"id":1,"method":"initialize","params":{"clientInfo":{"name":"adaptive_reasoning_discovery","version":"0.1.0"},"capabilities":{"experimentalApi":true}}}
{"method":"initialized","params":{}}
{"id":2,"method":"model/list","params":{"limit":100,"includeHidden":false}}
```

Only use fields supported by the server. Match replies by id, handle unrelated notifications, follow result.nextCursor with cursor and fresh IDs. Consume stderr without printing secrets; enforce short timeouts. Close only this inspection client/proxy. An in-memory client is fine; temporary helpers follow local storage rules.

Never blindly pipe all requests and assume initialization or ordering. If proxy fails, use another already exposed source above, not a service restart or repeated connection attempts.

Normalize returned fields:

- model: exact API ID; displayName is not an API ID.
- supportedReasoningEfforts[].reasoningEffort: supported levels, not sorted rank.
- defaultReasoningEffort: model default, not actual task setting.
- upgrade/upgradeInfo: migration information, not quality ordering or consent.
- inputModalities: preserve task inputs for authorized model choices.

Refresh on invalid-model/effort errors, but reconcile ambiguous delivery before retrying. Hidden/unavailable models are not new targets. A current user-selected hidden model may be inspected specifically. Unknown future effort semantics need current documentation, not alphabetical ordering.

## Prepare next-turn settings, then queue

Inspect `codex --version` and `codex queue --help` on the current host. Windows CLI 0.155.0-alpha.9.2 observed during development accepts --thread, --message, -m and -c. Other versions/platforms require checks. Do not assume a universal `codex models` command.

A local test showed that a queue command with `-m gpt-6-astra -c model_reasoning_effort="high"` started a new turn but left actual effort at medium. Accepting these flags is not proof that they override an existing task. Use a separate task-scoped settings preparation step.

Preferred preparation: an available `thread/settings/update` interface on the current host, with the exact task ID and only target model/effort fields. Inspect the actual schema. The locally generated protocol describes model and effort as overrides for subsequent turns. Do not assume a callable tool with that name exists merely because the protocol describes it. If its authenticated connection is unavailable, do not alter the daemon to gain access.

Desktop fallback, only when exposed: the app's `send_message_to_thread` tool accepts `thinking` and optional `model`. Inspect the actual tool schema and whether it permits the current task. Use one short settings-preparation message with the target thinking value and the authorized model, explaining that this is configuration preparation only: do not recompute, queue or send another message in response. This may steer the current turn; that is **not** the new execution turn. After acknowledgement, separately queue the real handoff and end the current turn. Do not let that preparation message trigger a recursive routing cycle.

The desktop preparation plus queue path has been verified on Windows for Astra medium→high, Astra high→Sol medium, and Sol medium→Astra medium, with fresh turn IDs and matching model/effort records. Use this tested path when the desktop tool is exposed. The direct task-settings protocol remains an alternative requiring host verification. When no acknowledged task-scoped setter exists, disable automatic switching instead of relying on queue flags alone.

Record `settings_status` and the preparation source in routing state. If delivery of either preparation or queueing is ambiguous, reconcile before doing more. If preparation succeeded but queueing failed, report that future-task settings may already be changed. Restore only the plugin's prepared settings when their current value is verified unchanged and no newer user instruction supersedes them; otherwise preserve the uncertainty and avoid another mutation.

A documented atomic deferred tool can replace the two steps only if it explicitly configures the queued turn and has been verified on that host. Immediate send_message_to_thread alone is never a continuation.

PowerShell (resolve and validate variables first):

```powershell
# Run the user's temporary-environment initializer first, if any.
# PRECONDITION: task-scoped target settings preparation was acknowledged.
$queueArgs = @('queue', '--thread', $taskThreadId, '--message', $handoffMessage)
& codex @queueArgs
if ($LASTEXITCODE -ne 0) { throw 'Queue not confirmed; reconcile before retrying.' }
```

POSIX shell, after checking the host CLI:

```sh
# PRECONDITION: task-scoped target settings preparation was acknowledged.
codex queue --thread "$task_thread_id" --message "$handoff_message"
```

Use the existing configured connection when routing flags are required; never guess endpoints/tokens. Validate target IDs against the catalog. Pass arguments separately; never eval, Invoke-Expression or interpolate handoff text into executable command text.

Omit sandbox, approval-bypass, service-tier, global configuration, worktree and unrelated flags. If host/provider/directory preservation cannot be established, use a documented equivalent or leave routing disabled there.

Save acknowledgement and end the turn. Transport failure can be ambiguous: inspect the queue ID and later records before retrying. Where available, thread/queue/list is read-only; inspect its schema rather than inventing `codex queue list`. Never delete other queued user messages.

## Compatibility evidence

Verify each actual transition, including CLI configuration precedence. A settings update does not retroactively change a running turn.

Windows CLI 0.155.0-alpha.9.2 evidence, 2026-09-22:

- Historical desktop preparation followed by queue produced a new gpt-6-astra / low turn.
- Queue-only medium→high failed: new turn, same medium effort. This established the need for settings preparation.
- A subsequent user-authorized three-stage test used desktop settings preparation followed by queue: Astra medium→high, Astra high→Sol medium, and Sol medium→Astra medium all matched fresh turn_context model/effort records. Task identity and cwd were retained; the original configuration was restored.
- Temporary read-only stdio discovery returned five models and their effort options, with zero model-inference requests. skills/list found the installed skill enabled.

These are bounded transport/integration tests, not a guarantee of difficulty classification or implicit invocation on every task. xhigh, other operating systems and long-running/concurrent failure scenarios have not been exercised. Verify every future transition rather than treating this evidence as a substitute.

Official reference: https://learn.chatgpt.com/docs/app-server . Use current host help and official documentation if protocol shapes change.
