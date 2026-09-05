# 教育行业开源项目与 Codex Skills 调研

> 调研日期：2026-09-05  
> 当前项目定位：课程资料、项目文档、讲义与题库的统一导入、检索、问答和引用追溯；后续扩展学习路径、能力测评、视频检索与多模态检索。  
> 数据口径：GitHub 仓库公开元数据快照。Star、更新时间会变化，采用前应重新核对。

## 1. 结论

当前项目不适合直接基于 Moodle 或 Open edX 二次开发。两者成熟但体量大、历史包袱重，和“教育知识库 + RAG 问答”的核心目标不一致。更合理的做法是：

1. 以现有知识库方案为主干；
2. 从 Frappe LMS、LearnHouse 借鉴课程/章节/学习进度的数据模型与交互；
3. 从 PageLM 借鉴“资料转笔记、题卡、测验”的 AI 学习工作流，但在许可证确认前不复制代码；
4. 从 math-question-bank 借鉴题库、LaTeX、OCR 和解析链路；
5. 从 Bloom 借鉴苏格拉底式辅导、学习状态与提示升级逻辑；
6. 将 Open edX、Moodle、Kolibri 作为权限、离线同步、运营后台和规模化架构参考，不作为首期代码底座。

## 2. 优先研究项目

| 优先级 | 项目 | 方向与技术 | 快照 | 对当前项目的可借鉴点 | 风险与边界 |
|---|---|---|---:|---|---|
| P0 | [Frappe LMS](https://github.com/frappe/lms) | LMS；Vue | 3,188 stars；AGPL-3.0 | 课程、批次、章节、测验、进度和运营后台；界面与领域模型较现代 | AGPL 网络分发义务需要法务/开源合规评估；优先借鉴设计，不直接复制代码 |
| P0 | [LearnHouse](https://github.com/learnhouse/learnhouse) | 新一代学习平台；Python | 2,237 stars；AGPL-3.0 | 课程空间、内容组织、学习体验和现代前后端拆分 | 同样存在 AGPL 约束；应先做接口与数据模型对照 |
| P0 | [PageLM](https://github.com/CaviraOSS/PageLM) | NotebookLM 类学习平台；TypeScript | 1,958 stars；GitHub 未识别许可证 | 资料转笔记、闪卡、测验、播客等 AI 学习闭环，最贴近知识库扩展方向 | 未确认许可证前不得复制、分发或合并其代码；只做产品研究 |
| P0 | [math-question-bank](https://github.com/JudgePeach/math-question-bank) | 本地高中数学题库；Python | 204 stars；AGPL-3.0 | 题型建模、LaTeX 渲染、OCR 公式识别、LLM 解析、题目检索 | 项目规模较小，需重点检查数据质量、测试覆盖和安全性 |
| P0 | [Bloom](https://github.com/Li-Evan/Bloom) | 中文优先 AI 家教；Python | 250 stars；MIT | 苏格拉底式对话、个性化教学、学习状态与提示策略 | 仍需验证其评估方法和长期学习效果，不能把提示词效果当成教学成效 |
| P1 | [Kolibri](https://github.com/learningequality/kolibri) | 离线教育平台；Python | 1,111 stars；MIT | 离线优先、内容包、低带宽同步、设备端部署 | 业务目标偏普惠离线学习，只选择性研究内容包和同步机制 |
| P1 | [Open edX Platform](https://github.com/openedx/edx-platform) | 大型 LMS/MOOC；Python | 8,177 stars；AGPL-3.0 | 成熟的课程结构、角色、评分、讨论、扩展机制和大规模运营 | 体量与运维成本高，不建议作为当前项目首期底座 |
| P1 | [Moodle](https://github.com/moodle/moodle) | 成熟 LMS；PHP | 7,376 stars；GPL-3.0 | 角色权限、课程管理、题库、插件生态和教学运营流程 | 技术栈与当前 Python/RAG 路线不一致，适合做功能基准 |
| P2 | [Tutor](https://github.com/overhangio/tutor) | Open edX 容器化发行版；Python | 1,123 stars；AGPL-3.0 | 如果未来采用 Open edX，可参考标准化部署、插件与升级流程 | 它是部署工具而非业务系统；当前阶段不要引入 |

## 3. 面向当前需求的能力对照

| 当前需求 | 首选参考 | 建议落地方式 |
|---|---|---|
| 课程、项目、章节与先修关系 | Frappe LMS、LearnHouse | 设计独立的 `course / module / lesson / prerequisite` 领域模型，与向量切片元数据分离 |
| 文档与题库统一导入 | PageLM、math-question-bank | 使用可重放的导入任务；原始文件、解析产物、切片和索引分别留版本 |
| 题型、选项、答案与解析 | math-question-bank、Moodle | 题目采用结构化表，不把答案与解析只塞进向量文本；保留题目版本和来源 |
| 可追溯 RAG 问答 | PageLM + 当前掌柜智库文档 | 回答必须返回文档、课程、章节、文件和片段定位；无充分证据时显式拒答或降级 |
| 多轮学习辅导 | Bloom | 将“教学对话状态”与普通聊天历史分开；记录目标、误区、已给提示和掌握证据 |
| 学习路径推荐 | Frappe LMS、LearnHouse + 课程知识架构 Skill | 先建立先修图和能力标签，再做规则基线；数据不足时不要直接上复杂推荐模型 |
| 能力测评 | Moodle/Open edX + 评估有效性 Skill | 区分诊断性、形成性和总结性测评；上线前验证题目与学习目标是否对齐 |
| 视频检索与离线场景 | Kolibri | 视频字幕按时间轴切片，结果返回 `start_time/end_time`；离线需求明确后再引入内容包机制 |

## 4. 已保存的 Codex Skills

来源：[GarethManning/education-agent-skills](https://github.com/GarethManning/education-agent-skills)（调研时 731 stars、104 forks）。上游仓库整体采用 CC BY-SA 4.0；本工作区仅对 YAML frontmatter 做了 Codex 兼容调整，保留正文、来源标记和同一许可证。

| Skill | 工作区路径 | 适用场景 | 与项目的关系 |
|---|---|---|---|
| `curriculum-knowledge-architecture-designer` | `.agents/skills/curriculum-knowledge-architecture-designer/` | 课程知识依赖、概念结构、能力层级和教学顺序 | 支撑课程元数据、先修图与学习路径推荐 |
| `assessment-validity-checker` | `.agents/skills/assessment-validity-checker/` | 审核测验是否真正测到学习目标，检查信度与效度 | 支撑题库质量、能力测评与验收标准 |
| `intelligent-tutoring-dialogue-designer` | `.agents/skills/intelligent-tutoring-dialogue-designer/` | 设计分支式辅导对话、提示阶梯和误区处理 | 支撑多轮问答向 AI 家教升级；只输出设计蓝图，不等于实现代码 |
| `ai-hallucination-fact-check-protocol` | `.agents/skills/ai-hallucination-fact-check-protocol/` | 核验 AI 生成的事实、统计、引用与来源 | 支撑 RAG 引用追溯、拒答策略和教学内容审核 |

### 使用方式

重新打开工作区或开始下一轮 Codex 对话后，这些 Skill 可被自动发现。也可在提示中显式指定，例如：

```text
使用 $curriculum-knowledge-architecture-designer，基于课程介绍和章节资料输出 Python 课程的先修知识图谱。

使用 $assessment-validity-checker，检查题目资料中的选择题是否覆盖既定学习目标，并列出效度风险。

使用 $intelligent-tutoring-dialogue-designer，为“Python 函数参数”设计一个能识别常见误区的多轮辅导流程。

使用 $ai-hallucination-fact-check-protocol，审查知识库答案中的引用、数字和来源是否可验证。
```

## 5. Skill 选型与安全检查

- 只选择了 4 个教育垂直 Skill，没有批量安装 165 个上游 Skill。
- 选择依据：与当前需求直接相关、上游仓库超过 100 stars、内容包含证据基础、无自动执行脚本、无密钥或外部写操作。
- 已扫描安装内容，4 个目录均只有 `SKILL.md`；没有 `.env`、脚本、二进制文件或 CI/CD 配置。
- 上游 frontmatter 与当前 Codex 规范不兼容；已将其收敛为 `name`、`description`、`license` 和 `metadata`，正文没有改写。
- 4 个 Skill 均已通过 Codex `quick_validate.py`：`Skill is valid!`。
- CC BY-SA 4.0 允许商用，但要求署名、标明修改，并对改编材料采用相同许可证。对外分发这些 Skill 或其改编版本时必须保留该义务。

## 6. 不建议现在做的事

1. 不要同时克隆并试跑全部候选项目；技术栈、许可证和部署成本差异过大。
2. 不要直接把 LMS 数据库结构照搬到 RAG 元数据；课程业务实体和检索切片是两个生命周期。
3. 不要把“大模型能生成题目”当成题库质量保证；至少需要学习目标对齐、难度、答案唯一性、解析正确性和人工抽检。
4. 不要只用向量相似度做学习路径推荐；先修关系、掌握度和课程目标需要显式结构。
5. 不要在 PageLM 许可证未确认前复制其代码。

## 7. 建议的后续研究顺序

1. 深读 Frappe LMS 与 LearnHouse 的课程/章节/进度数据模型，产出字段对照表。
2. 深读 math-question-bank 的题库导入、LaTeX/OCR 和解析链路，产出题目实体草案。
3. 对 Bloom 做对话状态与提示策略拆解，映射到现有多轮问答状态。
4. 对 PageLM 只做产品流程和界面研究，同时确认许可证。
5. 用已安装的 4 个 Skill 分别产出课程知识架构、测评质量规则、辅导对话状态机和引用核验协议。

## 8. 调研边界

- 本轮保存的是项目调研结果和项目级 Codex Skills，没有克隆候选项目源码。
- GitHub Star 只反映关注度，不等于代码质量、教学有效性或生产成熟度。
- 本轮没有运行候选项目、审计其完整依赖链，也没有验证演示站点可用性。
- 在选定 1—2 个候选项目进入技术尽调前，不建议引入其依赖或迁移现有架构。
