# adhd-dog-tutor-skill

一个给 Claude 用的 Agent Skill:把试卷、习题、课件案例和论文变成"被人带着走一遍"的过程,而不是被讲解一遍。

写给读不进材料、只能从做中学的人——尤其是 ADHD。

---

## 它解决什么问题

ADHD让 AI 当家教的通常结果是:问一个问题，ai产出铺天盖地的一段，读了之后脑子0个印象。因为你全程在**接收**,没有**产出**。而ai在讲课，而非引导。

这个 skill 把辅导反过来:每一轮只问你一个问题,然后停下来等你答。理论不是一开始点出来的，不需要你读懂再应用。狗会从观察环境中归纳规律，现在你就是狗，你只负责把自己的注意力导引到一个特定的点上，规律从孤立的点的归纳中生发出来。

## 设计上的几个决定

**1. 每轮回复必须以且仅以一个问题结尾,问题单独成行。**

苏格拉底式辅导最容易崩的方式是:模型问完"你觉得这里为什么?",然后在下一句自己把答案讲了。光写"不要自问自答"挡不住——对话一长,模型模仿最近几轮的倾向会压过遵守远处的指令。所以用格式来挡:如果最近每一轮都以一个孤立问句收尾,那个结构本身就成了它要模仿的东西,想多讲就得先打破一个每轮都看得见的形状。这比违反一条几十轮之前的文字规则难得多。

**2. 遮住结论,先猜后验证。**

做题天然适合这种辅导,因为答案本来就藏着。课件和论文不行——结论就印在页面上,扫一眼就"懂了"。所以要人为制造那个空缺:看作者的 method 之前,先问"如果是你要证明这件事,你会去测什么";看数字之前,先猜方向和量级。猜错是最好的情况,差异出现的地方就是真正学到东西的地方。

**3. 提示阶梯有底,第四级就直接给答案。**

卡住时按四级往下降:问得更小 → 指方向 → 填空 → 直接讲。到底了就讲,不硬撑。对 ADHD 来说,卡太久的挫败感会直接终止整个学习过程,比"没有独立想出来"糟得多。

**4. 只报进度,不评判。**

不写"进度有点慢""得加快了"。羞耻感对 ADHD 是关掉整个 session 的开关。

**5. 收尾时生成的笔记,不许写成教科书腔。**

session 结束时会问你要不要把走过的内容整理成 md 文件。写的时候保留你当时说出来的原话和比喻，让留下来的钩子都能重新导引一遍推理。改写成规整的知识点罗列等于白写,因为读不进课本正是你需要这个 skill 的原因。

## 支持的材料

| 类型 | 遮住的是 | 终点 |
|---|---|---|
| 试卷、习题、作业、代码 | 答案 | 答案 + 用到的 method |
| 课件、讲义、slides 里的案例 | 作者想让你得出的结论 | 这个案例支撑的是哪个 framework |
| 论文 | 方法和结果 | 一句话说出这篇论文成立的条件 |

## 安装

**Claude Code(推荐)**

```bash
/plugin marketplace add YOUR-NAME/adhd-dog-tutor-skill
/plugin install adhd-dog-tutor-skill@wooweeiao-skills
```

装好后直接 `/adhd-dog-tutor-skill` 调用,或者上传材料让它自动触发。之后更新由 Claude Code 自己跟进。

**Claude Code(手动)**

不想走 plugin 就直接拷 skill 目录:

```bash
git clone https://github.com/YOUR-NAME/adhd-dog-tutor-skill.git
cp -r adhd-dog-tutor-skill/skills/adhd-dog-tutor-skill ~/.claude/skills/        # 个人
# 或 cp -r adhd-dog-tutor-skill/skills/adhd-dog-tutor-skill .claude/skills/     # 只在当前项目
```

**Claude.ai / Claude 桌面端**

需要 Pro、Max、Team 或 Enterprise 方案,并开启 code execution。下载本仓库的 `adhd-dog-tutor-skill.skill`,然后 Settings → Features → 上传。上传后在该账号的所有对话里都可用。

**其他 agent**

skill 本体是 `skills/adhd-dog-tutor-skill/SKILL.md`,符合 [Agent Skills 开放标准](https://agentskills.io)。Cursor、Codex(`~/.codex/skills/`)、VS Code + Copilot(`.github/skills/` 或 `.claude/skills/`)都识别同样的目录结构,把 `skills/adhd-dog-tutor-skill/` 这个目录复制过去即可。

## 怎么用

装好之后,把材料丢进对话,说一句你想干什么:

- "带我做一遍这份卷子,别直接给答案"
- "这份 slides 里的案例我看不懂,带我走一遍"
- "帮我把这篇 paper 的核心结论搞清楚,我想自己想明白"

它会先清点材料、问你从哪开始,然后一轮一个问题往下走。

中途任何时候可以说"直接告诉我吧",它会给,给完继续往下走,不会因此改变整体节奏。

## 自定义

- **语言**:默认中文讲解、术语保留英文。要换成别的语言,改开头那一段。
- **学科**:论文那部分默认是实证类(有 claim、有 method、有数据)。纯理论/数学的 paper 走"题目类"的推理链更顺,skill 会自己判断,如果不准可以在第 3 节写死。
- **触发范围**:`description` 决定它什么时候被自动加载。想让它更容易/更难触发,改那一行。

## 不适合什么

- 你只想核对答案
- 你赶时间,要的是结果不是过程
- 你想要一份摘要

这几种情况下它会拖慢你。直接问,别开这个 skill。

## License

MIT。随便改、随便分发。如果你改出了更好的版本,欢迎开 issue 说一声。
