# Wan 2.7 R2V API (`wan2.7-r2v`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0664 per second** on the APIMart route — ein einziger OpenAI-kompatibler Endpoint unter `https://api.apimart.ai/v1`, kein Monatsabo nötig. *(erfasst am 2026-10-12)*

**[API-Key erhalten](https://go.apimart.ai/k-5923cc)** · **[Live-Preise](https://go.apimart.ai/k-caf1d8)** · **[Modellseite](https://go.apimart.ai/k-b9d1ac)** · [⚡ Quickstart in 60 Sekunden](#quickstart)

**Warum Teams Wan 2.7 R2V (`wan2.7-r2v`) über APIMart nutzen**

- **Ein Key, der gesamte Katalog.** Dieselbe Base-URL `https://api.apimart.ai/v1` und derselbe `Authorization`-Header erreichen Wan 2.7 R2V (`wan2.7-r2v`) und über 300 weitere Bild-, Video- und Sprachmodelle — Sie wechseln das `model`-Feld, nicht Ihren Client.
- **$1 Minimum, Pay-as-you-go.** Kein Abo und kein Prepaid-Tarif, den Sie vorab dimensionieren müssen: ab $1 aufladen und in Calls verbrauchen.
- **Die Abrechnung kommt in der Antwort zurück.** Jede abgeschlossene Aufgabe meldet den berechneten Betrag (`cost` / `credits_cost`); eine als `failed` markierte Aufgabe wird erstattet.
- **Asynchron by Design.** Absenden, `task_id` entgegennehmen, `GET /v1/tasks/{id}` pollen — Batching und Retries sind gewöhnliche Queue-Arbeit, keine Sonderintegration.

<!-- /conv-kit:v1 -->



WAN 2.7 R2V API on APIMart is the latest generation AI video model, offering superior visual quality, high motion coherence, and professional-grade performance. Use wan2.7-r2v with a prompt and reference images or videos to control style and characters; up to five image and video references can be combined.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `wan2.7-r2v` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-b9d1ac) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.083 | 0.0664 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-caf1d8) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

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

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-b9d1ac) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"wan2.7-r2v","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "wan2.7-r2v", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
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

**Wie lautet die Modell-id von Wan 2.7 R2V?**

`wan2.7-r2v`. Siehe Routentabelle oben.

**Wie wird abgerechnet?**

**from $0.0664 per second** on the APIMart route. Die abgeschlossene Aufgabe meldet den genauen Betrag im Feld `cost`; eine fehlgeschlagene Aufgabe wird erstattet.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-5923cc) link — $1 minimum top-up, no plan required.

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-b9d1ac> | `apimart.ai/de/model/wan-2-7-r2v` |
| Live pricing | <https://go.apimart.ai/k-caf1d8> | `apimart.ai/de/pricing` |
| Get an API key | <https://go.apimart.ai/k-5923cc> | `apimart.ai/de/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Wan 2.7 R2V is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
