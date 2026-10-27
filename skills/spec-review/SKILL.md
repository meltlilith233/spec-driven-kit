---
name: spec-review
description: 对照 spec 审一遍已完成的实现：双轴独立评审加证据核对，只出报告不改代码。当一份 spec 已实现完、要验收或合并前检查时使用。
---

# Spec Review

实现做完之后，换一个上下文审它——不是同一个 agent 回头检查自己刚写的东西。

审三样，互相不遮掩：

- **Spec 轴**：这份 diff 是否忠实实现了每一条 AC
- **Standards 轴**：是否符合本仓库的规范
- **证据**：TDD 到底发生了没有

## 为什么分成三块

一个改动可以完全符合规范但把功能实现错了，也可以忠实实现但把仓库规范踩了。揉成一个"总体评价"，两边的问题会互相抵消——干净整洁的错代码，和丑陋的正确代码，都不该拿高分。

证据单独一块，是因为它问的是另一个问题：不是"代码对不对"，是"你说的过程发生过没有"。前两个轴都是判断，这一轴是核对。

三块分开报，**不合并、不跨轴排名**。

## 固定点

对着 merge-base 比：

```
git diff <fixed-point>...HEAD
git log <fixed-point>..HEAD --oneline
```

固定点怎么定，按顺序：

1. commit message 里有 `SPEC-003` 的，取最早那个的 parent
2. 用户指定的
3. 都没有就问，别猜

先确认 `git rev-parse <fixed>` 能解析、diff 非空。坏 ref 或空 diff 在这一步失败，不要带进两个 sub-agent 里——那样你只会得到两份"没找到改动"的报告。

## 起两个 sub-agent

并行，前台，各自独立上下文。两份 brief 都要带上 diff 命令和 commit 列表。

细则、baseline、brief 模板见 [references/review-axes.md](references/review-axes.md)，起 sub-agent 之前把对应段落整段贴进去——它们没有别的渠道拿到这些内容。

两条共同约束：单份报告 400 字以内；工具已经强制的东西不要报。

**没有 sub-agent 的环境**：分两次做，一次只做一个轴，写完第一份再看第二个轴。中间别合并结论——两个轴分开的全部意义，是让它们互相不能解释掉对方的问题。

## 证据核对

这一轴不用 sub-agent。它该由命令回答，不该由判断回答。在本体跑：

```bash
git log --oneline <fixed>..HEAD        # 有没有 test 红 → feat 绿 的成对提交
grep -rn "MVP-FAKE" src/               # 和 MVP spec 的伪造清单对账
git diff --stat docs/specs/            # 回填到底有没有发生
```

再把 spec 里的映射表逐条拿出来核：`AC-2 → api/tests/inbox.spec.ts::creates_item_at_head`，这个文件在不在、这个用例名在不在、跑一次绿不绿。

这一轴只有两档：**对得上 / 对不上**。

对不上就是没做 TDD，不管实现报告里写了什么。这是整套流程里唯一一处不看叙述、只看物证的地方。

## 汇总

三块并列，原样呈现，不要揉成一段总结。

结尾一行：每块各几条问题，以及**每一块内部**最严重的那条。跨轴不排名——那正是分轴的用意。

## 审查不修

只出报告，不改代码。

理由不是分工，是上下文：审的人一旦动手改，就会开始为自己改的东西辩护，后面的判断就不独立了。

## 结论怎么用

| 情况 | 动作 |
|---|---|
| Spec 轴有缺失/错误 | 回 `spec-implement` 修，状态留 `in-progress` |
| 证据对不上 | 同上，并补上缺失的红证据 |
| 只有 Standards 轴有问题 | 可以合并，问题记进 spec 的风险节 |
| 三块都过 | 有判定阈值的改 `awaiting-verdict`，否则 `done` |

报告骨架见 [assets/review-report.md](assets/review-report.md)。
