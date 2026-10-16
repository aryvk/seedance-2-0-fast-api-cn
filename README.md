# Seedance 2.0 Fast API (`doubao-seedance-2.0-fast`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0213 per second** on the APIMart route (billed by resolution tier) — 一个 OpenAI 兼容端点 `https://api.apimart.ai/v1`，无需包月套餐。*(数据观测于 2026-10-12)*

**[获取 API Key](https://go.apimart.ai/k-aa41fb)** · **[实时价格](https://go.apimart.ai/k-346a0f)** · **[模型页面](https://go.apimart.ai/k-12b675)** · [⚡ 60 秒快速上手](#quickstart)

**为什么团队通过 APIMart 调用 Seedance 2.0 Fast（`doubao-seedance-2.0-fast`）**

- **一个 Key，整个目录。**同一个 `https://api.apimart.ai/v1` base URL 和 `Authorization` 请求头即可访问 Seedance 2.0 Fast（`doubao-seedance-2.0-fast`）以及图像、视频、语言等 300+ 模型——只需切换 `model` 字段，客户端无需改动。
- **$1 起充，按量付费。**没有订阅、也没有需要预先估算用量的预付费套餐：最低充值 $1，调用多少扣多少。
- **费用随响应一并返回。**每个完成的任务都会报告实际计费金额（`cost` / `credits_cost`）；状态为 `failed` 的任务会自动退款。
- **原生异步设计。**提交任务、拿到 `task_id`、轮询 `GET /v1/tasks/{id}`——批处理和重试都是普通的队列工作，不需要定制集成。

<!-- /conv-kit:v1 -->

**中文环境也叫:** 即梦视频

Seedance 2.0 Fast is ByteDance's faster Seedance 2.0 route on APIMart: text-to-video and image-to-video with the same framing controls, billed per generated second.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `doubao-seedance-2.0-fast` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-12b675) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| per generated second | — | see the live pricing page |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-346a0f) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-12b675) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"doubao-seedance-2.0-fast","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "doubao-seedance-2.0-fast", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Seedance 2.0 Fast 的模型 id 是什么？**

`doubao-seedance-2.0-fast`。见上方路由表。

**如何计费？**

**from $0.0213 per second** on the APIMart route (billed by resolution tier)。完成的任务会在 `cost` 字段中给出准确金额；失败的任务会自动退款。

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-aa41fb) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedance-2-0-fast-prompts-cn](https://github.com/aryvk/seedance-2-0-fast-prompts-cn)
- **Same model, other languages** — 9 more language builds: Deutsch [seedance-2-0-fast-api-de](https://github.com/m7sqz596a/seedance-2-0-fast-api-de) · Español [seedance-2-0-fast-api-es](https://github.com/qaukkgecogrt/seedance-2-0-fast-api-es) · Français [seedance-2-0-fast-api-fr](https://github.com/j-az78q90oa/seedance-2-0-fast-api-fr) · Relay/gateway [seedance-2-0-fast-api-gateway](https://github.com/nmx58j60dc8c8/seedance-2-0-fast-api-gateway) · Bahasa [seedance-2-0-fast-api-id](https://github.com/ulazbd7hzzt/seedance-2-0-fast-api-id) · 日本語 [seedance-2-0-fast-api-ja](https://github.com/sxb6d8v/seedance-2-0-fast-api-ja) · 한국어 [seedance-2-0-fast-api-ko](https://github.com/k9fc4/seedance-2-0-fast-api-ko) · Português `seedance-2-0-fast-api-pt` · Русский [seedance-2-0-fast-api-ru](https://github.com/wlpm01/seedance-2-0-fast-api-ru)
- **More models in this language** — [midjourney-api-cn](https://github.com/apimartycl6k972t/midjourney-api-cn) · [gemini-2-5-flash-image-preview-api-cn](https://github.com/hm-5j/gemini-2-5-flash-image-preview-api-cn) · [nano-banana-api-cn](https://github.com/mpflv/nano-banana-api-cn) · [gemini-3-1-flash-image-preview-api-cn](https://github.com/utp00/gemini-3-1-flash-image-preview-api-cn) · [nano-banana-2-api-cn](https://github.com/i3hxnu6/nano-banana-2-api-cn) · [gemini-3-pro-image-preview-api-cn](https://github.com/vd0tv42d/gemini-3-pro-image-preview-api-cn)
- **Topic hubs** — every build of this model, all languages: [seedance-2-0-fast](https://github.com/topics/seedance-2-0-fast) · [seedance-2-0-fast-api](https://github.com/topics/seedance-2-0-fast-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-12b675> | `apimart.ai/zh/model/doubao-seedance-2-0-fast` |
| Live pricing | <https://go.apimart.ai/k-346a0f> | `apimart.ai/zh/pricing` |
| Get an API key | <https://go.apimart.ai/k-aa41fb> | `apimart.ai/zh/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedance 2.0 Fast is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
