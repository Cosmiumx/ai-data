# dealType 使用规范

用于 `data/tool-source.json` 中 `tools[].deals[].dealType` 字段。

## 合法值

```json
[
  "LIMITED_FREE",
  "DISCOUNT",
  "SUBSCRIPTION",
  "PAYG",
  "COUPON"
]
```

## 定义

### `LIMITED_FREE`
适用于：
- 新用户免费额度
- 限时免费模型
- 免费资源包
- 赠送调用次数 / token / 积分
- 免费试用 / 试用期

示例：
- 新用户赠送 100 万 token
- 开通后赠送 50 次体验额度
- 某模型限时免费
- 7 天免费试用

说明：
- 旧值 `FREE` / `FREE_TRIAL` 已统一并入 `LIMITED_FREE`

---

### `DISCOUNT`
适用于：
- 直降
- 折扣
- 抵扣套餐
- 低价促销
- 邀请活动折算成优惠

示例：
- 50 元抵 100 元
- 4 折优惠
- 限时特价
- 邀请返利

说明：
- 旧值 `LOW_PRICE` / `TIER_BENEFIT` / `REFERRAL` 已统一并入 `DISCOUNT`

---

### `SUBSCRIPTION`
适用于：
- 月付 / 年付订阅
- Coding Plan / Pro / Max 套餐
- 会员型开发套餐

示例：
- Coding Plan Lite 39 元/月
- Pro 套餐 99 元/月

说明：
- 只要核心售卖方式是“订阅套餐”，优先用这个，而不是 `DISCOUNT`
- 即使没有原价对比，也可以是 `SUBSCRIPTION`

---

### `PAYG`
适用于：
- 按量付费
- 后付费
- 按 token / 次 / 分钟计费

示例：
- 0.2 元 / 张
- 45 元 / 分钟
- 按 token 后付费

说明：
- `PAYG` 记录的是计费机制，不一定代表“优惠”
- 仅当你希望把“当前可选的低门槛付费方式”也纳入 deals 时使用

---

### `COUPON`
适用于：
- 优惠码
- 兑换码
- 券码抵扣

示例：
- 输入优惠码立减
- 新用户券包

## 不再使用的旧值

以下值不要再写入：

```json
[
  "FREE",
  "FREE_TRIAL",
  "LOW_PRICE",
  "TIER_BENEFIT",
  "REFERRAL",
  "FEATURE"
]
```

## 映射规则

```json
{
  "FREE": "LIMITED_FREE",
  "FREE_TRIAL": "LIMITED_FREE",
  "LOW_PRICE": "DISCOUNT",
  "TIER_BENEFIT": "DISCOUNT",
  "REFERRAL": "DISCOUNT",
  "FEATURE": null
}
```

说明：
- `FEATURE` 不是优惠，应写入 `features`，不应写入 `deals`

## 选择优先级

当一条信息看起来可归到多个类型时，按这个顺序判断：

1. 是不是订阅套餐？→ `SUBSCRIPTION`
2. 是不是送免费额度/次数/试用期？→ `LIMITED_FREE`
3. 是不是优惠码？→ `COUPON`
4. 是不是按量/后付费？→ `PAYG`
5. 其余价格优惠/折扣/返利 → `DISCOUNT`
