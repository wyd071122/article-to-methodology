# 本地测试与修订报告

日期：2026-10-29（Asia/Shanghai）。最终指令版本标记：`v0.3-tested`。本报告记录真实执行和检查结果，不代表相对于上游项目的性能基准。

## 结果

| 检查层次 | 实际结果 | 范围 |
|---|---|---|
| 首轮蒸馏与摘要边界 | 8/8 通过 | 7 个蒸馏请求及 1 个只要摘要的请求 |
| 修订后的保留文章蒸馏 | 1/1 通过 | 研究背景中的完整测量方法 |
| 生成技能的独立使用 | 13/13 个有效用例通过 | 正常任务、缺输入、状态、冲突、零分母、相等与负值 |
| 修订后的定向回归 | 3/3 通过 | 来源选择与执行契约，非完整重生成 |
| 官方格式验证 | 7/7 通过 | 主 skill 与 6 个实际生成的 skill |
| 复制包一致性与引用 | 6/6 通过 | 新执行者仅使用复制后包内指令及引用 |

另保留 **1 次测试输入不足的原始运行**：库存边界题没有明确交期确定性，执行者先询问，未进入计算。该轮不计通过，也不当作已验证计算错误；补全前提后用新 ID 重新运行。使用阶段实际保存了 14 次回答，其中 13 次可按既定目标判定通过。

## 测试方法

- 8 篇文章均由本项目原创，覆盖操作指南、英文公式文章、研究报道、抒情随笔、访谈、多来源冲突、教程节选及研究方法短文，按项目 MIT 许可提供。所有业务和研究情境为合成材料。
- 初版先由官方 `init_skill.py` 创建，完成正文和官方生成的 UI 元数据后，分别交给未参与编写的独立子 agent 执行。[请求](cases.json) 与 [检查项](rubric.json) 分离；执行者不读取评分表和其他运行结果。
- 生成的技能包复制到项目外临时目录，再由新的独立执行者只读取包内指令、相对引用和新任务，实际生成最终回答；不提供原始文章、来源报告或预期答案。
- 主控按事先规定的语义要求核对实际产物和计算；格式校验使用宿主自带官方 `quick_validate.py`。补充检查 YAML、提示中的字面量技能名、描述长度、包内引用及无脚本。
- 执行环境为当前 Codex 会话的独立子 agent、PowerShell、Python 3.13.14 UTF-8 模式；未固定采样种子或采集精确宿主构建号。没有调用真实业务系统。

初版指令哈希见 [baseline-manifest.json](results/baseline-manifest.json)，最终文件哈希见 [refined-manifest.json](results/refined-manifest.json)。预先固定的测试计划与后续输入修订分别见 [test-plan-manifest.json](results/test-plan-manifest.json) 和 [test-plan-amendment.json](results/test-plan-amendment.json)。

## 蒸馏结果

| 用例 | 实际结果与检查重点 | 原始产物 |
|---|---|---|
| 01 操作指南 | 生成一周试验技能；保留三条记录、双条件筛选、基线和复盘，无评分或成功率杜撰 | [来源报告](results/baseline/01-procedure/source-report.md) |
| 02 英文公式文章 | 生成中文补货触发技能；保留公式、单位、`≤`、负头寸和订货量边界 | [来源报告](results/baseline/02-quantitative/source-report.md) |
| 03 研究报道 | 不生成绿植干预；相关性不足以支持因果或保证提升 | [来源报告](results/baseline/03-research-report/source-report.md) |
| 04 抒情随笔 | 无可重复机制，只报告，不把隐喻编成疗愈流程 | [来源报告](results/baseline/04-personal-essay/source-report.md) |
| 05 访谈 | 生成资料交接技能；保留未核事实、分歧和接收状态，忽略源内恶意留言 | [来源报告](results/baseline/05-interview/source-report.md) |
| 06 冲突指南 | 保留完整性及一致区间判断，只阻塞 `500 < b ≤ 800` 的归属结论 | [来源报告](results/baseline/06-conflicting-guidelines/source-report.md) |
| 07 教程节选 | 生成窗口观察技能；零流量未定义，不补 T/K 或回滚命令 | [来源报告](results/baseline/07-partial-tutorial/source-report.md) |
| 08 摘要近邻请求 | 仅回答三句话，未生成技能或报告；响应由测试控制器另存 | [捕获的回答](results/baseline/08-summary-routing/response.md) |
| 09 保留研究方法 | 生成测量技能；相等算准时、取消不入分母、缺结束时间不算全期比率 | [来源报告](results/refined/09-research-method/source-report.md) |

## 新输入的实际使用

| 用例 | 实际结果 | 回答 |
|---|---|---|
| 库存正常 | R=102 件，P=100 件，触发；不把差额当订货量 | [回答](results/usage/inventory-normal/response.md) |
| 库存缺时间单位 | 请求完整单位及适用前提，不给确定结论 | [回答](results/usage/inventory-missing-time/response.md) |
| 库存边界（明确前提重跑） | R=50；P=50、P=−2 均触发，负值表示短缺 | [回答](results/usage/inventory-equality-explicit/response.md) |
| 试验缺观察 | 两条不足，先补第三条，不选题或启动 | [回答](results/usage/procedure-missing-evidence/response.md) |
| 试验正常 | 可控问题入选，供应商问题暂存；基线60%，形成待执行计划，不编后测 | [回答](results/usage/procedure-normal/response.md) |
| 交接未确认 | 资料缺口保留，接收状态为待接收 | [回答](results/usage/handoff-unconfirmed/response.md) |
| 交接已确认 | 已接收，可记完成；不冒充事实全部独立核实 | [回答](results/usage/handoff-confirmed/response.md) |
| 审批冲突 | 600 元字段齐全，审批归属待确认 | [回答](results/usage/approval-conflict/response.md) |
| 审批一致范围 | 500 元组长、801 元主管；不声称已批准 | [回答](results/usage/approval-agreement/response.md) |
| 观察零流量 | n=f=0，错误率未定义，不能判定回滚 | [回答](results/usage/observation-zero/response.md) |
| 观察正常 | 2/40=0.05=5%，不能补猜回滚参数 | [回答](results/usage/observation-normal/response.md) |
| 会议完整记录 | N=3、H=2，准时率2/3≈66.67%，取消不入分母 | [回答](results/usage/meeting-equality-heldout/response.md) |
| 会议缺结束时间 | B会仍计入N=2，待补实际结束时间，不报告确定全期率 | [回答](results/usage/meeting-missing-heldout/response.md) |

逐项判断见 [scoring.json](results/scoring.json)，官方原始验证结果见 [validation.json](results/validation.json)，复制一致性见 [portability-check.json](results/portability-check.json)。

## 根据结果所做的完善

1. **按任务而非文体整篇筛选**：研究报道不能支持干预，不代表所有研究文章都不可用。显式加入“候选方法 × 目标任务”，并用第09例验证完整测量流程仍能入选。
2. **冲突精确到分支**：第06例表明一致区间有独立价值。写清保留原规则交集、只停止冲突动作，避免把局部争议扩大为整个流程不可用。[定向回归](results/refined/selection-regression.md) 复核03、06、07。
3. **区分忠实度与现实有效性**：来源核验与样例计算通过，不表示合成政策或经验方法已获实证验证。原文性质必须随技能保留。
4. **成对测试完成与停止行为**：除缺输入、零分母和冲突，还验证充分输入时确实完成任务，防止一味补问造成过度阻塞。
5. **适配官方模板变化**：初轮有执行者因脚手架假设不匹配恢复重试；补充初始化后读取实际模板再更新的指引。异常概况保留于本报告，详细过程日志已在公开发布清理时移除。
6. **测试前提本身也需检查**：原库存保留题只给交货天数，没有明确确定性。原回答保留为 [测试输入不足](results/usage/inventory-equality-heldout/response.md)；保留原数值预期，另加 `inventory-equality-explicit` 由新执行者重跑，未把原结果改成通过。

最后一项只修改评测规范与用例；没有为得到答案而放宽生成技能对适用前提的要求。最终官方验证在这些修订完成后重新执行。

## 实际限制

这是受控合成文章测试，尚未验证整本长书的覆盖率、扫描 PDF/OCR、真实网页提取或多模型稳定性；未与仓颉作同条件性能对照。测试采用显式加载，不能证明宿主自动发现行为。部分生成阶段记录中另列的扩展测试仍是未运行建议，本报告只对上述实际完成的主计划计数。

运行中出现过模板补丁重试、首次用例输出范围过宽和终端中文显示问题；未泄露隐藏检查项，未以这些过程异常冒充功能改进或成功率数据。详细过程日志已移除，实际回答、来源报告、评估结果和测试清单仍保留。

## 公开发布清理

- 移除本地抓取的 8 篇书籍网页正文及 1 份来源清单，共 9 个缓存文件；这些材料未取得明确的再分发许可。
- 移除 13 份调试及过程日志，并在忽略规则中排除同类日志和抓取缓存。
- 将 2 份评估记录中的本机命令路径改为可移植表示，将 4 份使用回答中的临时来源链接改为指向保留技能包的相对链接。以上为公开版本的路径规范化，不是原始命令或回答的逐字存档。
- 保留 8 篇原创测试文章、14 份使用回答、主技能和 6 个生成技能，以及测试清单、评分、文件哈希和上游许可。方法内容、计算、判断及行为评分未改动。

清理后重新核对敏感信息、文件引用和官方格式验证；原有行为测试未因路径规范化而重新执行或重新计分。最终检查摘要见 [final-audit.json](results/final-audit.json)。

缓存文件已全部删除；环境自动审批策略拦截了目录删除，因此暂留空的 `.firecrawl/` 及其 `naval/` 子目录，不含公开文件。

本次没有初始化 Git、安装技能到个人目录、上传 GitHub、执行发布或联系他人。
