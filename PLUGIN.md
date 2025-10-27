# InfoCollection Plugin 安装和使用指南

本指南说明如何在你的 Claude Code 项目中安装和使用。

## 📦 快速开始

### 方式 1：通过官方 Marketplace 添加（推荐）

最简单的方式 - 项目包含 marketplace.json 文件：

```bash
# 添加此项目的 marketplace
/plugin marketplace add https://github.com/lostabaddon/InfoCollection.git

# 或使用原始 marketplace.json URL
/plugin marketplace add https://raw.githubusercontent.com/lostabaddon/InfoCollection/main/.claude-plugin/marketplace.json
```

然后在可用 Plugin 列表中选择 `info-collector` 并安装：

```bash
/plugin install info-collector@lostabaddon-marketplace
```

### 方式 2：直接从 Git 仓库安装

如果你的项目已初始化 Claude Code：

```bash
# 添加为 Plugin marketplace
/plugin marketplace add https://github.com/lostabaddon/InfoCollection.git
```

然后列表中会出现 `info-collector` Plugin，直接安装：

```bash
/plugin install info-collector
```

### 方式 3：本地开发模式

用于本地开发或测试：

```bash
# 方式 A: 克隆到本地
git clone https://github.com/lostabaddon/InfoCollection.git
cd InfoCollection

# 方式 B: 作为 marketplace 添加（本地路径）
/plugin marketplace add ./  # 本地路径

# 安装
/plugin install info-collector
```

### 方式 4：使用本地 Marketplace 文件

如果你已下载或克隆了本仓库：

```bash
# 添加本地 marketplace 文件
/plugin marketplace add /path/to/InfoCollection/.claude-plugin/marketplace.json

# 安装 Plugin
/plugin install info-collector@lostabaddon-marketplace
```

## 🎯 Plugin 包含的内容

根据 `.claude-plugin/plugin.json` 的定义，此 Plugin 包含：

### Skill
- **info-collector** (`./skills/info-collector/SKILL.md`)
  - 主要信息收集和分析 Skill
  - 用户可通过 `/info-collector` 或自然语言调用

### Agents
- **source-processor** (`./agents/source-processor.md`)
  - 处理单个信息源，进行网页检索和内容筛选

- **webpage-analyzer** (`./agents/webpage-analyzer.md`)
  - 深度分析单个网页内容

- **site-evaluator** (`./agents/site-evaluator.md`)
  - 评估新发现的网站，自动更新 SITE.md

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

安装并配置后，使用 Plugin：

### 方式 1：使用 Skill 命令

```bash
/info-collector
```

然后按照交互式提示说明你的需求。

### 方式 2：自然语言触发

直接告诉 Claude 你的需求，它会自动使用该 Skill：

```
帮我收集今天关于 AI 的最新新闻

帮我搜集过去一周关于大语言模型的研究进展

收集今天的科技新闻和国际要事
```

### 示例用法

#### 示例 1：每日科技新闻汇总

```
收集今天的科技新闻
```

系统会：
1. 从 SITE.md 中筛选科技相关信息源
2. 执行 WebSearch 全网搜索
3. 并行处理所有信息源
4. 生成结构化新闻汇总报告

#### 示例 2：特定领域深度研究

```
帮我收集过去一周关于量子计算的最新进展和应用
```

系统会：
1. 扩展时间范围到 7 天
2. 聚焦量子计算相关信息源
3. 包含学术论文、技术博客、新闻报道
4. 生成综合性深度研究报告

#### 示例 3：多领域信息追踪

```
收集今天的 AI 新闻、网络安全事件和国际要闻
```

系统会：
1. 同时处理多个领域
2. 按领域分类组织报告
3. 提供跨领域的关联分析

## 📁 输出内容

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

### 最终报告格式

- **执行摘要**：3-5 段的关键发现总结
- **分领域内容**：按信息领域分类，段落形式叙述
- **引用索引**：所有引用的完整信息源列表
- **新增信息源**：本次发现的有价值网站

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
/plugin marketplace add https://github.com/lostabaddon/InfoCollection.git
```
Claude Code 会自动找到并加载 marketplace.json

**方式 B：直接指定 marketplace.json**
```bash
/plugin marketplace add https://raw.githubusercontent.com/lostabaddon/InfoCollection/main/.claude-plugin/marketplace.json
```

**方式 C：本地 marketplace 文件**
```bash
/plugin marketplace add /path/to/InfoCollection/.claude-plugin/marketplace.json
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
      "version": "1.1.2",
      "description": "..."
    }
  ]
}
```

## ⚙️ 权限配置

Plugin 需要以下权限（已在 `plugin.json` 中声明）：

```json
{
  "skills": [...],
  "agents": [...]
}
```

自动使用的工具：
- `Task` - 启动内部 Agent
- `Read` / `Write` / `Edit` - 文件操作
- `Glob` - 文件搜索
- `WebSearch` / `WebFetch` - 网页访问
- `Bash` - 目录创建

## 🔧 常见问题

### Q: Plugin 安装后不工作？

**A:** 检查以下几点：
1. 确保 PERSONEL.md 和 SITE.md 存在于项目根目录
2. 运行 `claude --debug` 查看详细错误信息
3. 检查 SITE.md 格式是否正确

### Q: 如何自定义偏好设置？

**A:** 编辑项目根目录的 `PERSONEL.md` 和 `SITE.md`：
- `PERSONEL.md` - 设置语言、关注领域、时间范围等
- `SITE.md` - 添加/移除信息源

### Q: Plugin 如何自动更新信息源？

**A:** 系统有三个自动机制：
1. **发现新网站** - 在 WebSearch 和信息源中发现新网站
2. **评估价值** - 使用加权评分系统评估质量
3. **自动添加** - 符合标准的网站自动添加到 SITE.md

评分标准：
- ≥ 7.5 分：自动添加
- 6.0-7.4 分：添加并标注"待审核"
- < 6.0 分：不添加

### Q: 生成的文件存在哪里？

**A:** 所有输出保存在项目根目录下的日期文件夹中：
```
./YYYY-MM-DD/
```

例如今天的日期是 2025-10-27，文件会在 `./2025-10-27/` 中。

## 📚 进阶配置

### 调整 WebSearch 权重

在 `PERSONEL.md` 中修改：

```markdown
### WebSearch 配置
- **启用 WebSearch**: 是/否
- **WebSearch 权重**: 30%  # 改为 0%-100%
```

权重说明：
- 30% (默认) - 平衡 SITE.md 和 WebSearch
- 0% - 仅使用 SITE.md
- 50% - 等权重
- 100% - 仅使用 WebSearch

### 定制信息源评分标准

site-evaluator Agent 使用以下评分维度：
- **权威性** (0-10 分) - 网站的专业度和知名度
- **更新频率** (0-10 分) - 内容更新的及时性
- **内容深度** (0-10 分) - 内容的详尽程度
- **出现频率** (0-10 分) - 在搜索中出现的次数
- **可访问性** (0-10 分) - 网站的稳定性和可用性

发现途径权重：
- SITE 引用：1.2×
- WebSearch 直接发现：1.0×
- 混合途径：1.3×

## 🤝 贡献与反馈

- 发现问题？提交 [Issue](https://github.com/lostabaddon/InfoCollection/issues)
- 改进建议？提交 [Pull Request](https://github.com/lostabaddon/InfoCollection/pulls)
- 新增信息源建议？编辑 SITE.md 并提交 PR

## 📜 许可证

MIT License - 详见 [LICENSE](./LICENSE)

---

**版本**: 1.1.2
**最后更新**: 2025-10-27
**作者**: LostAbaddon
**仓库**: https://github.com/lostabaddon/InfoCollection
