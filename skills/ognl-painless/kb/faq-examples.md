# FAQ 例子 KB（ognl-painless）

> 来源：`docs/reference/OGNL常用写法FAQ.md` 提炼，按写法分类。
> **与薄契约分离**：薄契约（SKILL.md「薄契约注入」段）是跨题通用语法边界，本 KB 是按写法分类的例子。本文件**不重写语法边界契约**，只给「写法名 + 原句示例 + 适用语法」的条目。
> 覆盖第二条件跑批中拉过线的高频写法：大小写转换、三元取值去空白、split 取位、`distinct` 去重、域名还原、正则抽邮箱、递归扁平化。
> **路由**：弱档 model 按需读本 KB 例子再生成，强档 model 直出不读（路由指令与弱档清单由 SKILL.md「按档路由」段落地，Issue #13）。

条目结构：每条含 **写法名** + **原句示例** + **适用语法**。原句示例摘自《OGNL常用写法FAQ》原资料写法，仅作写法记忆锚点，不是本题答案表达式——生成时仍需对照用户「样例日志 + 期望效果」逐题改写。

---

## L1 单步写法

### 大小写转换

- **写法名**：字段值大小写统一
- **原句示例**：`<% 字段.toLowerCase() %>` / `<% 字段.toUpperCase() %>`（FAQ 1.8）
- **适用语法**：OGNL `<% %>`

### split 取位

- **写法名**：字段值分割后取单个元素
- **原句示例**：`<% original_log.split(",")[1] %>`（FAQ 1.6）
- **适用语法**：OGNL `<% %>`

### 加减乘除 / 比较

- **写法名**：字段间加减乘除、`==` 比较
- **原句示例**：`<% a 运算符 b 运算符 c %>` / `<% a == b %>`（FAQ 1.21 / 1.14）
- **适用语法**：OGNL `<% %>`

---

## L2 多步/嵌套写法

### A.B 点号替换

- **写法名**：JSON 中 `A.B` / `A.B.C` 点号替换为下划线再映射
- **原句示例**：`<% original_log.replaceAll("\"(.*?)\\.([a-zA-Z0-9_]+)\":", "\"$1_$2\":") %>`（FAQ 1.1 方法一）
- **适用语法**：OGNL `<% %>`

### 三元取值去空白

- **写法名**：条件取值，`XFF1` 存在取 `XFF1` 否则取 `sip`（去两端空白判空）
- **原句示例**：`<% XFF1.trim().equals("") ? sip : XFF1 %>`（FAQ 1.9）
- **适用语法**：OGNL `<% %>`
- **扩展**：多级优先取值 A>B>C —— `<% (A != null && !A.trim().equals("") ? A : (B != null && !B.trim().equals("") ? B : C)) %>`

### 多重数组投影合并

- **写法名**：多重数组中提取同一字段值合并
- **原句示例**：`<% A.{b} %>`（FAQ 1.3）
- **适用语法**：OGNL `<% %>`

### 条件匹配再解析

- **写法名**：字段值匹配多种元素 / 模糊包含
- **原句示例**：`field8 in ['A','B','C']` / `original_log like ' A ' or original_log like ' B '`（FAQ 1.5）
- **适用语法**：OGNL `<% %>`（条件表达式，配合三元）

### 非空字段拼接

- **写法名**：筛选 doc 中非空字段名并以逗号拼接
- **原句示例**：`{% def fields = new String[] {"a","b","c","d"}; Arrays.asList(fields).stream().filter(p -> doc.get(p) != null && doc.get(p).toString().length() > 0).collect(Collectors.joining(",")) %}`（FAQ 1.12）
- **适用语法**：Painless `{% %}`
- **注**：FAQ 标注该写法在 3.5 及以上版本生效。

### `distinct` 去重

- **写法名**：从 URL 数组抽域名后去重
- **原句示例**：`{% 集合?.stream().map(m -> { def url = m.get("url"); def startIndex = url.indexOf("://") + 3; def endIndex = url.indexOf("/", startIndex); if (endIndex == -1) { endIndex = url.length(); } return url.substring(startIndex, endIndex); }).distinct().collect(Collectors.toList()) %}`（FAQ 1.4）
- **适用语法**：Painless `{% %}`
- **要点**：`?.` 安全导航避免空指针；`distinct()` 去重后 `collect(Collectors.toList())` 返回 List，亦可 `collect(Collectors.joining(","))` 返回字符串。

---

## L3 陷阱写法

### 域名还原（DNS 完整域名带括号数字）

- **写法名**：Windows DNS 日志 `(20)optimizationguide-pa(10)googleapis(3)com(0)` 还原为标准域名
- **原句示例**：`<% original_log.replaceAll("^\\(\\d+\\)|\\(\\d+\\)$", "").replaceAll("\\(\\d+\\)", "\\.") %>`（FAQ 1.15）
- **适用语法**：OGNL `<% %>`
- **要点**：先去首尾 `(数字)`，再把中间 `(数字)` 替换为 `.`。两步 `replaceAll` 顺序不能反。

### 正则抽邮箱

- **写法名**：从邮件头/收件人数组中正则抽取标准格式邮箱并合并为数组
- **原句示例**：`{% def result = new ArrayList(); def emailPattern = /[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/; ... def matcher = emailPattern.matcher(addr); if (matcher.find()) { result.add(matcher.group()); } ... return result; %}`（FAQ 1.23）
- **适用语法**：Painless `{% %}`
- **要点**：正则 `/[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/`；遍历数组逐项 `matcher.find()` + `matcher.group()` 收集；可按 `isto` 等条件过滤。

### 递归扁平化（多重数组嵌套 JSON 提取）

- **写法名**：多重数组里还嵌一层 JSON，提取相同字段值合并
- **原句示例**：`<% @com.alibaba.fastjson.JSON@parseObject(log).{product} %>`（FAQ 1.3 补充）
- **适用语法**：OGNL `<% %>`
- **要点**：用 fastjson 静态调用解析嵌套 JSON，再用 OGNL 投影 `.{字段}` 跨多层数组提取；注意白名单——`com.alibaba.fastjson.JSON` 在白名单内（见 SKILL.md 白名单段，#18 落地）。

### base64 判定再解码（条件分支陷阱）

- **写法名**：字段同时可能是 base64 密文与明文，先判定再解码避免明文被解出乱码
- **原句示例**：`<% @com.hansight.dataviewer.utils.Base64@Base64Decode(original_log.matches('^[A-Za-z0-9+/]*={0,2}$') ? original_log : original_log) %>`（FAQ 1.18）
- **适用语法**：OGNL `<% %>`
- **要点**：`com.hansight.dataviewer.utils` 在白名单内；先用正则判定是否 base64，再决定是否解码。

### 科学计数法时间值处理

- **写法名**：科学计数法时间戳映射为标准时间字段
- **原句示例**：`<% timestamp.replaceAll("e\\+\\d+", "").replaceAll("\\.", "").replaceAll("(\\d{10}).*", "$1000") %>`（FAQ 1.16）
- **适用语法**：OGNL `<% %>`

---

## 维护说明

- 本 KB 是弱档 model 的「例子记忆」，**不是答案库**：生成时必须对照用户当前「样例日志 + 期望效果」逐题改写，不能直接套用原句示例的字段名。
- 新写法上线 / 旧写法淘汰时，在此追加/删除条目并更新本文件 header 的「覆盖写法」清单；同步在 SKILL.md CHANGELOG 记一笔。
- 与薄契约的边界：本文件只写「怎么写某类写法」的例子，不重复「三种语法边界 + 首选制」这类跨题契约。
