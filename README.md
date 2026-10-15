# Paper Deep Reader · 科研文献精读 Skill

[![Checks](https://img.shields.io/badge/checks-unittest-lightgrey)](#验证)

一个**能直接作为 Agent Skill 使用**、也可发布到 GitHub 的文献阅读仓库。特别关注电力电子、直流微电网、电机控制、AI数据中心供电、CPL/负阻抗、Simulink复现；也适用于其他科研领域。

它不是自动生成论文结论的“魔法脚本”，而是一套**严格基于原文证据**的Agent工作流程：区分原创论文与综述，逐公式推导，逐Figure/Table解读，审计仿真/实验平台和参数，比较有来源的创新点，并明确复现所缺条件。

## 内容

```text
paper-deep-reader/
├── SKILL.md                    # Agent Skill 的入口（YAML frontmatter）
├── AGENTS.md                   # 通用项目级代理说明
├── README.md
├── LICENSE
├── references/
│   ├── evidence-rules.md       # 原文证据与非虚构规则
│   ├── figure-analysis.md      # 逐图、逐表读法
│   └── engineering-replication.md
├── templates/
│   ├── reading-report.md       # 13节精读报告模板
│   ├── evidence-ledger.csv
│   ├── figure-matrix.csv
│   ├── experiment-matrix.csv
│   └── parameter-ledger.csv     # 参数来源与缺失清单
├── examples/
│   ├── N02_reading_report.md   # 17页CPL综述：无原创仿真实验
│   └── E12_experiment_audit.md # 34页原创研究：仿真/硬件证据链
├── scripts/
│   └── validate_report.py      # 标题/章节/基本证据规范检查
└── tests/
    └── test_validate_report.py
```

## 使用方式

**用支持 Agent Skills 的代理**：将仓库放在代理支持的 Skills 搜索位置，例如个人或项目的 `.agents/skills/paper-deep-reader/`，或将本仓库目录添加到代理的Skills配置；不同产品的技能发现规则可能不同，请依对应产品版本设置。`SKILL.md` 是技能入口文件。

**没有Skill功能时也能使用**：上传PDF，同时把 `SKILL.md` 作为系统/项目任务说明提供给代理，并要求按 `templates/reading-report.md` 出报告。

示例请求：

> 请使用 paper-deep-reader 精读这篇PDF。先判断是否为综述，再按原文图号/页码讲解研究目的、公式推导、方法、全部关键图表、仿真及硬件平台（如未报告要明确）、主要创新、局限和可复现路线。请用中文，面向刚开始学Simulink的硕士生。

> 请将 Fig.1–10 的每幅图对应作者结论，遇到 Eq.(5) 要给出从 P=VI 开始的完整推导；作者没有实验时不要编平台。

### 最直接的启动方法

1. 下载并解压这个仓库，把整个 `paper-deep-reader` 文件夹放进支持 Skills 的助手所要求的位置。
2. 对于使用 `.agents/skills/` 目录的项目，把仓库复制为 `.agents/skills/paper-deep-reader/`（确保该目录下直接有 `SKILL.md`）。
3. 打开一个新对话，上传PDF，输入：

> 使用 paper-deep-reader Skill 精读这篇论文。**完整输出**研究目的、工作和创新、逐步公式推导、每张仿真及实验图（图号+页码+坐标轴+结论）、仿真软件和版本（如文中有）、硬件平台、所有可识别参数及其出处、每个工况与研究主张的联系、与前人对比、局限和需要解决的技术难关。先判断是否为综述，不要编造原文不存在的实验。使用中文，把我当刚入门的硕士生。

如果助手不支持自动发现 Skills，就把 `SKILL.md` 上传到对话，同时上传论文PDF，并要求“严格遵循这个Skill的阅读流程”。

若需要兼顾某个Dissertation方向，可追加一句：“最后给出与我的**直流微电网/电力电子控制**课题的联系，但要把你自己的建议和原文结论分开”。

## 输出保证与边界

- **需要上传原PDF或有权访问正文**：没有原文，不进行虚构式“读过全文”。
- **Figure清单**：核对图中坐标轴、线型、单位、控制信号方向；无法看清的参数标为未报告。
- **综述论文**：不存在的原创Simulation/HIL/DSP数据必须标记未报告；不得将前人引用研究当作本论文实测数据。
- **公式**：原文编号、定义、计算步骤和物理含义分开；教学补充推导必须标记。
- **文献比较**：原文描述属于二手证据；未经外部核验不得宣称已经检查引用论文全文。
- **复现**：从教育模型→平均模型→完整开关电路，并明确各层级保真度。

## 验证

无需安装第三方包，使用 Python 3.9+：

```bash
python scripts/validate_report.py examples/N02_reading_report.md
python scripts/validate_report.py examples/E12_experiment_audit.md
python -m unittest discover -s tests -v
```

脚本只做**结构与基础可追溯性检查**，不代替人工核对图表、推导或引用真实性。

## GitHub 发布

```bash
git init
git add .
git commit -m "Initial paper-deep-reader skill"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/paper-deep-reader.git
git push -u origin main
```

先在GitHub创建空仓库并替换上方 `YOUR_USERNAME`。项目不包含原文PDF/页面图片，避免未经授权再分发论文全文。示例报告只对图表进行概述并标注定位信息。

## 许可证

技能说明、模板和本仓库中的原创代码使用MIT；不改变所引用论文原作者对其文章的权利。
