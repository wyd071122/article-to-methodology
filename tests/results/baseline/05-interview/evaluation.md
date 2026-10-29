# 验证记录

2026-10-29，独立蒸馏上下文；显式加载待测 methodology-to-skill 与实际需要的三个参考，仅使用本例请求与全文，无评分表和其他执行者结果。生成技能未安装、未发布。

## 来源与契约检查

实际对照三个问答与附录，核对了位置、版本、已核/待核、段落/缺证据、未发现/未检查、接手者、时间、确认、待接收与事实分歧。以上纳入同一任务入口，新增格式与展示状态已标注。未服从垃圾留言。该检查不等于新技能实际使用测试。

## 使用测试计划

| 用例 | 输入 | 预先固定的可观察检查项 | 实际产物 | 状态 |
|---|---|---|---|---|
| U1 代表任务 | usage-test-inputs.md §U1 | 第 6 段待核且缺原表；9—10 段仍未检查；无确认时待接收；不称全稿核实 | 无 | 未运行 |
| U2 缺接手者 | usage-test-inputs.md §U2 | 不猜接手人，先找负责人；交接未完成；在已检查范围写未发现 | 无 | 未运行 |
| U3 矛盾与确认 | usage-test-inputs.md §U3 | 保留两个日期、来源页码、第 4 段和缺公告；不裁定；真实确认可标已接收而待核继续保留 | 无 | 未运行 |
| U4 摘要近邻 | usage-test-inputs.md §U4 | 只摘要，不生成交接单或技能 | 无 | 未运行 |

计划 4 项，完成 0 项，通过 0 项，失败 0 项，未运行 4 项。按分工另由干净执行者运行；未做同上下文使用自测，未验证自动发现或独立调用。

## 格式验证

实际运行 `python -X utf8` 调用宿主官方 quick_validate.py，退出码 0，原始输出 `Skill is valid!`。

另用一次不落盘的 Python 检查解析 SKILL.md frontmatter 与 agents/openai.yaml：名称一致、中文 UI 名称正确、default_prompt 保留字面量 `$editorial-material-handoff`，short_description 为 33 字符。入口相对链接在包内且文件存在；文件恰为 SKILL.md、agents/openai.yaml、references/evidence.md；没有 TODO、脚本或机器绝对路径依赖。检查退出码 0。

结构检查完成且通过，不代表行为测试或默认发现通过；上面的 4 个使用任务仍全部未运行。


## 主控后续独立使用实测

以上“未运行”保留生成阶段的原始状态。生成后已由新的独立执行者仅读复制技能包和新输入，实际完成以下主计划用例，主控按固定检查项审阅通过：

- [handoff-unconfirmed](../../usage/handoff-unconfirmed/response.md)
- [handoff-confirmed](../../usage/handoff-confirmed/response.md)

完整范围、原始测试输入不足记录及最终官方验证见 [总报告](../../../REPORT.md)。本文件此前另列的扩展建议没有被这些用例覆盖的，仍为未运行，不合并宣称通过。
