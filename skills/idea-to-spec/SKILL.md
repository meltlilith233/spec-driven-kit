---
name: idea-to-spec
description: 把产品想法变成可执行文档：拷问出决策、写 product.md、切成带验收标准的 spec。当项目里还没有 spec 文件，或需求只到功能列表、没有验收标准时使用。
---

# Idea to Spec

把一个想法，变成能直接开工的文档集。

完成的标准：换一个对这个项目一无所知的执行者，只读这些文件，能不提问就把活干对。

## 先定档

| 体量 | 判据 | 跑什么 |
|---|---|---|
| 小 | 决策都可逆：单点工具、一个页面、自己用 | 问 3 个问题，直接写一份 spec |
| 中 | 有不可逆决策（数据模型、计费、权限），或有第二个人要读这些文档 | 全流程，product.md 一页 |
| 大 | 多角色、有商业目标、要对外上线 | 全流程，product.md 两页 |

分档看**决策的可逆性**，不看有没有提到"用户"。"给设计师用的配色工具"仍然是可逆的周末项目，走小档。

小档要顺手裁掉模板里用不上的部分：非功能约束、待定问题、反画像，能省则省。

给一个两小时的脚本跑完整流程，代价远超收益，而且用户会从此关掉这个 skill。

## 三步

**1. 拷问。** 一轮问完当前能问的（3-6 个），每个带上推荐答案和理由，措辞让"是"就代表接受。问完停下等回答，不要自问自答往下推。

目标不是了解需求，是把想法逼成一串有理由的决策。出口是一份决策日志——决定了什么、为什么、谁定的、可逆性。用户确认才往下走。

仓库里已有的选型、配置、现有流程是**事实**，自己去读；仓库里不存在的选型是**决策**，必须问，给选项不给结论。

做法见 [references/grilling.md](references/grilling.md)。

**2. product.md。** 按 Scrum 的 Product Goal 写，不是 PRD：定位、愿景（含 kill criteria）、画像（含反画像）、目标与非目标、成功指标、约束与假设、最大风险、首个可用增量。两页以内。

出口是逐条能打勾，不是感觉对了。超过两页，写的就是 spec。

模板见 [assets/product.md](assets/product.md)，逐节写法与正反例见 [references/product-doc.md](references/product-doc.md)。

**3. Spec。** 先切图给用户确认，再逐片写透。只详写最近两三片，其余在 `_index` 里留一行。

第一片是**能证伪最大风险的最薄通路**。这两者常常不是同一片——最薄通路通常是 CRUD，风险常在效果或数据上。不同时先插一个 spike 验风险，再补骨架。

粒度用事前可判的指标：≤5 文件 / ≤400 行 / ≤8 条 AC / 0 新依赖。超了先试着拆，拆不动的写明理由。

切完立刻落盘。详见 [references/spec-writing.md](references/spec-writing.md)，模板见 [assets/spec.md](assets/spec.md) 与 [assets/_index.md](assets/_index.md)。

## 有存量代码时

切片方式不一样，先测绘再动手。见 [references/brownfield.md](references/brownfield.md)。

## 边界

自己可以做：建 `docs/`、写文件、更新 `_index.md`、按体量裁掉不必要的步骤。

必须先问：改愿景或非目标、放宽非目标、砍掉某片、跳过切图直接写。

用户说"别问了""先做个原型"，就降级成一份 spec，并在文件头写 `拷问：未完成（用户要求跳过）`。降级要留痕，不是禁止。

## 产出

```
docs/
├── product.md
├── decisions.md      # 决策日志，grilling 每轮往里追加
└── specs/
    ├── _index.md
    └── NNN-<slug>.md
```

仓库已有文档约定就跟着走，别另起一套。

状态：`draft → ready → in-progress → done`；带判定阈值的走 `awaiting-verdict`（见 `spec-mvp`）；被取代的标 `superseded`，不删不改。

## 之后

先验证值不值得做 → `spec-mvp`。直接开工 → `spec-implement`。MVP 的证据到期后，回到 `spec-mvp` 做判定。

收尾给一段不超过 10 行的总结，必须包含**最大的未验证假设**。
