# 智能信息收集系统

一个基于 Claude Code 的**独立 Plugin 项目**，能够从多个可靠信息源自动收集信息，进行深度分析，并生成结构化报告。

## 核心特性

- ✅ **独立 Plugin**：完全自包含，不依赖系统内置 Agent
- ✅ **双 Skill 支持**：`info-collector`（信息收集）+ `deep-investigator`（深度调查）
- ✅ **专用 Agent**：项目内定义的 6 个专业 Agent，共享复用
- ✅ **智能双模式**：webpage-analyzer 支持 focused/comprehensive 双分析模式
- ✅ **多层协作**：Skill → 专用 Agent → 网页分析 Agent
- ✅ **双通道搜索**：SITE.md 可靠源 + WebSearch 全网搜索，覆盖更广（info-collector）
- ✅ **多轮调查**：反思评估 + 补充搜索，确保信息完整性（deep-investigator）
- ✅ **并行处理**：多个信息源和网页同时处理，提高效率
- ✅ **智能筛选**：根据用户需求自动筛选和分类信息
- ✅ **深度分析**：每条信息都经过详细分析和总结
- ✅ **结构化报告**：按领域/维度分类，段落形式叙述，带完整引用
- ✅ **自动发现新源**：智能评估并添加新发现的有价值网站到 SITE.md
- ✅ **加权评分系统**：根据发现途径自动调整信息源评分权重
- ✅ **反馈闭环**：收集用户反馈，自动优化配置（info-collector）

## 项目结构

```
InfoCollector/
├── marketplace.json				# Marketplace 配置文件（仅用于本地模式）
├── .claude-plugin/
│   └── plugin.json					# Plugin 配置文件
├── agents/
│   ├── webpage-analyzer.md			# 网页分析 Agent（共用，支持双模式）
│   ├── source-searcher.md			# 信息源搜索 Agent（deep-investigator 专用）
│   ├── report-generator.md			# 报告生成 Agent（deep-investigator 专用）
│   ├── source-processor.md			# 信息源处理 Agent（info-collector 专用）
│   ├── site-evaluator.md			# 信息源评估 Agent（info-collector 专用）
│   └── personel-updater.md			# 个人偏好更新 Agent（info-collector 专用）
├── skills/
│   ├── info-collector/
│   │   └── skill.md				# 信息收集 Skill
│   └── deep-investigator/
│       └── skill.md				# 深度调查 Skill
├── PERSONEL.md						# 用户个人偏好配置（自动创建并更新）
├── SITE.md							# 可靠信息源配置（自动更新）
├── README.md						# 本文件
├── PLUGIN.md						# Plugin 安装和使用指南
├── YYYY-MM-DD/						# 每日信息收集目录（info-collector）
│   ├── {网页标题1}.md
│   ├── {网页标题2}.md
│   ├── {信息源名称1}-总结.md
│   ├── {信息源名称2}-总结.md
│   └── 新增信息源评估.md
├── 深度调查-{对象}-YYYY-MM-DD/		# 深度调查目录（deep-investigator）
│   ├── 搜索-{维度}.md
│   ├── {维度}-{网页标题}.md
│   ├── 维度汇总-{维度}.md
│   └── 信息完整性评估-第N轮.md
├────── 最终报告-YYYY-MM-DD.md
└────── 深度调查报告-{对象}-YYYY-MM-DD.md
```

## 架构设计

### 双 Skill 架构

本项目包含两个独立的 Skill，共享同一套 Agent：

#### 1. info-collector Skill（信息收集）

```
info-collector Skill
    ├── source-processor Agent 1
    │   ├── webpage-analyzer Agent 1.1 (comprehensive 模式)
    │   ├── webpage-analyzer Agent 1.2 (comprehensive 模式)
    │   └── ...
    ├── source-processor Agent 2
    │   ├── webpage-analyzer Agent 2.1 (comprehensive 模式)
    │   └── ...
    ├── ...
    ├── site-evaluator Agent
    └── personel-updater Agent
```

#### 2. deep-investigator Skill（深度调查）

```
deep-investigator Skill
    ├── source-searcher Agent (第1轮)
    │   ├── webpage-analyzer Agent 1.1 (focused 模式)
    │   ├── webpage-analyzer Agent 1.2 (focused 模式)
    │   └── ...
    ├── source-searcher Agent (第2轮补充，如需要)
    │   ├── webpage-analyzer Agent 2.1 (focused 模式)
    │   └── ...
    └── report-generator Agent
```

**职责分工：**

| 模块                         | 职责简介                                             |
|-----------------------------|-----------------------------------------------------|
| **info-collector Skill**    | 流程编排、配置读取、双通道搜索、最终汇总                   |
| **deep-investigator Skill** | 需求分析、多轮搜索评估、深度调查报告生成                   |
| **source-processor Agent**  | 处理单个信息源的搜索和筛选、收集新发现的网站                |
| **source-searcher Agent**   | 执行网络搜索、筛选结果、启动网页分析                      |
| **webpage-analyzer Agent**  | 深度分析单个网页内容（支持 focused/comprehensive 双模式） |
| **site-evaluator Agent**    | 评估新发现的网站、更新 SITE.md                          |
| **report-generator Agent**  | 生成深度调查综合报告                                    |
| **personel-updater Agent**  | 根据用户反馈更新个人偏好配置                             |

### 工作流程

```
用户需求
    ↓
需求分析 → 读取配置 (PERSONEL.md, SITE.md)
    ↓
创建工作目录 (YYYY-MM-DD)
    ↓
    ├─→ 通道A: 从 SITE.md 筛选信息源
    │   └─→ 形成 SITE 类型信息源集
    │
    ├─→ 通道B: 执行 WebSearch 全网搜索
    │   ├─→ 构造 2-3 个搜索查询
    │   ├─→ 并行执行 WebSearch
    │   ├─→ 按域名分组结果
    │   └─→ 形成 WEBSEARCH 类型虚拟信息源集
    │
    ↓
合并两个通道的信息源（去重，SITE 优先）
    ↓
并行启动 source-processor Agents（区分 SITE/WEBSEARCH 类型）
    ├─→ SITE 类型: 使用 site:domain.com 搜索
    └─→ WEBSEARCH 类型: 直接使用预收集的 URL
    ↓
每个 Agent 并行启动 webpage-analyzer Agents
    ↓
收集并保存所有分析结果 + 新发现的网站（带发现途径标记）
    ↓
启动 site-evaluator Agent ← 评估新网站
    ├─→ 应用加权评分系统（根据发现途径）
    ├─→ 得分 ≥ 7.5: 自动添加
    ├─→ 得分 6.0-7.4: 添加并标注"待审核"
    └─→ 得分 < 6.0: 不添加
    ↓
更新 SITE.md（自动添加有价值的网站）
    ↓
生成最终报告 + 新增信息源评估报告
```

## 快速开始

### 1. 作为独立 Plugin 使用

将此项目作为 Plugin 添加到其他 Claude Code 项目中：

```bash
# 在你的项目中
git clone <repository-url> .plugins/InfoCollector
```

### 2. 配置

#### 编辑 PERSONEL.md

设置您的个人偏好：
- 语言偏好（中文/英文）
- 关注领域（科技、政治、经济等）
- 信息类型偏好（新闻、博客、学术）
- 默认时间范围（24小时）

#### 编辑 SITE.md

添加可靠的信息源：

```markdown
## 科技与技术

### 人工智能
- **OpenAI Blog**
  - URL: https://openai.com/blog
  - 类型: AI、GPT、研究
  - 语言: 英文
```

### 3. 使用

#### 使用 info-collector Skill（信息收集）

在 Claude Code 中启动：

```bash
/info-collector
```

或者直接告诉 Claude：

```
帮我收集今天关于人工智能的最新信息
```

#### 使用 deep-investigator Skill（深度调查）

在 Claude Code 中启动：

```bash
/deep-investigator
```

或者直接告诉 Claude：

```
帮我深度调查一下量子计算
```

## 配置说明

### PERSONEL.md 配置项

| 配置项 | 说明 | 默认值 |
|-------------------|------------------------|----------------|
| 主要语言			 | 报告生成使用的主要语言	  | 中文			|
| 次要语言			 | 信息源可包含的其他语言	  | 英文			|
| 默认关注领域		  | 不指定时收集的信息领域	  | 国际与国内要事新闻 |
| 信息类型偏好		  | 偏好的信息形式			 | 新闻、博客、学术等 |
| 内容风格偏好		  | 偏好的内容深度			 | 深度 > 广度		|
| 时间范围			 | 默认收集时间窗口			 | 过去 24 小时		|
| **启用 WebSearch** | 是否启用全网搜索			| 是			  |
| **WebSearch 权重** | 相对于 SITE.md 的权重   | 30%			 |
| **自动添加阈值**	  | 新信息源自动添加的最低分数 | 7.5 分			 |
| **人工审核阈值**	  | 需要人工审核的分数范围	  | 6.0-7.4 分		|

### SITE.md 配置格式

每个信息源需要包含：
- **名称**：信息源的显示名称（粗体）
- **URL**：信息源的访问地址
- **类型**：提供的信息类型标签
- **语言**：内容使用的语言

按以下结构组织：
- 二级标题（##）：大类（如"科技与技术"）
- 三级标题（###）：子类（如"人工智能"）
- 无序列表：具体信息源及元数据

## 使用场景

### info-collector Skill 场景

#### 场景 1：每日科技新闻汇总

```
收集今天的科技新闻
```

系统会：
1. 从 SITE.md 筛选科技类信息源
2. 执行 WebSearch 全网搜索
3. 收集过去 24 小时的相关新闻
4. 生成按领域分类的汇总报告

#### 场景 2：特定领域信息收集

```
帮我收集过去一周关于大语言模型的最新研究和应用进展
```

系统会：
1. 扩展时间范围到 7 天
2. 聚焦 AI/大语言模型相关信息源
3. 包含学术论文、技术博客、新闻报道
4. 生成综合性信息汇总报告

#### 场景 3：多领域信息追踪

```
收集今天的 AI 新闻、网络安全事件和国际要闻
```

系统会：
1. 同时处理多个领域
2. 按领域分类组织报告
3. 提供跨领域的关联分析

### deep-investigator Skill 场景

#### 场景 4：深度调查单个对象

```
帮我深度调查一下 ChatGPT
```

系统会：
1. 分析调查对象类型（单个对象）
2. 确定调查维度（技术原理、发展历史、应用场景等）
3. 执行多轮搜索和网页分析（每轮聚焦不同维度）
4. 评估信息完整性，必要时进行补充搜索
5. 生成全面深入的调查报告

#### 场景 5：深度调查一组对象

```
深度调查主流大语言模型产品（ChatGPT、Claude、Gemini）
```

系统会：
1. 识别调查对象类型（一组对象）
2. 为每个对象收集详细信息
3. 分析对象间的关联、异同和竞争关系
4. 生成包含对比分析的综合报告

#### 场景 6：深度调查一个类别

```
深度调查无代码开发平台这个类别
```

系统会：
1. 识别调查对象类型（一个类别）
2. 定义类别边界和分类体系
3. 收集代表性个体信息
4. 分析行业现状和发展趋势
5. 生成类别全景分析报告

## 技术细节

### 指令设计原则

本项目所有 Skill 和 Agent 定义都遵循以下原则：

1. **明确的执行指令**：所有操作都使用"执行操作"、"使用 XXX 工具"等明确的动作指令
2. **详细的参数说明**：每个工具调用都详细说明所有参数的来源和格式
3. **清晰的流程控制**：使用"对每个"、"等待"、"然后"等明确的流程控制语句
4. **避免模糊示例**：不使用容易混淆的"示例"表述，而是直接给出执行指令

### Agent 定义

#### source-processor Agent

位于 `agents/source-processor.md`，负责：
- **支持双类型信息源**：
  + SITE 类型：使用 WebSearch 或 WebFetch 访问信息源并搜索相关信息
  + WEBSEARCH 类型：直接使用预收集的 URL 列表
- 筛选符合用户需求的信息条目
- 使用 Task 工具并行启动多个 webpage-analyzer Agent（comprehensive 模式）分析每个网页
- 使用 Read 工具读取分析结果，使用 Write 工具汇总生成 `{信息源名称}-总结.md`
- 收集新发现的网站信息（域名、URL、出现次数、主题、**发现途径标记**）

#### webpage-analyzer Agent

位于 `agents/webpage-analyzer.md`，负责：
- **支持双分析模式**：
  + **focused 模式**（聚焦模式）：围绕特定调查对象和维度进行深度分析（用于 deep-investigator）
  + **comprehensive 模式**（全面模式）：客观全面地总结网页内容（用于 info-collector，默认）
- 使用 WebFetch 工具读取单个网页内容
- 根据模式动态构造提取指令
- 提取核心观点和关键信息
- 评估来源可信度
- 使用 Write 工具保存为 `{网页标题}.md` 或 `{维度}-{网页标题}.md`

#### site-evaluator Agent

位于 `agents/site-evaluator.md`，负责：
- 使用 Read 工具读取 SITE.md，提取已存在的网站列表
- **使用加权评分系统**评估新发现的网站：
  + 基础评分：权威性、更新频率、内容深度、出现频率、可访问性（0-10分）
  + 发现途径权重：
    * SITE引用：1.2×
    * WebSearch直接发现：1.0×
    * 混合途径：1.3×
  + 最终得分 = 基础得分 × 权重系数（0-13分）
- 使用 WebFetch 工具验证网站可访问性
- 自动决策：
  + 得分 ≥ 7.5：自动添加
  + 得分 6.0-7.4：添加并标注"待人工审核"
  + 得分 < 6.0：不添加
- 使用 Edit 工具将符合标准的网站添加到 SITE.md 的正确分类下
- 使用 Write 工具生成评估报告 `新增信息源评估.md`

#### source-searcher Agent

位于 `agents/source-searcher.md`，负责（deep-investigator 专用）：
- 执行 WebSearch 搜索（支持多个查询维度）
- 筛选出最相关的搜索结果（每个维度 10-15 条）
- 使用 Task 工具并行启动多个 webpage-analyzer Agent（focused 模式）分析关键网页
- 使用 Read 工具读取分析结果
- 为每个调查维度生成汇总文档 `维度汇总-{维度}.md`
- 保存搜索结果文件 `搜索-{维度}.md`

#### report-generator Agent

位于 `agents/report-generator.md`，负责（deep-investigator 专用）：
- 使用 Read 工具读取所有维度汇总和分析结果
- 进行跨维度综合分析，建立信息关联
- 根据调查对象类型（单个/一组/一个类别）组织报告框架
- 生成结构清晰、内容详实的深度调查报告
- 使用段落形式叙述，包含引用索引
- 保存最终报告 `深度调查报告-{对象}-{日期}.md` 到项目根目录

#### personel-updater Agent

位于 `agents/personel-updater.md`，负责（info-collector 专用）：
- 分析用户反馈的有效性和合理性
- 使用 Read 工具读取当前 PERSONEL.md 配置
- 判断是否需要更新配置
- 使用 Edit 工具执行增量更新（不重写整个文件）
- 生成更新报告，说明变更内容和理由
- 为用户提供后续建议

### 文件命名规范

- **日期目录**：`YYYY-MM-DD`（如 `2025-10-27`）
- **网页分析**：`{网页标题}.md`
- **信息源总结**：`{信息源名称}-总结.md`
- **新增信息源评估**：`新增信息源评估.md`
- **最终报告**：`最终报告.md`

### 最终报告格式

```markdown
# 信息收集报告

**收集时间范围**: 过去 24 小时
**收集领域**: 科技、国际要闻
**报告生成时间**: 2025-10-27 14:30
**信息源数量**: 5
**信息条目数量**: 23

---

## 一、科技与技术

### 人工智能

根据最新报道[1][2]，OpenAI 发布了新版本的 GPT 模型，在多项基准测试中
表现出色。与此同时，Anthropic 也宣布[3]推出了 Claude 的重大更新...

（使用段落形式叙述，不用列表）

### 网络安全

...

## 二、国际要闻

...

---

## 信息源列表

1. OpenAI Blog - https://openai.com/blog
2. TechCrunch - https://techcrunch.com/
3. Anthropic News - https://www.anthropic.com/news
...
```

**关键特点：**
- ❌ 不使用列表形式展示信息（除非用户要求）
- ✅ 使用段落形式进行叙述性分析
- ✅ 每条信息都有引用标记 [1][2]
- ✅ 末尾有完整的信息源列表

## 性能优化

1. **并行处理**
   - 多个 source-processor Agent 并行启动
   - 每个 Agent 内部并行启动多个 webpage-analyzer Agent
2. **错误处理**
   - 单个信息源失败不影响整体流程
   - 单个网页失败不影响该信息源其他网页
3. **避免重复**
   - 文件系统缓存机制
   - 合并重复信息

## 限制与注意事项

1. **访问限制**：某些网站可能有反爬虫机制
2. **时间消耗**：
   - 双通道搜索会增加处理时间
   - WEBSEARCH 类型信息源较多时可能较慢
3. **API 限制**：
   - WebSearch 调用次数限制（建议最多 3 次查询）
   - 网页抓取受 API 调用限制
4. **内容准确性**：自动总结可能存在偏差，建议人工审核
5. **新信息源质量**：
   - WebSearch 发现的网站质量参差不齐
   - 加权评分系统已考虑发现途径，但仍建议定期审核
   - 得分 6.0-7.4 的网站会自动标注"待审核"

## 故障排除

### Skill 未启动

确保文件路径正确：

```
skills/info-collector/skill.md
skills/deep-investigator/skill.md
```

### Agent 未找到

确保 Agent 文件存在：

```
agents/source-processor.md
agents/source-searcher.md
agents/webpage-analyzer.md
agents/site-evaluator.md
agents/report-generator.md
agents/personel-updater.md
```

### 配置文件缺失

确保以下文件在项目根目录：
- `PERSONEL.md`
- `SITE.md`

### 信息收集失败

检查：
- 网络连接是否正常
- 信息源 URL 是否可访问
- 是否触发了访问限制

## 贡献指南

欢迎提交 Issue 和 Pull Request 改进本项目。

### 开发建议

1. 添加新的信息源类型
2. 优化网页内容提取逻辑
3. 改进最终报告的格式
4. 增加更多语言支持
5. ✅ **已实现**：双通道搜索（SITE.md + WebSearch）
6. ✅ **已实现**：加权评分系统（根据发现途径）
7. 添加信息源质量监控，自动清理低质量或失效的网站
8. 优化 WebSearch 查询构造策略，提高相关性
9. 添加用户反馈机制，手动调整信息源评分

## 许可证

[MIT License](LICENSE)

---

**版本**：1.2.2
**最后更新**：2025-10-28
**作为独立 Plugin 使用**：完全自包含，可移植

**主要功能**：
- ✅ 首次启动交互式初始化（自动生成 PERSONEL.md）
- ✅ 自动发现并添加新信息源到 SITE.md
- ✅ 双通道搜索（SITE.md + WebSearch 全网搜索）
- ✅ 加权评分系统（根据发现途径自动调整权重）
- ✅ 智能去重（域名级别，SITE 优先）
- ✅ 最终报告保存到项目根目录（便于查看）
- ✅ 根据用户反馈自动更新 PERSONEL.md 配置（第六阶段新增）
- ✅ 完整的"收集-反馈-优化"闭环工作流
- ✅ 深度调查分析功能（deep-investigator Skill）

**版本历史**：
- v1.2.2 (2025-10-28): webpage-analyzer Agent 支持双模式（focused/comprehensive），统一服务于 deep-investigator 和 info-collector 两个 Skill
- v1.2.1 (2025-10-28): deep-investigator 启动网络搜索前先检查本地是否已有相关信息
- v1.2.0 (2025-10-27): 新增深度调查分析功能（deep-investigator Skill）
- v1.1.2 (2025-10-27): 新增第六阶段 - 根据用户反馈自动更新 PERSONEL.md 配置，形成"收集-反馈-优化"闭环
- v1.1.1 (2025-10-27): 新增首次启动交互式初始化和最终报告项目根目录输出
- v1.1.0 (2025-10-27): 新增双通道搜索和加权评分系统
- v1.0.0 (2025-10-27): 初始版本（info-collector Skill），支持单通道 SITE.md 搜索
