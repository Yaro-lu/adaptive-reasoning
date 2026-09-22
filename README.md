# Codex自动推理强度 · Adaptive Reasoning 0.1.0

中文说明见下文；英文对照见 [English documentation](README.en.md)。
For English equivalents of the sections below, see [English documentation](README.en.md).

## 最简单的接入方式

把下面整段发给你自己的 Codex，让它检查环境并帮你安装：

```text
请从 https://github.com/Yaro-lu/adaptive-reasoning 安装 adaptive-reasoning。
先阅读 README 和 skills/adaptive-reasoning/SKILL.md，检查内容与本机能力。
优先用你可用的 Skill 安装器安装该仓库的 skills/adaptive-reasoning 目录；
也可使用受支持的本地插件安装方式。遵循我的存储偏好，不覆盖已有同名技能。
安装后核验技能可发现且已启用，说明是否需要新任务加载。
默认保持当前模型，只自动调整推理强度，不授权跨模型或修改全局模型配置。
如果缺少切档接口，明确说明限制，不声称已实现自动切换。
```

安装完成后，在需要启用的开发任务里说：

> 使用 $adaptive-reasoning 帮我开发，困难时自动提高推理强度，保持当前模型。

不需要自己部署服务。安装权限、宿主能力和技能加载情况可能影响可用性；这不是官方目录的一键安装链接。

纯 Skill 插件：由开发中的 Codex 判断难度，调用已有接口，在同一任务的下一轮使用新的推理强度并核验。包内没有执行脚本、MCP 服务、后台监控或 hooks。

**本机核心切换链路已通过真实测试。** 流程为“先更新任务的下轮设置 → 排队 → 结束本轮 → 新轮核验”。已逐轮核验 Astra medium→high、Astra high→Sol medium、Sol medium→Astra medium，当前恢复 Astra/medium。仅靠 `codex queue -c ...` 不保证改变已有任务档位，不能省略设置准备步骤。

## 功能

- 保留当前模型与初始强度，不强制 medium。
- 从当前环境读取模型及支持档位，不写死内置模型路线。
- 困难时逐档提高，默认自动升级上限 xhigh；冷却、防重复排队、交接、新轮核验。
- 跨模型默认关闭；读取已授权的确切路线，按用户指定范围保存偏好，无需逐次确认。
- 用户手动选择、暂停或关闭规则优先。难题解决后，仅在还有较长普通工作时恢复基准。

## 使用

安装或加载后说：

> 使用 $adaptive-reasoning 继续当前开发，遇到复杂问题自动升档，保持当前模型。

跨模型授权示例：

> 对这个项目，允许 Sol 困难时升级到 Astra，最多 high。记住偏好，后续不用逐次问我。

技能会查询当前模型列表解析名称，再保存授权路线。不会仅因出现新模型就更换模型。

允许隐式调用，但安装不保证每个任务都会加载。如需长期采用，在希望生效的项目 AGENTS.md 添加一行（使用安装后的实际技能名称）：

> 开发任务使用 adaptive-reasoning Skill；以当前模型和强度为基准自动调整，跨模型遵循已保存授权。先确认下轮任务设置已更新，再排队并结束本轮，新轮核验再继续。

本包不会自行修改全局配置或其他项目规则。

## 安装和发布

- **直接使用 Skill**：将 `skills/adaptive-reasoning` 放入用户指定的技能目录，或让任务按绝对路径读取 SKILL.md。遵循用户存储偏好，避免覆盖同名技能。
- **本地插件**：包含 Codex 兼容清单 `.codex-plugin/plugin.json` 和可移植根清单 `plugin.json`。本机已安装到 personal marketplace，技能扫描返回 `adaptive-reasoning:adaptive-reasoning` 且 enabled=true。其他电脑可将本包加入选择的 marketplace，再按当前 `codex plugin --help` 安装。
- **公开目录**：可准备 Skills-only 提交。尚未提交或上架；发布者身份、目录素材及审核需另行完成。兼容清单按本机校验器要求使用 `Unpublished local build` 标记开发版，公开提交时须换成真实、已验证的发布者。本包未指定对外许可证或虚构隐私网址、官方背书。

## 运行边界

需要模型能力来源、任务级设置接口、同任务排队、实际轮次记录及执行权限。缺少关键能力时不自动切换，不伪称成功。即时消息可以在支持的桌面接口中准备下轮设置，但不能代替排队接续。

测试环境为 Windows、Codex CLI 0.155.0-alpha.9.2。临时只读 stdio 连接成功获取 5 个模型及支持档位，没有模型推理请求。三阶段任务接续与模型切换已实测；xhigh、macOS/Linux、长期自动触发准确率及故障并发场景尚未实测。路径按环境解析，无固定盘符或用户名。新模型沿用接口结构时可动态发现；新档位语义或协议变化仍需维护。

纯 Skill 不保证代理每次选用，也不承诺减少 token 或费用。升强度、换模型和增加接续轮次可能增加消耗。

## 数据

只需要本任务身份、模型能力和轮次元数据；交接及状态保存在用户允许的位置。插件不修改会话数据库或全局模型配置，不带外发日志或遥测服务。模型交互仍走现有 Codex 服务。

## 包结构

```text
adaptive-reasoning/
  plugin.json
  .codex-plugin/plugin.json
  README.md
  skills/adaptive-reasoning/
    SKILL.md
    agents/openai.yaml
    references/preferences.md
    references/runtime.md
```
