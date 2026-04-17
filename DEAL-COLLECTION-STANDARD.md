# 优惠活动采集通用标准流程

## 目标

建立跨平台统一的优惠活动采集流程，避免依赖某个平台的临时脚本、手工笔记或本地特殊化文档。

核心原则：
- 以**本轮真实抓取页面**作为主证据
- 以**统一 JSON 结构**输出结果
- 先展示 JSON 给用户确认，再决定是否落库
- 本地历史文件只能作为辅助证据，不能替代本轮抓取

---

## 标准流程

### 1. 发现入口
每个平台至少寻找以下页面中的一种或多种：
- 产品页
- 活动页
- 定价页
- FAQ / 帮助文档页
- 购买页 / 套餐页

建议优先级：
1. 活动页
2. 套餐购买页
3. 定价页
4. FAQ / 文档页
5. 产品页

### 2. 本轮真实抓取
优先顺序：
1. 浏览器 DOM 抓取
2. HTTP 抓取页面 HTML + 提取内嵌 JSON / SSR 文本
3. 截图 / OCR（仅在必要时辅助）

要求：
- 至少拿到一个可追溯的本轮页面证据
- 记录本轮抓取使用的 URL
- 若页面依赖登录，明确标记 `login_required`
- 若只能看到壳页，明确标记 `page_shell_only`

### 3. 标准化抽取
每条优惠活动统一抽取为以下结构：

```json
{
  "title": "",
  "dealType": "DISCOUNT",
  "dealUrl": "",
  "originalPrice": null,
  "discountedPrice": null,
  "currency": "CNY",
  "discountText": "",
  "limits": {},
  "supportedModels": [],
  "supportedTools": [],
  "rules": [],
  "status": "ACTIVE",
  "evidence": [],
  "collectedAt": "YYYY-MM-DD"
}
```

### 4. 状态分级
每次抓取结果必须标记分级：

- `confirmed`
  - 本轮真实抓到
  - 价格/权益/规则至少有一项明确
  - `dealUrl` 明确
  - 可直接候选落库

- `partial`
  - 本轮确认活动存在
  - 但价格、规则、状态、套餐细节不完整
  - 不能直接正式落库

- `blocked`
  - 登录后可见
  - 页面抓取失败
  - 浏览器链路异常
  - 需要人工继续处理

### 5. 回复与确认
每次采集完成后，必须先输出：
1. 简短摘要
2. 标准 JSON
3. 明确说明哪些是 `confirmed` / `partial` / `blocked`

未经确认，不直接落库。

### 6. 落库规则
只有满足以下条件的记录才允许正式落库：
- 有本轮真实抓取页面证据
- `dealUrl` 明确
- 至少一项核心优惠信息明确：价格 / 免费额度 / 限时折扣 / 套餐用量
- 字段能映射到当前 `tool-source.json` schema

不满足条件时：
- 仅输出 JSON
- 标记为 `partial` 或 `blocked`
- 不写入主数据文件

---

## 证据使用规则

### 主证据
优先使用：
- 本轮浏览器 DOM 文本
- 本轮 HTTP 返回 HTML
- 本轮页面内嵌 JSON / SSR 数据

### 辅助证据
可使用但不能单独作为正式落库依据：
- 历史截图
- 历史 txt 摘录
- 历史 HTML 文件
- 旧会话结论

历史证据仅用于：
- 辅助定位页面
- 交叉验证字段
- 帮助判断是否需要继续抓

---

## 通用输出模板

```json
{
  "platform": "platform-slug",
  "status": "confirmed|partial|blocked",
  "sourceUrls": [],
  "deals": [
    {
      "title": "",
      "dealType": "DISCOUNT|LIMITED_FREE|SUBSCRIPTION",
      "dealUrl": "",
      "originalPrice": null,
      "discountedPrice": null,
      "currency": "CNY",
      "discountText": "",
      "limits": {},
      "supportedModels": [],
      "supportedTools": [],
      "rules": [],
      "status": "ACTIVE|SOLD_OUT|UNKNOWN",
      "evidence": [],
      "collectedAt": "YYYY-MM-DD"
    }
  ],
  "blockedReason": null
}
```

---

## 执行注意事项

- 不要把“历史已有数据”直接当作“本轮已确认数据”
- 不要先落库再补证据
- 不要因为某个平台以前抓过，就跳过本轮验证
- 不要依赖某个平台私有脚本作为唯一主流程
- 同一平台的专用脚本只能作为加速器，不能替代通用流程判断

---

## 当前工作约束

后续所有平台采集，默认按本文件执行。
如果某个平台需要特殊处理，必须在结果里明确说明“偏离标准流程的原因”。
