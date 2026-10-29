# 书籍方法论转 Skill：GitHub 初步调研

调研日期：2026-10-29（Asia/Shanghai）。

目标：为“一本书的方法论 → 可执行 Skill”选择可借鉴的工作流。

已通过 GitHub 公共 API 搜索仓库，读取三个项目的 README、核心 Skill 定义和一个成品示例，并查看仓颉的压力测试方法。以下判断属于文件审阅结果，尚未安装这些项目、执行其脚本或用真实书籍评测。

## 项目比较

| 项目 | 文件中体现的设计 | 对本项目的价值 | 需要调整之处 |
|---|---|---|---|
| [kangarooking/cangjie-skill](https://github.com/kangarooking/cangjie-skill) | 整书理解、候选提取、来源/可执行性/任务增益验证、能力卡、独立入口筛选、实际任务评测 | 最贴近方法论蒸馏，可借鉴来源追踪、执行契约、适用边界及验证流程 | 首本书可按需要精简产物；并非每个知识点都要拆成独立 Skill |
| [virgiliojr94/book-to-skill](https://github.com/virgiliojr94/book-to-skill) | 文档提取、按章节组织、主入口与主题索引、按需加载、决策速查表 | 借鉴输入处理和参考资料组织；适合技术书及需要回查原书的场景 | 需要结合目标任务，把索引和知识进一步落实为可执行流程 |
| [apple-ouyang/book-to-skill](https://github.com/apple-ouyang/book-to-skill) | 框架骨架、主 Skill 调度子 Skill、具体提问、案例和场景自测 | 写法直观；making-decisions 展示了 WRAP 如何变成分步引导 | 页数和行数阈值只是该项目的经验约定；不能用固定案例数量迫使模型补编内容 |

## 值得采用的做法

1. 先取得实际书籍文本，记录版本和章节/页码；提炼规则时保留可定位的依据。
2. 通读结构后按用户任务提取方法：触发条件、所需输入、操作步骤、判断分支、预期输出、适用边界。
3. 区分原书观点、外部补充和为方便执行增加的安排。apple-ouyang 的 WRAP 示例同时包含标为“亦仁原则”的内容，说明复合来源需要明确标识。
4. 先建立一个有明确用途的入口。只有确实存在独立任务、独立输入输出和复用价值时，才拆成多个 Skill；章节不是天然的 Skill 边界。
5. 主文件保留执行规则，较长案例和出处放入按需读取的参考文件。
6. 验证应同时覆盖正确触发、避免误触发、缺输入和边界情况，以及实际交付物是否满足要求。
7. 首次试点可用同一组任务比较“使用 Skill”和“不使用 Skill”，记录实际结果；没有实测数据时不宣称性能提升。

## 建议的首本书路线

以 cangjie-skill 的来源核验和任务验证为主要参考，借鉴 virgiliojr94 的文本提取与按需索引，以及 apple-ouyang 的具体提问写法。

拿到书籍后，先从一个完整方法或章节做最小试点：建立可追溯的方法卡，生成 Skill，用正常任务、缺输入任务和容易误用的任务验证，再决定全书的组织形式。试点只能代表该方法的验证范围。

## 已保存的核心参考

本地文件是调研材料，不表示已安装或启用其中的指令。仓库中的安装、发布、确认或多 Agent 规则不会因为被阅读就自动适用于本项目。

| 本地材料 | 固定版本来源 |
|---|---|
| [virgilio-book-to-skill.md](github/virgilio-book-to-skill.md) | [SKILL.md @ 7540eef](https://github.com/virgiliojr94/book-to-skill/blob/7540eefbe74933f5fc84c86becac52486b347778/SKILL.md) |
| [apple-book-to-skill.md](github/apple-book-to-skill.md) | [转换流程 @ a24960a](https://github.com/apple-ouyang/book-to-skill/blob/a24960ac89a3baa96a87cdf5ebaecf16c5d2eab1/skills/book-to-skill/SKILL.md) |
| [apple-making-decisions.md](github/apple-making-decisions.md) | [WRAP 成品 @ a24960a](https://github.com/apple-ouyang/book-to-skill/blob/a24960ac89a3baa96a87cdf5ebaecf16c5d2eab1/skills/making-decisions/SKILL.md) |
| [cangjie-skill.md](github/cangjie-skill.md) | [SKILL.md @ a28de55](https://github.com/kangarooking/cangjie-skill/blob/a28de55ba881b9928956a55048f743f7a9e3b23e/SKILL.md) |
| [cangjie-pressure-test.md](github/cangjie-pressure-test.md) | [压力测试流程 @ a28de55](https://github.com/kangarooking/cangjie-skill/blob/a28de55ba881b9928956a55048f743f7a9e3b23e/methodology/06-stage4-pressure-test.md) |

文件与来源映射另存于 [sources.json](github/sources.json)。

## 本地实现

本项目的原创实现位于 `methodology-to-skill/`，采用独立编写的任务流程和三份按需参考，未引入上游的编译器或脚本。原始参考文件作为调研存档保留，对应 MIT 版权声明位于 [github/licenses/](github/licenses/)。这些文件是只读参考材料，不属于安装后技能的运行依赖。
