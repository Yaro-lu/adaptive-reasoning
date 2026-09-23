# 更新记录 / Changelog

## 0.2.0 — 2026-09-23

按用户所选模型升档，跳过低档，ultra 增加专项选择流程。

Escalate on the user's selected model, exclude low tiers, and add a stage-specific ultra choice.

| 中文 | English |
|---|---|
| 自动路径仅 medium → high → xhigh，不自动使用 max。 | Automatic levels are medium → high → xhigh; max is not selected automatically. |
| 所有型号的 none/minimal/low 均不作为自动目标；低档起步在同模型规范化，恢复不回 low。 | Exclude none/minimal/low across models; normalize a low start on the same model and never restore low. |
| 需要 ultra 时，先推荐可用的 Astra 或 Sol 具体版本，等待用户选择或拒绝。 | Before ultra, recommend available exact Astra/Sol variants and wait for a selection or decline. |
| 等待时不改设置、不排队；授权只覆盖当前专项，未回答不视为同意。 | While waiting, do not prepare settings or queue; consent covers only the current stage, and silence is not approval. |
| 旧偏好保守迁移；低档上限冲突、旧 ultra 路线、候选失效都不能扩大授权。 | Migrate old preferences conservatively; conflicting low caps, old ultra routes, and vanished candidates do not broaden consent. |
| 中英文首页在功能介绍后展示更新说明。 | Both READMEs show release notes immediately after the introduction. |

本次验证包括结构、引用、包内容及规则场景走查。0.1.0 历史真实切换结果仅作为传输链路证据；0.2.0 的 low 规范化和 ultra 选择接续尚未做真实执行测试。不是官方目录审核或上架记录。

Validation covers structure, references, package contents, and document-based scenario review. Historical 0.1.0 live switches are transport evidence only; 0.2.0 floor normalization and ultra choice continuation have not been tested live. This is not an official directory approval or publication record.

## 0.1.0 — 2026-09-22

首次发布：动态模型目录读取、同任务设置准备与排队接续、实际档位核验、冷却和用户授权路线。Windows 本机三阶段切换通过。

Initial release: dynamic catalog discovery, task settings preparation and queued continuation, actual-effort verification, cooldowns, and user-authorized routes. Three local Windows switching stages passed.
