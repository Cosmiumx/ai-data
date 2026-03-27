# 平台资产台账

用于统一记录 AI 优惠活动采集涉及的平台信息，方便后续更新、补采和排查。

## 当前平台清单

### 阿里云百炼 (`aliyun-bailian`)
- 官网：`https://www.aliyun.com/product/bailian`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`6`
- 原始数据目录：`data/raw/aliyun-bailian`

### Anthropic (`anthropic`)
- 官网：`https://www.anthropic.com`
- 分类：`model-platform, chat-assistant`
- 当前优惠数：`0`
- 原始数据目录：`data/raw/anthropic`

### 百度千帆 (`baidu-qianfan`)
- 官网：`https://cloud.baidu.com/product/wenxinworkshop`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`2`
- 原始数据目录：`data/raw/baidu-qianfan`

### DeepSeek (`deepseek`)
- 官网：`https://platform.deepseek.com`
- 分类：`model-platform`
- 当前优惠数：`0`
- 原始数据目录：`data/raw/deepseek`

### Google Gemini (`google-gemini`)
- 官网：`https://ai.google.dev`
- 分类：`model-platform, chat-assistant`
- 当前优惠数：`0`
- 原始数据目录：`data/raw/google-gemini`

### 国家超算平台 (`hpccube`)
- 官网：`https://www.hpccube.com`
- 分类：`model-platform, ai-dev-platform, cloud-service`
- 当前优惠数：`2`
- 原始数据目录：`data/raw/hpccube`

### 无问芯穹 (`infini-ai`)
- 官网：`https://cloud.infini-ai.com`
- 分类：`model-platform, ai-dev-platform, cloud-service`
- 当前优惠数：`4`
- 原始数据目录：`data/raw/infini-ai`

### 京东云 (`jdcloud`)
- 官网：`https://www.jdcloud.com`
- 分类：`model-platform, ai-dev-platform, cloud-service`
- 当前优惠数：`2`
- 原始数据目录：`（未建 raw 目录）`

### Kimi (`kimi`)
- 官网：`https://platform.moonshot.cn`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`4`
- 原始数据目录：`data/raw/kimi`

### MiniMax (`minimax`)
- 官网：`https://www.minimaxi.com`
- 分类：`model-platform`
- 当前优惠数：`7`
- 原始数据目录：`data/raw/minimax`

### NVIDIA NIM (`nvidia-nim`)
- 官网：`https://build.nvidia.com`
- 分类：`model-platform`
- 当前优惠数：`0`
- 原始数据目录：`data/raw/nvidia-nim`

### OpenAI (`openai`)
- 官网：`https://openai.com`
- 分类：`model-platform, chat-assistant`
- 当前优惠数：`0`
- 原始数据目录：`data/raw/openai`

### OpenRouter (`openrouter`)
- 官网：`https://openrouter.ai`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`4`
- 原始数据目录：`（未建 raw 目录）`

### 硅基流动 (`siliconflow`)
- 官网：`https://siliconflow.cn`
- 分类：`model-platform`
- 当前优惠数：`5`
- 原始数据目录：`data/raw/siliconflow`

### SophNet (`sophnet`)
- 官网：`https://sophnet.com`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`5`
- 原始数据目录：`（未建 raw 目录）`

### 腾讯云混元 (`tencent-hunyuan`)
- 官网：`https://cloud.tencent.com/product/hunyuan`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`17`
- 原始数据目录：`data/raw/tencent-hunyuan`

### 火山方舟 (`volcengine-ark`)
- 官网：`https://www.volcengine.com/product/ark`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`3`
- 原始数据目录：`data/raw/volcengine-ark`

### 智谱AI (`zhipu-bigmodel`)
- 官网：`https://open.bigmodel.cn`
- 分类：`model-platform, ai-dev-platform`
- 当前优惠数：`4`
- 原始数据目录：`data/raw/zhipu-bigmodel`

## 维护规则

- 新增平台时，先在 `data/tool-source.json` 建立 tool，再创建对应 `data/raw/{slug}/` 目录。
- 原始网页、文本、截图尽量按平台目录集中存放，不要散落在 `data/raw/` 根目录。
- 每个平台至少保留：官网首页/定价页/活动页/截图证据。
- 若平台存在登录墙，优先记录公开活动页、FAQ、购买指南、产品页。
- 更新优惠后，运行 `python3 scripts/collect_ai_deals.py validate` 校验。
- 每次采集后，如有结构调整，同步更新 `data/DEALTYPE-GUIDE.md` 或 `data/DATA-GUIDE.md`。

## 建议补充字段

- 如果你后面想进一步标准化，我建议给每个平台再补这几个字段：
- `pricingUrl`：主要定价页
- `promoUrl`：主要活动页
- `evidencePaths`：关键证据文件路径列表
- `fetchNotes`：抓取注意事项（是否需浏览器/登录/动态渲染）
