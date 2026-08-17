# 端到端平台实跑记录

> Issue #28 的验收记录。平台实跑是唯一真值来源：本文件可提前准备，但**只有手动把当前 skill 输出的首选表达式贴进本脑「添加字段」并记录显示值后，才能关闭对应 issue**。
>
> 不能以 `docs/eval/results.md` 的历史“薄契约 + FAQ 例子附页”跑批，替代当前 `SKILL.md` 完整文本的新版验收。两者可以作为对照基线，但不是同一次执行。
>
> 运行模型统一记录为 `Auto/unknown`：WISCODE 当前自动选模、不暴露可信 model 元数据，故**不报告模型分组准确率、不使用模型自报身份做归因**，只验证当前 Auto 生产形态下 skill 整体是否达到门槛。

## 执行前条件

- 使用当前 `skills/ognl-painless/SKILL.md` 和 `kb/faq-examples.md` 的内容。
- 每个 case 一条新对话；给 `样例日志`、`意图`、`期望效果` 完整三元组。
- 按路由指令执行：身份不可得（`Auto`）→ 默认读取相关 FAQ KB 再生成。
- 每个 case 只认第一个可粘贴表达式块；第二个表达式仅记备注。
- 平台显示值与期望效果按 `docs/eval/README.md` 判分：13 条类型敏感题进硬门，4 条盲题只记软过线/盲区值错/报错。
- `KB loaded` 只能记 `requested`（按 fallback 请求读取）或 `unknown`（无法确认实际读取），不得伪称 `yes`。

## Auto/unknown 黑盒批次

- **验收门槛**：13 条类型敏感题首选平台实跑判对 ≥ 11/13。
- **路由断言**：运行身份不可得，走「默认读 KB」fallback；`KB loaded` 记 `requested`（无法证实实际读取，故不写 `yes`）。
- **基线（非新版验收）**：历史第二条件跑批三档（地板 11/13、中档 11/13、天花板 13/13）只作离线基线，不作当前 Auto 的模型归因证据。见 `docs/eval/results.md`。
- **结果**：`待执行`。

| case id | runtime model | KB route requested | KB loaded | 首选表达式 | 平台显示值/报错 | 判定 | 备注 |
| ------- | ------------- | ------------------ | --------- | ---------- | -------------- | ---- | ---- |
| faq-02 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L1 硬门 |
| faq-03 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L1 硬门 |
| faq-06 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |
| faq-07 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |
| faq-08 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L3 硬门 |
| faq-09 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |
| faq-10 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |
| faq-11 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L3 重点必测 |
| faq-12 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L3 硬门 |
| qa-02 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |
| qa-03 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |
| qa-04 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L3 硬门 |
| qa-05 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | L2 硬门 |

### 盲题单独记账（不进 13 条硬门）

| case id | 期望值 | runtime model | KB route requested | KB loaded | 首选表达式 | 平台显示值/报错 | 记账（软过线/盲区值错/报错） | 备注 |
| ------- | ------ | ------------- | ------------------ | --------- | ---------- | -------------- | ------------------------------ | ---- |
| faq-01 | 4 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | 标量类型未验 |
| faq-04 | true | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | 标量类型未验 |
| faq-05 | 2000 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | 标量类型未验 |
| qa-01 | 100 | Auto/unknown | yes | requested | 待执行 | 待执行 | 待执行 | 标量类型未验 |

## 收尾规则

- #28：Auto/unknown 黑盒硬门 ≥11/13、盲题单独记账完成后，填写实跑日期/判分人并关闭。
- 任一失败不得修改预注册阈值；记录失败 case 后回到对应 skill 切片修复，再重跑。
- #24/#25 分档验收已废弃：因 WISCODE 模式变更无法选择/观测指定模型，其验收口径不再适用当前 Auto 生产形态，仅保留为离线基线。
