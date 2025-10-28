# InfoCollector Plugin 安装和使用指南

本指南说明如何在你的 Claude Code 项目中安装和使用智能信息收集系统。

## 📦 快速开始

### 方式 1：通过 Marketplace 添加（推荐）

最简单的方式 - 使用官方 Marketplace：

```bash
# 添加 CCMarketplace
/plugin marketplace add lostabaddon/CCMarketplace
```

然后在可用 Plugin 列表中选择 `info-collector` 并安装

### 方式 2：本地安装

```bash
# Clone Repo
git clone https://github.com/LostAbaddon/InfoCollector.git
cd InfoCollector
# 添加本地测试用 Marketplace
/plugin marketplace add ./marketplace.json
```

## 🎯 Plugin 包含的内容

根据 `.claude-plugin/plugin.json` 的定义，此 Plugin 包含：

### 双 Skill 架构
- **info-collector** (`./skills/info-collector/skill.md`)
  - 信息收集 Skill：从多个可靠信息源自动收集信息
  - 双通道搜索：SITE.md 可靠源 + WebSearch 全网搜索
  - 用户可通过 `/info-collector` 或自然语言调用

- **deep-investigator** (`./skills/deep-investigator/skill.md`)
  - 深度调查 Skill：对特定需求进行多维度深入分析
  - 多轮搜索评估：反思补充，确保信息完整性
  - 用户可通过 `/deep-investigator` 或自然语言调用

### 6 个专用 Agent
- **source-processor** (`./agents/source-processor.md`)
  - 处理单个信息源，进行网页检索和内容筛选
  - 支持两种信息源类型：SITE（可靠源）和 WEBSEARCH（网络搜索）

- **webpage-analyzer** (`./agents/webpage-analyzer.md`)
  - 深度分析单个网页内容（共用 Agent）
  - 支持双分析模式：focused（聚焦，用于 deep-investigator）和 comprehensive（全面，用于 info-collector）

- **source-searcher** (`./agents/source-searcher.md`)
  - 执行网络搜索和结果筛选（deep-investigator 专用）
  - 支持多维度搜索

- **report-generator** (`./agents/report-generator.md`)
  - 生成深度调查综合报告（deep-investigator 专用）
  - 支持三种对象类型：单个对象、一组对象、一个类别

- **site-evaluator** (`./agents/site-evaluator.md`)
  - 评估新发现的网站，自动更新 SITE.md
  - 加权评分系统：根据发现途径自动调整权重

- **personel-updater** (`./agents/personel-updater.md`)
  - 根据用户反馈更新个人偏好配置（info-collector 专用）

## 配置文件

安装 Plugin 后，需要在你的项目中创建配置文件：

### 1. PERSONEL.md - 个人偏好配置

```markdown
# 个人偏好设置

## 信息收集偏好

### 语言设置
- **主要语言**: 中文
- **次要语言**: 英文

### 关注领域
- 人工智能与机器学习
- 科技创业与投融资
- 网络安全与隐私
- 国际与国内要事

### 信息类型偏好
- 新闻报道 (优先)
- 技术博客
- 学术论文

### 时间范围
- **默认时间范围**: 过去 24 小时
- **允许范围**: 1 小时 ~ 30 天

### WebSearch 配置
- **启用 WebSearch**: 是
- **WebSearch 权重**: 30%（相对于 SITE.md）

### 信息源管理
- **自动添加阈值**: 7.5 分
- **人工审核范围**: 6.0-7.4 分
- **不添加**: < 6.0 分
```

### 2. SITE.md - 可靠信息源配置

```markdown
# 可靠信息源库

## 科技与技术

### 人工智能
- **OpenAI Blog**
  - URL: https://openai.com/blog
  - 类型: AI、GPT、研究
  - 语言: 英文

- **机器之心**
  - URL: https://jiqizhixin.com/
  - 类型: AI、技术、行业
  - 语言: 中文

### 创业与投融资
- **TechCrunch**
  - URL: https://techcrunch.com/
  - 类型: 创业、融资、产品
  - 语言: 英文

## 国际与国内要事

### 新闻媒体
- **BBC News**
  - URL: https://www.bbc.com/news
  - 类型: 国际新闻、时事
  - 语言: 英文

（...添加更多信息源）
```

## 📖 使用方式

安装并配置后，有两个 Skill 可供使用：

### 使用 info-collector（信息收集）

#### 方式 1：Skill 命令
```bash
/info-collector
```

#### 方式 2：自然语言触发
```
帮我收集今天关于人工智能的最新信息

收集过去一周的科技新闻

收集今天的 AI 新闻、网络安全事件和国际要闻
```

### 使用 deep-investigator（深度调查）

#### 方式 1：Skill 命令
```bash
/deep-investigator
```

#### 方式 2：自然语言触发
```
帮我深度调查一下 ChatGPT

深度调查主流大语言模型产品（ChatGPT、Claude、Gemini）

深度调查无代码开发平台这个类别
```

### info-collector 示例用法

#### 示例 1：每日科技新闻汇总

```
收集今天的科技新闻
```

系统会：
1. 从 SITE.md 中筛选科技相关信息源
2. 执行 WebSearch 全网搜索（双通道搜索）
3. 并行处理所有信息源，分析每个网页
4. 生成分领域的结构化新闻汇总报告
5. 自动发现和评估新信息源

#### 示例 2：特定领域信息收集

```
帮我收集过去一周关于大语言模型的最新研究和应用进展
```

系统会：
1. 扩展时间范围到 7 天
2. 聚焦 AI/大语言模型相关信息源
3. 包含学术论文、技术博客、新闻报道
4. 生成综合性信息汇总报告

#### 示例 3：多领域信息追踪

```
收集今天的 AI 新闻、网络安全事件和国际要闻
```

系统会：
1. 同时处理多个领域
2. 按领域分类组织报告
3. 生成综合性信息汇总

### deep-investigator 示例用法

#### 示例 4：单个对象深度调查

```
帮我深度调查一下 ChatGPT
```

系统会：
1. 分析调查对象（单个产品）
2. 确定调查维度（技术原理、发展历史、应用场景等）
3. 多轮搜索和网页分析（每轮聚焦不同维度）
4. 评估信息完整性，必要时补充搜索
5. 生成全面深入的调查报告

#### 示例 5：一组对象对比分析

```
深度调查主流大语言模型产品（ChatGPT、Claude、Gemini）
```

系统会：
1. 识别调查对象类型（一组产品）
2. 为每个产品收集详细信息
3. 分析产品间的关联、异同和竞争关系
4. 生成包含对比分析的综合报告

#### 示例 6：类别全景分析

```
深度调查无代码开发平台这个类别
```

系统会：
1. 识别调查对象类型（一个类别）
2. 定义类别边界和分类体系
3. 收集代表性产品信息
4. 分析行业现状和发展趋势
5. 生成类别全景分析报告

## 📁 输出内容

### info-collector 输出

执行完成后，会在当前项目创建日期目录（如 `./2025-10-27/`），包含：

```
2025-10-27/
├── 最终报告.md                    # 主报告：分领域的综合分析
├── 新增信息源评估.md              # 评估新发现的网站
├── {信息源名称1}-总结.md          # 各信息源的详细总结
├── {信息源名称2}-总结.md
├── {网页标题1}.md                 # 单个网页的深度分析
├── {网页标题2}.md
└── ...
```

**最终报告格式：**
- **执行摘要**：关键发现总结
- **分领域内容**：按领域分类的段落形式叙述
- **引用索引**：所有引用的完整信息源列表
- **新增信息源**：本次发现的有价值网站

### deep-investigator 输出

执行完成后，会在当前项目创建调查目录和报告（如 `./深度调查-ChatGPT-2025-10-27/`），包含：

```
深度调查-ChatGPT-2025-10-27/
├── 搜索-技术原理.md               # 各维度的搜索结果
├── 搜索-发展历史.md
├── 技术原理-{网页标题}.md         # 各维度的详细分析
├── 发展历史-{网页标题}.md
├── 维度汇总-技术原理.md           # 各维度的综合总结
├── 维度汇总-发展历史.md
├── 信息完整性评估-第1轮.md        # 信息完整性评估
└── ...
```

**最终报告（项目根目录）：**
```
深度调查报告-ChatGPT-2025-10-27.md
```

**深度报告格式：**
- **调查对象分析**：对象概述、类型识别
- **多维度分析**：按各调查维度详细叙述
- **综合评估**：信息完整性、补充搜索说明
- **引用索引**：所有引用源的完整列表

## 📋 Marketplace 文件说明

本项目包含 `.claude-plugin/marketplace.json` 文件，这是 Claude Code 的官方 Marketplace 配置文件。

### 文件位置
```
./.claude-plugin/marketplace.json
```

### 文件作用

Marketplace 文件允许：
1. **集中管理** - 在一个文件中定义 Plugin 的所有信息
2. **灵活安装** - 用户可以通过 marketplace 直接发现和安装 Plugin
3. **自定义分类** - 为 Plugin 提供详细的元数据和分类信息
4. **扩展性** - 未来可以在同一个 marketplace 中添加多个 Plugin

### 用户如何使用

**方式 A：自动发现（推荐）**
```bash
/plugin marketplace add https://github.com/lostabaddon/InfoCollector.git
```
Claude Code 会自动找到并加载 marketplace.json

**方式 B：直接指定 marketplace.json**
```bash
/plugin marketplace add https://raw.githubusercontent.com/lostabaddon/InfoCollector/main/.claude-plugin/marketplace.json
```

**方式 C：本地 marketplace 文件**
```bash
/plugin marketplace add /path/to/InfoCollector/.claude-plugin/marketplace.json
```

### 创建自己的 Marketplace（高级用法）

如果你想管理多个 Plugin，可以创建自定义 marketplace.json：

```json
{
  "name": "my-custom-marketplace",
  "owner": {
    "name": "Your Name",
    "email": "your@email.com"
  },
  "plugins": [
    {
      "name": "info-collector",
      "source": "https://github.com/lostabaddon/InfoCollector.git",
      "version": "1.0.0",
      "description": "..."
    }
  ]
}
```

## ⚙️ 核心特性

### info-collector 特性
- ✅ **双通道搜索**：SITE.md 可靠源 + WebSearch 全网搜索
- ✅ **并行处理**：多个信息源和网页同时处理
- ✅ **智能筛选**：根据用户需求自动筛选信息
- ✅ **自动发现新源**：智能评估并添加新信息源
- ✅ **加权评分系统**：根据发现途径自动调整权重
- ✅ **反馈闭环**：根据用户反馈优化配置

### deep-investigator 特性
- ✅ **多维度分析**：自动识别调查维度
- ✅ **多轮搜索**：反思评估 + 补充搜索
- ✅ **对象类型识别**：支持单个对象、一组对象、一个类别
- ✅ **信息完整性评估**：自动检查是否需要补充搜索
- ✅ **聚焦模式分析**：按维度进行深度网页分析

## ⚙️ 使用的工具和权限

Plugin 自动使用的工具（已在 `plugin.json` 中声明）：

- `Task` - 启动内部 Agent（并行处理）
- `Read` / `Write` / `Edit` - 文件操作
- `Glob` - 文件搜索和匹配
- `WebSearch` / `WebFetch` - 网页访问和内容抓取
- `Bash` - 目录创建和文件操作

## 🔧 常见问题

### Q: 首次启动时系统是否会提示我创建 PERSONEL.md？

**A:** 是的！系统会自动检测并创建。系统会询问：
- 主要语言选择
- 默认关注领域

**你不需要手动创建此文件！** 详见 HOWTOUSE.md 了解完整流程。

### Q: Plugin 安装后不工作？

**A:** 检查以下几点：
1. 确保 SITE.md 存在于项目根目录
2. PERSONEL.md 会在首次启动时自动创建
3. 检查 SITE.md 格式是否正确（标题、URL、类型、语言）
4. 查看 HOWTOUSE.md 了解详细配置说明

### Q: 两个 Skill 有什么区别？

**A:**
- **info-collector**：用于定期收集信息（新闻、资讯）
  - 双通道搜索（SITE.md + WebSearch）
  - 快速覆盖广范围
  - 输出日期目录格式

- **deep-investigator**：用于深入调查特定对象
  - 多维度多轮搜索
  - 深度内容分析
  - 输出调查报告格式

### Q: 如何自定义偏好设置？

**A:** 编辑项目根目录的文件：
- `PERSONEL.md` - 设置语言、关注领域、时间范围、WebSearch 权重等
- `SITE.md` - 添加/移除信息源、编辑已有信息源

详见 HOWTOUSE.md 中的详细说明。

### Q: Plugin 如何自动更新信息源？

**A:** info-collector 有自动发现机制：
1. **发现新网站** - 在 WebSearch 和信息源中发现新网站
2. **评估价值** - 使用加权评分系统评估质量
3. **自动添加** - 符合标准的网站自动添加到 SITE.md

**加权评分标准：**
- **发现途径权重**：SITE 引用 1.2×，WebSearch 直接发现 1.0×，混合途径 1.3×
- **≥ 7.5 分**：自动添加
- **6.0-7.4 分**：添加并标注"待审核"
- **< 6.0 分**：不添加

### Q: 生成的文件存在哪里？

**A:** 输出位置根据 Skill 不同：

**info-collector 输出：**
```
./YYYY-MM-DD/        （如 ./2025-10-27/）
├── 最终报告.md
├── 新增信息源评估.md
└── ...
```

**deep-investigator 输出：**
```
./深度调查-{对象}-YYYY-MM-DD/
├── 搜索-*.md
├── {维度}-*.md
├── 维度汇总-*.md
└── ...

深度调查报告-{对象}-YYYY-MM-DD.md  （项目根目录）
```

## 📚 进阶配置

### 调整 WebSearch 权重（info-collector）

在 `PERSONEL.md` 中修改：

```markdown
### 双通道搜索配置
- **启用广泛网络搜索（WebSearch）**: 是
- **WebSearch 相对权重**: 30%  # 改为 0%-100%
```

**权重说明：**
- **30%** (默认) - 平衡 SITE.md 和 WebSearch
- **0%** - 仅使用 SITE.md
- **50%** - 等权重
- **70%** - 强调网络发现，可能找到更多新信息源

### 定制新信息源发现标准

在 `PERSONEL.md` 中修改：

```markdown
### 新信息源发现与管理
- **自动添加新信息源**: 是
- **自动添加阈值**: 7.5 分（降低此值使系统更容易添加新源）
- **人工审核阈值**: 6.0-7.4 分
```

**site-evaluator Agent 评分维度：**
- **权威性** (0-10 分) - 网站的专业度和知名度
- **更新频率** (0-10 分) - 内容更新的及时性
- **内容深度** (0-10 分) - 内容的详尽程度
- **出现频率** (0-10 分) - 在搜索中出现的次数
- **可访问性** (0-10 分) - 网站的稳定性和可用性

**发现途径权重系数：**
- SITE 引用：1.2×（来自已信任源的推荐）
- WebSearch 直接发现：1.0×（直接搜索发现）
- 混合途径：1.3×（多个信息源确认）

### 自定义关注领域（info-collector）

编辑 `PERSONEL.md` 的关注领域部分，根据你的兴趣添加或修改：

```markdown
### 默认关注领域
1. **人工智能与机器学习**
   - 大语言模型
   - 图像生成
   - AI 芯片

2. **科技与创业**
   - 科技新闻
   - 创业融资
   - 产品发布
```

### 深度调查配置（deep-investigator）

deep-investigator 会根据调查对象自动确定分析维度，无需预配置。

**支持的调查类型：**
- 单个对象（如 "ChatGPT"）→ 自动分析：技术原理、发展历史、应用场景、竞争优势、隐私安全等
- 一组对象（如 "主流大语言模型"）→ 自动进行对比分析
- 一个类别（如 "无代码开发平台"）→ 自动分析行业全景和发展趋势

## 🤝 贡献与反馈

- 发现问题？提交 [Issue](https://github.com/lostabaddon/InfoCollector/issues)
- 改进建议？提交 [Pull Request](https://github.com/lostabaddon/InfoCollector/pulls)
- 新增信息源建议？编辑 SITE.md 并提交 PR

## 📜 许可证

MIT License - 详见 [LICENSE](./LICENSE)

---

**版本**: 1.2.2
**最后更新**: 2025-10-28
**作者**: LostAbaddon
**仓库**: https://github.com/lostabaddon/InfoCollector

---

## 版本历史

- **v1.2.2** (2025-10-28)
  - webpage-analyzer Agent 支持双模式（focused/comprehensive）
  - 统一服务于 deep-investigator 和 info-collector 两个 Skill
  - Plugin.md 文档全面更新，增加 deep-investigator 使用说明

- **v1.2.1** (2025-10-28)
  - deep-investigator 启动网络搜索前先检查本地是否已有相关信息

- **v1.2.0** (2025-10-27)
  - 新增深度调查分析功能（deep-investigator Skill）

- **v1.1.2** (2025-10-27)
  - 新增第六阶段 - 根据用户反馈自动更新 PERSONEL.md 配置

- **v1.1.1** (2025-10-27)
  - 新增首次启动交互式初始化和最终报告项目根目录输出

- **v1.1.0** (2025-10-27)
  - 新增双通道搜索和加权评分系统

- **v1.0.0** (2025-10-27)
  - 初始版本（info-collector Skill），支持单通道 SITE.md 搜索
