# 评估记录：02-quantitative

## 实际执行与分层状态

本执行者取得待测 `methodology-to-skill/SKILL.md`、其要求的三个 references、`tests/cases.json` 中本用例请求及本篇英文 fixture，实际完成中文技能蒸馏。未读取 `tests/rubric.json`、其他用例内容或其他执行者结果，未修改待测技能。

生成技能：`inventory-reorder-trigger/`。实际应用任务留给另一个干净执行者，**本执行者未运行生成技能应用测试**，无应用回答文件。独立算出下表参考数值不等于执行过生成技能。

| 检查层 | 计划数 | 完成数 | 通过 | 失败 | 未运行 | 说明 |
|---|---:|---:|---:|---:|---:|---|
| 来源、公式与范围复核 | 1 | 1 | 1 | 0 | 0 | 同上下文对照原文，不证明新输入行为 |
| 官方结构验证 | 1 | 1 | 1 | 0 | 0 | `quick_validate.py` 实际退出码 0 |
| YAML 与资源可移植性检查 | 1 | 1 | 1 | 0 | 0 | 实际解析与路径检查 |
| 生成技能应用用例 | 8 | 0 | 0 | 0 | 8 | 测试任务与检查项固定，未执行 |
| 默认发现/自动触发 | 1 | 0 | 0 | 0 | 1 | 未验证 |

## 预先固定的应用任务与参考值

以下均为新增合成演练，不是真实业务或原文案例。除明确给出的异常外，所有用例的库存数量单位均为同一种“件”，已知需求稳定、交期确定，`S` 由计划人员提供。干净执行者仅取得技能包和任务输入，不提供检查项、参考结果或本评估文件。

| ID | 固定输入/请求 | 独立参考值与可观察检查项 | 实际产物 | 状态 |
|---|---|---|---|---|
| Q1 等号触发 | `d=8件/天, L=3天, S=6件, on_hand=26件, on_order=7件, allocated=3件`。判断补货并说明能订多少。 | `R=8×3+6=30件; P=26+7−3=30件; 30≤30`，触发；应拒绝从该规则造订货量，不把0或30当作订货量。 | 无 | 未运行 |
| Q2 负库存头寸 | `d=2件/天, L=4天, S=3件, on_hand=1件, on_order=0件, allocated=5件`。 | `R=11件; P=−4件; −4≤11`，触发，说明短缺；不能因P负而判输入非法。 | 无 | 未运行 |
| Q3 时间单位缺失 | `d=5`（未给时间单位）, `L=2天, S=4件, on_hand=20件, on_order=2件, allocated=3件`。 | 指出需求率时间单位缺失并询问；不得默认件/天、给出确定R或确定触发结论；可保留已知量。 | 无 | 未运行 |
| Q4 模型超范围 | 需求稳定 `d=2件/天`，交期在2至5天之间变化，`S=3件, on_hand=1件, on_order=0件, allocated=5件`。 | 说明交期可变超范围，请求合适政策；不静默用平均、最大或最小交期扩展模型，不给确定政策判断。 | 无 | 未运行 |
| Q5 缺安全库存 | `d=2件/天, L=4天`，计划人员未提供S，`on_hand=1件, on_order=0件, allocated=5件`。 | 请计划人员提供S；不默认0、不估计、不反推；不作确定触发判断。 | 无 | 未运行 |
| Q6 明确换算 | `d=14件/周`，用户明确一周=7天，`L=3天, S=4件, on_hand=9件, on_order=5件, allocated=4件`。 | 展示`d=2件/天`；`R=10件, P=10件`，等于也触发；展示转换依据及单位。 | 无 | 未运行 |
| Q7 高于补货点 | `d=5件/天, L=2天, S=4件, on_hand=20件, on_order=2件, allocated=3件`。 | `R=14件; P=19件; 19>14`，未触发；不能把所有情境一律判为补货。 | 无 | 未运行 |
| Q8 非法负参数 | `d=−2件/天, L=4天, S=3件, on_hand=1件, on_order=0件, allocated=5件`。 | 指出d违反非负要求并请求更正，停止确定判断；区分负需求率与合法负库存头寸。 | 无 | 未运行 |

在应用测试运行前，已用独立的 Python `decimal.Decimal` 内联算术算出四个完整数值用例参考值（不读取或调用生成技能）：

```text
Q1-equality R= 30 units; P= 30 units; P<=R= True
Q2-negative-position R= 11 units; P= -4 units; P<=R= True
Q6-converted-rate R= 10 units; P= 10 units; P<=R= True
Q7-above-point R= 14 units; P= 19 units; P<=R= False
```

这段参考计算的实际退出码为0；没有以它冒充技能行为结果。用例Q6的14/7=2由明确换算关系得出。

## 实际来源核对

已逐项对照 Definitions、Compute the trigger 两段、Scope，确认：需求率/交期前提，非负只明确适用于`d,L,S`；`R=d×L+S`；`P=on_hand+on_order−allocated`；数量单位和时间单位规则；`P≤R`包含等号；负P可计算且代表短缺；无订货量、安全库存选取、供应商、持有成本或服务水平保证。来源的合成、未验证性质保留。没有外部算法或未读范围。输入表、缺口和展示格式明确为执行设计。

## 实际格式与资源检查

实际命令（2026-10-29，PowerShell，Python UTF-8模式）：

以下为历史命令的可移植表示：工具目录改用占位符，目标目录相对于项目根目录；运行时需替换工具目录。保留原运行的输出与退出码。

```powershell
python -X utf8 '<skill-creator目录>/scripts/quick_validate.py' 'tests/results/baseline/02-quantitative/inventory-reorder-trigger'
```

实际标准输出：`Skill is valid!`；退出码：`0`，没有编码错误。

一次性内联Python还实际确认：YAML可解析；`default_prompt`含字面量`$inventory-reorder-trigger`；short_description长度25–64字符；入口两处`references/evidence.md`均存在且解析后位于包内；包内没有机器绝对路径、`[TODO`或`scripts/`；未配置禁用默认自动发现。每项返回`true`，命令退出码0。包内只有`SKILL.md`、`agents/openai.yaml`、`references/evidence.md`。

上述是结构与内容检查，不能证明自动发现或实际应用已通过。本轮无内容验证失败或行为修订；待另一个干净执行者保存真实回答后再评行为。


## 主控后续独立使用实测

以上“未运行”保留生成阶段的原始状态。生成后已由新的独立执行者仅读复制技能包和新输入，实际完成以下主计划用例，主控按固定检查项审阅通过：

- [inventory-normal](../../usage/inventory-normal/response.md)
- [inventory-missing-time](../../usage/inventory-missing-time/response.md)
- [inventory-equality-explicit](../../usage/inventory-equality-explicit/response.md)

完整范围、原始测试输入不足记录及最终官方验证见 [总报告](../../../REPORT.md)。本文件此前另列的扩展建议没有被这些用例覆盖的，仍为未运行，不合并宣称通过。
