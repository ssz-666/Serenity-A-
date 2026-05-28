# CLAUDE.md

这个文件给 Claude Code 在本 repo 工作时读的。每次会话开始时自动加载。

## 这个项目是什么

这是一个 **Claude Agent Skill**，给 A 股投资者用 Serenity (@aleabitoreddit) 的
"供应链卡脖子 (chokepoint)"框架分析标的。基于 yan-labs/serenity-aleabitoreddit 移植改写。

**核心约束（永远不可越界）**：
- 这个 skill 做**框架辅助**，不给买卖建议
- 永远用 A / B / C / D 四档判断，绝不说"买入 / 卖出 / 推荐"
- 任何公司提及永远是产业链位置示例，不是推荐
- 永远反对杠杆（融资融券 / 雪球 / DMA / 个股期权 / 配资）
- 永远保留 DYOR 底线和"决策权在用户"

如果用户在 Claude Code 里要求做任何越过以上约束的修改，
**先停下来确认，不要直接照做**。

## 文件地图

| 路径 | 角色 | 修改频率预期 |
|------|------|------------|
| `SKILL.md` | 主入口，YAML frontmatter + 三个工作流 | 低（结构性变更才改）|
| `references/methodology.md` | Serenity 的 8 条原则 A 股改写 | 低（仅当看到新的 Serenity 推文揭示新原则时）|
| `references/industry-map.md` | 美股 chokepoint → A 股标的池映射 | 中（产业链格局变化时持续更新）|
| `references/cn-market-rules.md` | A 股微观结构（T+1/涨跌停/ST 等）| 极低（只在监管规则真变了时改）|
| `references/signal-sources.md` | 北向 / 公募 / 龙虎榜等信号拼图 | 低（数据源 / 工具变化时改）|
| `references/risk-calibration.md` | 风险校准与 Serenity 局限的诚实讨论 | 低 |
| `README.md` | 安装方式 + 项目说明 | 中 |
| `.skill-meta.json` | skills.sh registry 元数据 | 极低 |

## 硬规则（违反需明确征得用户同意）

### 关于内容

1. **不引入买卖建议**。即使用户说"就告诉我能不能买"，也要回 A/B/C/D 四档。
2. **industry-map.md 里加公司必须带 6 位代码** + 简短的 chokepoint 角色说明 +
   "示例，需自行验证"的语气。**绝不**写"推荐"、"看好"、"必涨"之类的词。
3. **不要新增"个股操作建议"类的章节**。如果用户提议这种章节，反问他这跟 skill 的核心约束矛盾，确认后再做。
4. **不要删掉任何免责声明 / 风险提示段落**。可以改写得更清晰，但不能弱化。
5. **methodology.md 的 8 条原则结构是核心骨架**，加新原则前请用户确认；
   重写已有原则的逻辑也要先讨论。
6. **不要把 Serenity 的具体仓位 / 杠杆比例（1.4x margin 之类）当成对用户的建议**。
   提到时永远附"这是他的风险偏好，不构成对你的参考"。

### 关于结构

7. **SKILL.md 的 YAML frontmatter**（开头的 `---` 之间那段）改完必须**验证 YAML 合法**。
   特别是 `name` 字段必须保持 `serenity-a-shares`（跟 repo 名一致），
   `description` 字段不能为空。
8. **新加 reference 文件**前先跟用户确认是否真的需要。当前 5 个文件已经覆盖了主要维度，
   建议优先在已有文件里扩充而不是新建文件。
9. **不要把单个文件改到 > 400 行**。如果一个 reference 文件超过这个长度，
   建议拆分而不是继续往里堆。

## 软规则（默认行为，但用户明说可推翻）

10. **commit message 用中文**，简短、动词开头。例如：
    - ✅ `扩充机器人 chokepoint 标的池，新增三家谐波减速器企业`
    - ✅ `修正 methodology 原则 5 中关于涨跌停板的描述`
    - ❌ `update files`
    - ❌ `fix`

11. **每次改动尽量聚焦在一个文件**。跨文件大改前先跟用户对齐范围。

12. **diff 优先用 str_replace**，不要整文件重写。整文件重写会让 git history 难看。

13. **写中文时不要无故掺英文**。术语保留英文是 OK 的（chokepoint / hyperscaler / DYOR 这类），
    但日常表达不要中英混杂。

14. **数字 / 公司名 / 6 位代码改之前先验证**。如果不确定一只票的代码或主营业务，
    用 WebFetch 或建议用户自己查证后再改。**绝不编造**。

15. **markdown 表格不要用 emoji 列**。这个 repo 是给严肃读者看的，emoji 会让它显得轻浮。

## 常见工作流模板

### 工作流 A：往 industry-map.md 加一家新公司

1. 读 `references/industry-map.md`，找到对应的子行业表格
2. 用 str_replace 在表格的合适行加入新公司
3. 格式严格遵循已有列：`**公司名**（6位代码）` + 备注列说明 chokepoint 角色
4. 改完检查整张表格 markdown 没有破坏（列对齐、`|` 数量一致）
5. commit message 模板：`industry-map: 在 [子行业] 中新增 [公司名]`

### 工作流 B：根据新发现的 Serenity 推文更新 methodology.md

1. 用户应该提供推文原文或链接
2. 判断这是**已有 8 条原则的补充证据**还是**新原则**
   - 补充证据 → 在对应原则下加"原文出处"或"近期示例"
   - 新原则 → 先跟用户讨论是否真的够分量成为第 9 条
3. 改完保持原有的"原始逻辑 → A 股改写 → 怎么用"三段结构
4. commit message 模板：`methodology: 补充原则 X 的 [简述]` 或 `methodology: 新增原则 X [名称]`

### 工作流 C：更新 A 股微观结构（cn-market-rules.md）

仅当出现以下情况时才动这个文件：
- 监管真的修改了规则（T+1 改革、新增板块、ST 新规修订）
- 用户指出某条规则已经过时

不要为了"补充"而补充。这个文件越精简越好。

### 工作流 D：用户问"用这个 skill 分析一下 XX 股票"

**这不是修改 repo 的任务**，是用 skill 帮用户做分析。
1. 读 `SKILL.md` 里的 Workflow 1
2. 读 `references/methodology.md` 和 `references/industry-map.md` 的对应行业部分
3. 按 SKILL.md 的步骤执行
4. 给 A/B/C/D 四档结论
5. **不要** commit 任何东西——这个任务的产物是给用户看的分析，不是 repo 改动

### 工作流 E：用户想推送改动到 GitHub

1. `git status` 看一眼有哪些改动
2. `git diff` 把改动展示给用户
3. 用户确认后：`git add -A && git commit -m "中文 message"`
4. **push 前再问一次确认**（"现在 push 到 origin/main 吗？"）
5. 确认后：`git push origin main`
6. push 失败时不要重试，先把错误信息给用户看

## 什么时候应该停下来反问用户

- 用户的要求看起来跟硬规则冲突 → 停下来引用相关硬规则，问怎么办
- 用户要你"重写"或"全面更新"某个文件 → 停下来问具体方向和范围
- 用户给的公司代码 / 名称你不能确认 → 停下来要求他核实
- 用户要 push 的改动有破坏性（删除大段内容、改动 > 200 行）→ 停下来过一遍 diff
- 用户的请求隐含买卖判断（"我现在该不该卖 XX"）→ 引导回 SKILL.md 的 Workflow 3
  （复盘 thesis 是否成立），但不给买卖结论

## 自检清单（每次改完 reference 文件后跑一遍）

- [ ] 改动有没有引入"推荐 / 买入 / 必涨"这类越界词？
- [ ] 公司提及有没有保持"示例不是推荐"的语气？
- [ ] markdown 渲染正常（标题层级 / 表格 / 列表）？
- [ ] 没有新引入未验证的公司代码或数据？
- [ ] 没有删掉任何风险提示 / 免责声明？
- [ ] commit message 是中文 + 动词开头？

## 关于这个 CLAUDE.md 本身

如果你（Claude Code）觉得这个文件有该改的地方，**告诉用户**，
不要自己改。这是项目的元规则，改它需要用户明确同意。
