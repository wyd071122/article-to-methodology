# Methodology to Skill · 方法论蒸馏

把书籍、文章、课程和访谈中的方法提炼成 **有来源依据、能处理新输入、可以实际测试** 的 Agent Skill。适合拆书和工作流沉淀；普通摘要无需走蒸馏流程。

## 项目解决什么问题

书中的框架和经验往往停留在摘要或笔记里，到了实际任务中，仍然缺少明确的输入、操作步骤、判断条件和输出标准。本项目提供一个供 Agent 读取的技能指令包，将这些内容整理成可复用的任务技能，同时保留出处、适用范围和未解决的问题。

参考 [仓颉 cangjie-skill](https://github.com/kangarooking/cangjie-skill) 的方法提取与验证思路，重点完善证据追踪、不同文体的筛选、冲突和缺失处理，以及技能包的独立使用。没有足够依据时会说明缺口，而不是编出一套步骤。

## 主要功能

- **来源追踪**：记录来源元信息和章节、段落、页码或时间戳，标明实际阅读范围。
- **方法筛选**：按目标任务判断哪些内容可执行、仅供参考、需要核查或应当排除，区分原文与新增执行设计。
- **条件与冲突处理**：保留公式、单位、例外和缺输入处理；多来源冲突时保留有依据的部分，暂停存在分歧的动作。
- **生成技能包**：编写 `SKILL.md`、`agents/openai.yaml` 和必要的包内参考文件；按独立任务价值决定是否拆分入口。
- **测试与修订**：要求 Agent 用新输入实际执行生成的技能，记录结果与失败原因，并修订后复测；未执行的检查如实标注。

分析、文件生成和测试由宿主 Agent 执行。本项目包含技能指令、参考规范与测试材料，没有独立的命令行应用或一键转换程序。

## 安装方法

准备一个支持读取本地技能指令的 Agent。以下 PowerShell 示例使用**本项目开发环境已有的 Codex 用户技能目录**：设置了 `CODEX_HOME` 时使用其下的 `skills/`，否则使用用户目录下的 `.codex/skills/`。自定义配置或其他宿主请使用其实际技能目录。

在本项目根目录执行，将整个技能文件夹复制过去：

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$skillRoot = Join-Path $codexRoot 'skills'
$target = Join-Path $skillRoot 'methodology-to-skill'
if (Test-Path -LiteralPath $target) {
    throw '目标技能目录已存在，请先备份并处理现有版本。'
}
New-Item -ItemType Directory -Path $skillRoot -Force | Out-Null
Copy-Item -LiteralPath '.\methodology-to-skill' -Destination $skillRoot -Recurse
```

复制范围包括 `SKILL.md`、`agents/`、`references/` 和 `LICENSE`。随后按宿主的技能加载方式启用；若尚未识别，可新建会话后尝试显式调用。也可以直接采用下面的本地读取方式体验。

技能包没有额外运行脚本或固定联网工具依赖，使用宿主自身的模型与文件工具。PDF、EPUB、网页或音视频需由宿主提供相应读取、OCR 或转写能力；本项目不附带这些解析器。生成阶段优先使用宿主可用的官方技能初始化工具，工具不可用时按规范手工创建并记录。

## 使用方法

**安装后调用**：提供原文位置、希望解决的任务及可选的输出目录。

```text
请使用 $methodology-to-skill，
将我提供的访谈原文蒸馏成用于编辑资料交接的 skill。
原文：./materials/interview.md（替换为你的实际文件）
输出目录：./distilled/editorial/
请保留来源和适用边界，实际测试并记录结果。
```

**直接读取本地技能**：在本项目根目录，让 Agent 先读取入口，再处理已有样例。

```text
请读取 methodology-to-skill/SKILL.md，按其中的流程处理：
原文：tests/fixtures/02-quantitative.md
目标：生成中文库存补货触发判断 skill。
技能名称：inventory-reorder-trigger
输出目录：distilled/inventory/
请实际测试，记录来源、适用边界和未完成项。
```

输入可以是粘贴的原文、本地文件、可访问的链接或已经取得的转写文本。只有书名或无法读取的内容时，需要补充可核验原文。未指定输出位置时，默认写入当前项目的 `distilled/<slug>/`。普通摘要、书评及仅解释或应用已有方法的请求不属于默认蒸馏任务。

## 输入与输出示例

**蒸馏输入**：上面的 [英文公式文章](tests/fixtures/02-quantitative.md) 规定，在需求率稳定、交货期确定、安全库存已提供时，按 `R = 日需求量 × 交货天数 + 安全库存` 计算补货点，库存头寸 `P ≤ R` 时触发补货。文章没有订货量公式。

**蒸馏输出**：按上述名称和目录生成后，结构如下；参考文件仅按实际需要保留。

```text
distilled/inventory/
├── source-report.md
├── inventory-reorder-trigger/
│   ├── SKILL.md
│   ├── agents/openai.yaml
│   └── references/evidence.md
└── evaluation.md
```

已执行过的生成样例保存在 [测试产物](tests/results/baseline/02-quantitative/inventory-reorder-trigger/SKILL.md)，不需要重新生成即可查看。

**使用生成的技能**：让 Agent 读取新生成的 `distilled/inventory/inventory-reorder-trigger/SKILL.md`，再给出任务：

```text
需求稳定，平均12件/天，交货提前期确定且固定为7天，
计划人员提供的安全库存为18件。
现货50件、在途70件、已分配20件。
请判断是否触发补货，并说明能否确定订货数量。
```

对应结果：

```text
补货点：12 × 7 + 18 = 102 件
库存头寸：50 + 70 − 20 = 100 件
100 ≤ 102，触发补货；本文不足以确定订货数量。
```

来源报告记录依据、筛选和范围；`evaluation.md` 记录实际运行结果或未执行状态。研究报道、随笔或缺页材料若不支持所需方法，输出范围和缺口报告；没有合格单元时不创建空技能包。

## 本地开发与验证

主技能由官方 `skill-creator/scripts/init_skill.py` 初始化，界面元数据由官方生成器生成。官方验证器由宿主提供，未打包进本仓库；有该工具和所需 Python 环境时，可按实际路径运行：

```text
python -X utf8 <skill-creator目录>/scripts/quick_validate.py methodology-to-skill
```

测试请求见 [tests/cases.json](tests/cases.json)，预先固定的检查项见 [tests/rubric.json](tests/rubric.json)。执行者仅接收请求、原文和待测技能，评测者再核对实际产物。测试文章均为可开源的原创合成材料，不代表真实研究或业务制度。

8 篇原创文章覆盖 9 个蒸馏／摘要请求；13 个有效使用用例、3 项定向回归及主技能与 6 个生成技能的官方格式验证均通过。另保留了 1 次前提不完整的原始测试，补齐输入后独立重跑。详见 [测试与改进记录](tests/REPORT.md)；这组受控测试不证明对任意书籍的通用效果。

## 结构与许可

- `methodology-to-skill/`：可复制使用的技能包，无运行脚本。
- `tests/`：文章样本、请求、检查项、实际产物与评测记录。
- `research/`：GitHub 调研、固定版本参考和上游许可。

本项目原创内容采用 [MIT](LICENSE) 许可。参考了 [cangjie-skill](https://github.com/kangarooking/cangjie-skill)、[virgiliojr94/book-to-skill](https://github.com/virgiliojr94/book-to-skill) 和 [apple-ouyang/book-to-skill](https://github.com/apple-ouyang/book-to-skill)；保存的第三方文件遵循各自 [MIT 许可](research/github/licenses/)。用户输入的书籍和文章不因本项目开源而获得再分发许可。
