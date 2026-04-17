# AI 优惠数据管理规范

## 目录结构

```
data/
├── tool-source.json          # 主数据文件（结构化优惠数据）
├── DATA-GUIDE.md             # 本规范文档
│
├── raw/                      # 原始采集数据（按平台分类）
│   ├── aliyun-bailian/       # 阿里云百炼
│   │   ├── pricing.txt       # 定价页原始文本
│   │   ├── activity.txt      # 活动页原始文本
│   │   └── screenshots/      # 截图证据
│   │       └── coding-plan.png
│   │
│   ├── baidu-qianfan/        # 百度千帆
│   ├── zhipu-bigmodel/       # 智谱AI
│   ├── tencent-hunyuan/      # 腾讯云混元
│   ├── volcengine-ark/       # 火山方舟
│   ├── deepseek/             # DeepSeek
│   ├── kimi/                 # Kimi（月之暗面）
│   ├── siliconflow/          # 硅基流动
│   ├── minimax/              # MiniMax
│   ├── infini-ai/            # 无问芯穹
│   ├── hpccube/              # 国家超算
│   ├── openai/               # OpenAI
│   ├── anthropic/            # Anthropic
│   ├── google-gemini/        # Google Gemini
│   └── nvidia-nim/           # NVIDIA NIM
│
└── exports/                  # 导出数据（报表、对比等）
    └── deals-report.md
```

## 文件命名规范

### 原始数据文件
- `{平台slug}-{页面类型}.{扩展名}`
- 页面类型：`pricing`（定价）、`activity`（活动）、`home`（首页）
- 示例：`aliyun-bailian-pricing.txt`

### 截图文件
- `{平台slug}-{内容描述}-{日期}.png`
- 示例：`zhipu-coding-plan-20260324.png`

## 数据流程

```
1. 采集原始数据 → data/raw/{平台}/
2. 整理提取优惠 → 更新 tool-source.json
3. 生成报告 → data/exports/
```

## tool-source.json 结构

```json
{
  "version": "YYYY-MM-DD",
  "categories": [...],
  "tools": [
    {
      "slug": "平台标识",
      "name": "平台名称",
      "deals": [
        {
          "title": "优惠标题",
          "dealType": "FREE|LIMITED_FREE|DISCOUNT|TRIAL",
          "discountedPrice": 数字,
          ...
        }
      ]
    }
  ]
}
```

## dealType 类型说明

| 类型 | 说明 | 示例 |
|------|------|------|
| `FREE` | 完全免费 | GLM-4.7-Flash 永久免费 |
| `LIMITED_FREE` | 限时/限额免费 | 新用户免费额度 |
| `DISCOUNT` | 打折优惠 | Coding Plan 订阅 |
| `TRIAL` | 试用体验 | 免费试用 X 天 |

## 什么不算优惠活动

以下属于**定价机制**，不算优惠活动，不记录到 deals：
- ❌ 缓存命中折扣（如 Kimi、智谱的缓存优惠）
- ❌ Batch API 批量折扣
- ❌ 阶梯定价（量大从优）
- ❌ 包年/包月常规折扣

## 什么算优惠活动

以下算优惠活动，应记录到 deals：
- ✅ 新用户免费额度/体验金
- ✅ 限时免费模型
- ✅ Coding Plan 等订阅套餐
- ✅ 抵扣套餐（如阿里云 50 抵 100）
- ✅ 首充优惠、充值赠送
- ✅ 节日活动、限时折扣

## 采集检查清单

每次采集完成后：
- [ ] 原始数据保存到 `data/raw/{平台}/`
- [ ] 截图保存到 `data/raw/{平台}/screenshots/`
- [ ] 更新 `tool-source.json` 中的 deals
- [ ] 验证数据结构（运行 validate 脚本）

## 登录页处理规范

遇到登录页时，不把登录页当作采集结果，也不以“需要登录”作为终点。

正确处理流程：
- 先判定该页面为登录拦截页，不能作为最终证据来源
- 立即切换到未登录可访问的公开页面继续采集
- 优先查找：产品页、活动页、FAQ、购买指南、产品文档、营销页
- 只使用未登录可验证页面中的信息落库到 `tool-source.json`
- 登录后的购买页、控制台页可以作为辅助入口参考，但不作为主要公开证据来源

适用原则：
- ✅ 登录页出现后继续换路径找公开来源
- ✅ 公开产品页 / 文档页 / FAQ 中的活动信息可落库
- ❌ 仅登录后可见的信息，不作为当前轮采集的唯一依据
- ❌ 把登录页本身当成“没有数据”的结论

---

*创建时间：2026-03-25*

## 通用采集探针流程（2026-04-14）

目标：不为单个平台写定制采集逻辑，统一用“发现候选页 → 抽取候选优惠 → 人审 → 落库 → 校验”的流程。

### 1) 采集候选，不落库

```bash
python3 scripts/generic_deal_probe.py collect aliyun-bailian --max-pages 3
```

多平台批量测试：

```bash
python3 scripts/generic_deal_probe.py collect aliyun-bailian tencent-hunyuan baidu-qianfan --max-pages 3
```

输出位置：

- 单平台：`data/raw/evidence/{slug}-probe-YYYYMMDD.json`
- 多平台：`data/raw/evidence/generic-probe-YYYYMMDD.json`

### 2) 审核 JSON

重点看：

- `pages[]`：实际抓取过的页面、原始 HTML 路径、候选链接
- `candidates[]`：原始候选句子、推荐类型、置信度
- `reviewDeals[]`：可落库的候选 deal 模板

`reviewDeals[]` 默认都带：

```json
{
  "isPublished": false,
  "needsHumanReview": true
}
```

确认前不要直接发布。

### 3) 人工修订后落库

可以在 probe JSON 中把确认后的列表整理到 `approvedDeals`，然后执行：

```bash
python3 scripts/generic_deal_probe.py apply data/raw/evidence/{slug}-probe-YYYYMMDD.json --publish
```

说明：

- 如果存在 `approvedDeals`，优先落库 `approvedDeals`
- 否则使用 `reviewDeals`
- `--publish` 会把 `isPublished` 置为 `true`
- 落库后自动运行 `python3 scripts/collect_ai_deals.py validate`

### 4) 当前稳定性结论

已测试平台：

- `aliyun-bailian`：能发现免费 tokens、资源包、定价相关候选
- `tencent-hunyuan`：能发现新客体验、领取入口、定价/活动入口
- `baidu-qianfan`：能发现 tokens 免费领、免费试用、Coding Plan 相关线索
- `siliconflow`：能发现价格页、试用、邀请代金券线索
- `minimax`：能发现定价、订阅、token 套餐线索
- `zhipu-bigmodel`：官网首页信号弱，需要更多候选页发现能力

### 5) 后续优化方向

- 增强二级页发现，尤其是官网首页没有活动信息的平台
- 增加搜索引擎发现：`site:domain 优惠 OR 免费 OR 试用 OR pricing`
- 增加截图证据保存
- 增加报告模式：输出成功、失败、需登录、需人工确认项
- 增加去重合并，避免同一优惠被多句文案拆成多条
