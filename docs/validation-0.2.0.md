# 0.2.0 规则验收 / Policy review

2026-09-23。以下为对最终 Skill 指令的人工式场景走查，不是独立模型评测或真实切档执行结果。

2026-09-23. These are document-based scenario reviews of the final skill, not independent model evaluations or live switching results.

| 输入场景 / Scenario | 指令规定的处理 / Required result |
|---|---|
| 用户所选 Sol/high，普通开发 / Selected Sol/high, routine work | 保持 Sol/high，不强制 medium / Preserve Sol/high |
| Astra/medium，推理瓶颈符合升级条件 / Justified reasoning bottleneck | 同模型选择支持的 high / Choose supported high on the same model |
| 分别给定 5.6 Sol、6 Luna、6 Sol、6 Astra 的 low；目录均支持 medium / Four example families at low, medium available | 每种均规划同模型 medium，不因名称跳过过滤 / Each normalizes to medium on that model |
| 未来型号 X/low，目录 [xhigh, low, high] / Future model, unsorted catalog | 选 high；不虚构 medium、不按返回顺序选 xhigh / Select high, invent no medium |
| 当前 low，但用户只要求读取设置 / Read-only inspection at low | 只读；不启动规范化 / No normalization |
| 当前 low，用户明确要求保持 low / Explicit keep-low instruction | 尊重用户，暂停规范化，不循环改回 / Suspend normalization; no setting fight |
| 模型只有 low/ultra，或缺少设置接口 / No normal eligible level or setter | 报告不可按普通路径切换，不偷偷选 ultra / Report limitation; no automatic ultra |
| 当前 Luna/xhigh，有明确整体审查范围，Astra/Sol ultra 可用 / Bounded review with both candidates | 给出确切候选与 Astra 倾向建议，等待选择 / Recommend Astra for the review, wait |
| 独立模块实现，两个候选可用 / Independent implementation stage | 倾向建议 Sol；不因选 ultra 自动获得并行授权 / Recommend Sol; delegation still needs authorization |
| 用户尚未回答或 UI 预选了推荐项 / No submitted answer | awaiting_ultra_choice，pending=false；零 setter、零 queue / No settings preparation or queue |
| 用户明确选择本次 Astra/ultra / Explicit current-stage selection | 刷新检查后允许单次设置与排队，不再问同一个问题 / One verified transition; no redundant question |
| 用户拒绝或要求保持原档 / Decline or stay | 继续实际档位，同专项不反复问 / Continue current effort; no repeated prompt |
| 只有一个 ultra 候选；或 Sol 有两个不明确版本 / One candidate or ambiguous variants | 说明可用性，仍等选择；多版本不能猜 / Still ask; do not guess a variant |
| 用户选定候选后该型号失效 / Chosen candidate disappears | 不替换另一模型；报告并等待新选择 / No substitution; request a new choice |
| 明确最高 high / Explicit cap high | 不提出突破上限的 ultra 切换 / No ultra proposal beyond the cap |
| schema 1 已保存 ultra 路线 / Legacy ultra route | 不能代替本次选择 / Cannot replace current-stage consent |
| 初始 low 规范化到 medium，之后升 high，完成难题 / Normalized baseline after escalation | 恢复目标 medium；不回 low / Restore medium, never low |
| ultra 专项结束但任务全部完成 / Ultra stage ends with all work done | 不为降档额外排队 / No restoration-only turn |
| 新专项要求再次进入 ultra / Re-enter ultra for a new stage | 旧专项授权过期，需要新选择 / Old choice expired; ask again |
| 旧低档/ultra 队列或设置回执不明 / Stale or ambiguous transition | 先协调状态，不将历史记录视为新授权、不盲重发 / Reconcile; no blind retry |

设计取舍：普通路径限制为 medium/high/xhigh，ultra 独立询问；保留用户原模型而非默认 Sol/high。建议是工作流分配，不是模型性能排名。档位过滤基于元数据，不固定模型版本。

Design: automatic medium/high/xhigh with a separate ultra question; preserve the user's model instead of imposing Sol/high. Recommendations assign work, not performance rankings. Filtering uses metadata rather than fixed model versions.

验证范围：Skill/插件结构、两清单版本一致、UTF-8 与引用、JSON 示例、ZIP CRC 和逐文件一致性。未运行真实 low 规范化、xhigh/ultra 推理、ultra 用户选择接续或跨平台测试；0.1.0 的三阶段实测仅保留为历史传输证据。

Validation scope: skill/plugin structure, matching manifest versions, UTF-8/references, JSON examples, ZIP CRC and exact contents. No live low normalization, xhigh/ultra inference, ultra choice continuation, or cross-platform tests were run; the three 0.1.0 stages remain historical transport evidence only.
