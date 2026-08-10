# Changelog

本文件记录 `ognl-painless` skill 的版本变更，遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 格式，版本号遵循 [Semantic Versioning](https://semver.org/lang/zh-CN/)。

## [Unreleased]

> `0.1.0` 开发期增量记录。切片落地后在此追加；#26 发布收尾时把 Unreleased 段提升为 `[0.1.0] - <日期>` 并锁定。

### Added

- skill 骨架与版本治理：`SKILL.md` frontmatter（`name` / `description` / `version: 0.1.0`）、部署形态说明、正文结构占位（Issue #9）。
- 输入三元组约束：skill 入口要求 `样例日志` + `意图` + `期望效果`，缺失引导补齐、`期望效果` 须「应新增字段 xxx，值应为 yyy」形式（Issue #12）。
- 薄契约注入：三种语法边界契约（`<% %>` / `{% %}` / `%{ }`）逐字同 `docs/eval/README.md` 跑批注入上下文，含首选制输出形态（Issue #10）。
- FAQ 例子 KB：`kb/faq-examples.md` 按写法分类条目，覆盖大小写转换/三元去空白/split 取位/distinct 去重/域名还原/正则抽邮箱/递归扁平化，与薄契约分离（Issue #11）。
- 按档路由指令：SKILL.md 落「LLM 自读身份，弱档读 KB / 强档直出」指令 + 弱档 model 清单（独立小节、以第二条件跑批结果为准、定期回归），天花板 claude-opus-4-8 直出不读 KB（Issue #13）。

<!--
后续切片落地时在此追加条目，格式示例：

- 三种语法边界薄契约注入（Issue #10）。
- FAQ 例子 KB 提炼与落盘（Issue #11）。
- 输入三元组校验（Issue #12）。
- 按档路由指令 + 弱档 model 清单（Issue #13）。
- L1/L2/L3 回归必测点 + 陷阱补强（Issue #14 / #15 / #16）。
- 诊断式修正循环（Issue #17）。
- 白名单契约侧兜底（Issue #18）。
- 空值/边界防御指引（Issue #19）。
- 收藏落盘 docs/favorites/（Issue #20）。
- L3 重点回归集 / edge 白名单回归集（Issue #21 / #22）。
- 软硬分离盲区记账（Issue #23）。
- 端到端验证：地板档 / 天花板档（Issue #24 / #25）。
-->
