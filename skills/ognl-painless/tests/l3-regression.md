# L3 重点回归集

> Issue #21。**skill 改动后必跑**。
> 来源与输入 SSOT：`docs/eval/faq-cases.md`、`docs/eval/qa-cases.md`。
> 运行与判分 SSOT：`docs/eval/README.md`「跑批流程」「多表达式判分口径（首选制）」「判分标准」。
> 与 `kb/faq-examples.md` 的关系：KB 维护写法示例与陷阱解释；本文件只固化回归用例与已验证的跑通首选表达式，避免维护两份写法主内容。

## 运行规则

1. 每个 case 新开对话，先按 SKILL.md 的输入三元组提供 `样例日志`、`意图`、`期望效果`。
2. 路由后由模型输出 1–2 个表达式；只把按出现顺序第 1 个表达式块贴现场本脑「添加字段」实跑。
3. 记录平台显示值、判定和备注。第二个表达式只作观察，不改变判定。
4. List / 集合题按平台原样输出做双向集合比对：漏深层、含重复、多元素均判错。

## 必测用例

### faq-08：DNS 域名还原

- **case id**：`faq-08`
- **样例日志**：`{"dns_name":"(4)mail(6)yuntu(3)com(0)"}`
- **意图**：dns_name 是「(长度)标签」拼接的完整域名格式，去掉首尾的 `(数字)`，把中间的 `(数字)` 替换成点，还原为正常域名，新增字段 domain。
- **期望效果**：应新增字段 domain，值应为 mail.yuntu.com。
- **跑通首选表达式**：`<% dns_name.replaceAll("^\\(\\d+\\)|\\(\\d+\\)$", "").replaceAll("\\(\\d+\\)", ".") %>`
- **适用语法**：OGNL `<% %>`
- **回归断言**：先去首尾 `(数字)`，再替换中间 `(数字)`；两步顺序不可颠倒。

### faq-11：域名提取与去重（重点必测）

- **case id**：`faq-11`
- **样例日志**：`{"links":[{"url":"http://a.yuntu.com/x"},{"url":"http://b.xinghai.com/y"},{"url":"http://a.yuntu.com/z"}]}`
- **意图**：links 数组每项的 url，提取协议后到第一个 `/` 之间的域名部分，去重后合并成列表，新增字段 domains。
- **期望效果**：应新增字段 domains，值应为集合 {a.yuntu.com, b.xinghai.com}（顺序不敏感，去重后 2 个元素）。
- **跑通首选表达式**：`{% doc['links']?.stream().map(m -> { def url = m.get("url"); def s = url.indexOf("://") + 3; def e = url.indexOf("/", s); if (e == -1) {e = url.length();} return url.substring(s, e); }).distinct().collect(Collectors.toList()) %}`
- **适用语法**：Painless `{% %}`
- **回归断言**：**必须 `distinct()`，平台原样输出必须恰有 2 个元素**。返回 `["a.yuntu.com","b.xinghai.com","a.yuntu.com"]` 即判错。
- **风险说明**：这是第二条件下地板/中档仍最易错的 L3 点；每次 skill 改动后必须实跑。

### faq-12：正则抽邮箱

- **case id**：`faq-12`
- **样例日志**：`{"recipients":[{"isto":false,"addr":"Alice alice@yuntu.com"},{"isto":true,"addr":"Bob bob@xinghai.com"}]}`
- **意图**：从 recipients 中筛出 isto 为 true 的项，用正则从 addr 里提取标准邮箱地址，合并成列表，新增字段 to_emails。
- **期望效果**：应新增字段 to_emails，值应为集合 {bob@xinghai.com}（1 个元素）。
- **跑通首选表达式**：`{% def result = new ArrayList(); def emailPattern = /[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/; for (def item : doc.recipients) { if (item != null && item.get('isto') == true) { def addr = item.get('addr'); if (addr != null) { def matcher = emailPattern.matcher(addr); if (matcher.find()) { result.add(matcher.group()); } } } } return result; %}`
- **适用语法**：Painless `{% %}`
- **回归断言**：返回抽出的邮箱而非原始 `addr` 字符串；只包含 `isto == true` 的项。

### qa-01：Map 按类型筛最小等级（类型盲题，待补平台首选）

- **case id**：`qa-01`
- **样例日志**：`{"scores":{"a":"1:100","b":"1:101","c":"2:102"}}`
- **意图**：scores 是 Map，值形如「类型:等级」，筛出类型为 1 的项，取等级数字最小的，找不到则返回 103，新增字段 level_type1。
- **期望效果**：应新增字段 level_type1，值应为 100。
- **跑通首选表达式**：`待现场实跑补录`。历史结果的首选为 Painless `compile error`，不能作为通过基线。
- **适用语法**：Painless `{% %}`
- **回归断言**：平台显示值必须为 `100`；这是标量类型盲题，只判值，记录「类型未验」。
- **待补原因**：现有 `docs/eval/results.md` 没有该 case 的跑通首选表达式。下次平台实跑得到可用首选、显示值与判定后，必须回填此项；在此之前，不能把 L3 回归集标为全绿。

### qa-04：不规整嵌套递归提取 image_url.url

- **case id**：`qa-04`
- **意图**：msg 是多层嵌套结构，content 里可能再嵌套 content，递归提取所有 image_url.url，合并成列表，新增字段 urls。
- **期望效果**：应新增字段 urls，值应为集合 {u1, u2}（2 个元素，顺序不敏感）。
- **跑通首选表达式**：`{% void r(def e, List u) { if (e instanceof Map) { if (e.image_url?.url != null) u.add(e.image_url.url); if (e.content != null) r(e.content, u); } else if (e instanceof List) { for (x in e) r(x, u); } } List u = []; for (o in doc.msg) r(o.content, u); return u; %}`
- **适用语法**：Painless `{% %}`
- **回归断言**：平台 List 必须包含 `u1` 与 `u2`；只抽浅层、漏 `u2` 判错。

## 维护边界

- 本回归集记录“平台已验证”的首选表达式和不可替代的断言，不扩写 FAQ 主条目。
- `qa-01` 当前没有已验证的跑通首选，明确保留为待补点，不能被误判为通过。
- 平台能力变化或 skill 变更后，更新本文件的首选表达式、验证日期和实跑备注，并同步 `CHANGELOG.md`。
