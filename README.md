# spec-driven-kit

> Four chained agent skills that turn a one-line idea into a product doc, verifiable specs, tested code, and an independent review.
> 四个串成一条链的 Agent Skill：把一句话想法，变成能直接开工的文档集，再变成有测试保护和独立评审的代码。

大多数人用 AI 写代码，卡住的地方不是代码，是**动手之前**。

想法含糊就开始写，写完发现方向错了；需求写得很长但没人读，读的人还得再问一遍；代码写完不知道对不对，只能靠"看起来能跑"。

这三个 skill 只解决这一件事：**在写第一行代码之前，把含糊变成决策；在写完代码之后，留下能复核的证据。**

## 链条

```
想法
 │
 ├─ idea-to-spec ──→ docs/product.md + docs/specs/
 │                   拷问决策、写产品文档、切成可验收的 spec
 │
 ├─ spec-mvp ─────→ MVP-NNN
 │                   收成最小可验证版本，带成本上限与判定阈值
 │
 ├─ spec-implement → 代码 + 测试
 │                   每条 AC 一个红绿循环，做完回填状态
 │
 └─ spec-review ──→ 评审报告
                     双轴独立评审 + 证据核对，只出报告不改代码
```

四段可以只用其中一段。`spec-implement` 单独用也成立——只要手上有一份带验收标准的文档；`spec-review` 同理，只要有 spec 和一个可对比的固定点。

## 四个 skill

| Skill | 输入 | 产出 | 什么时候用 |
|---|---|---|---|
| **idea-to-spec** | 一句话想法、模糊需求 | `product.md`、决策日志、spec 切片表 | 项目里还没有 spec，需求只到功能列表 |
| **spec-mvp** | 一份已有的 spec | 带假设、伪造清单、成本上限的 MVP spec | 还没决定值不值得投入完整实现 |
| **spec-implement** | 一份 `ready` 状态的 spec | 代码、测试、回填后的 spec | 要落成代码 |
| **spec-review** | 一份 spec + 一个固定点 | 评审报告（Spec 轴 / Standards 轴 / 证据） | 实现做完，合并或验收前 |

`spec-review` 单独分出来，而不是塞进 `spec-implement` 的收尾，是因为**审查必须换上下文**。同一个 agent 回头检查刚写的代码，检查的是记忆，不是代码。

## 安装

### Claude Code

```
/plugin marketplace add meltlilith233/spec-driven-kit
/plugin install spec-driven-kit@spec-driven-kit
```

### Codex 及其他支持 Agent Skills 的客户端

skill 就是带 `SKILL.md` 的目录，复制到对应位置即可：

```bash
git clone https://github.com/meltlilith233/spec-driven-kit.git
cp -r spec-driven-kit/skills/* <你的 skills 目录>
```

| 客户端 | 目录 |
|---|---|
| Codex | `~/.agents/skills/`（或仓库内 `.agents/skills/`） |
| Claude Code | `~/.claude/skills/` |
| WorkBuddy | `~/.workbuddy/skills/` |
| 其他 | 认 `SKILL.md` 的地方都行 |

只想要其中一个：

```bash
cp -r spec-driven-kit/skills/spec-implement ~/.claude/skills/
```

## 一条链走下来是什么样

以"给设计师用的配色工具"为例：

1. **先分档**。决策都可逆（自己用、不上线）→ 走小档：问 3 个问题，直接写一份 spec，不写 product.md。给一个周末项目跑七节产品文档是浪费，而且用户会从此关掉这个 skill。
2. **写 spec**。4 条验收标准，每条都有断言点和一条能跑的命令。
3. **要不要收 MVP**？4 条 AC、3 个文件，已经是最小形态——跳过。
4. **实现**。每条 AC 先写失败的测试，贴出失败输出，提交一个只含测试的 commit，再写刚好够通过的代码。做完回填三样：映射表、红证据、文档 diff。
5. **审**。换两个独立上下文，一个只看"有没有按 spec 做"，一个只看"符不符合仓库规范"；再自己核一遍证据——映射表里的测试真的存在吗、红 commit 真的成对吗、`MVP-FAKE` 和伪造清单对得上吗。

如果你只想看一段完整推演，`skills/spec-mvp/references/example.md` 里有一份 9 条 AC 砍到 4 条的对照，包括"做错了会是什么样"。

## 设计取舍

这五句话解释了这个套件为什么长得这么"怪"。

**1. 门禁优先于流程。** 每一步都写了出口条件——决策日志确认了才写产品文档，切片表点头了才写 spec。流程谁都会写，难的是判断"什么时候算想清楚了"。

**2. 能留痕的规则才算规则。** 这是被评审逼出来的：一份写着"必须先写测试"的文档，agent 可以逐条遵守、最后诚实地写下 done，而仓库里没有一条测试曾经红过。所以这里的规则都要留下产物——失败输出、红 commit、映射表、`git diff --stat`。

**3. 少而硬，不是多而软。** 四个 `SKILL.md` 一共 320 行，细节都在 `references/` 里按需加载。写满"必须""不许"的清单，对能力强的模型是负优化：它会过度谨慎，在该继续的地方停下来。

**4. 决策是人的，事实是 agent 的。** 仓库里已有的选型，agent 自己去读；不存在的选型，必须问。这条线划错了，agent 会安静地替你做决定。

**5. 审和做要分开。** 做完立刻审，审的是记忆不是代码。所以评审是独立的一步，起独立上下文，而且评审不改代码——一动手改，就会开始为自己改的东西辩护。

## 已知局限

- **v0.2.0，尚未在真实项目上完整跑通。** 设计经过多轮评审，但"能不能被真正执行"这件事，只有实际跑过才知道。欢迎开 issue 说它在哪一步崩了——那是这个项目最需要的信息。
- 文档以中文为主。
- 红证据依赖 git。非 git 仓库、squash 合并、保护分支的情况有降级路径，但证据强度会下降。
- 没有配套的 lint / CI 校验，`_index.md` 和 spec 状态的正确性靠回填时自觉。

## License

MIT
