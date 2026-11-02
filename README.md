<p align="center">
  <a href="https://www.piloterr.com"><img src="images/banner.png" alt="Point.P Scraper API by Piloterr – scrape Point.P data as JSON" width="100%"></a>
</p>

# Point.P Scraper API

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/CFj3ph8aHU) [![Get API key](https://img.shields.io/badge/Piloterr-Get%20a%20free%20API%20key-111827)](https://app.piloterr.com) [![API docs](https://img.shields.io/badge/Docs-OpenAPI-0EA5E9)](https://app.piloterr.com/openapi) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

The **Piloterr Point.P Scraper API** lets you extract public data from Point.P as **clean, structured JSON** — no proxies, headless browsers or HTML parsing to maintain. Endpoints available in production:

- **Point.P Product API** — Point.P Product API: scrape live `/p/` product pages from a full URL.
- **Point.P Search API** — Point.P Search API: search by keyword or URL and get building-material listings with prices, brands, ratings, units, and pagination as JSON via browser rendering.

Use it for price monitoring, assortment tracking, competitor analysis, product research or catalog enrichment.

| Endpoint | Path | Credits | What you get |
|---|---|---|---|
| [Point.P Product API](#pointp-product-api) | `/v2/pointp/product` | 2 | Point.P Product API |
| [Point.P Search API](#pointp-search-api) | `/v2/pointp/search` | 2 | Point.P Search API |

## Table of contents

- [How it works](#how-it-works)
- [Point.P Product API](#pointp-product-api)
- [Point.P Search API](#pointp-search-api)
- [Code examples in other languages](#code-examples-in-other-languages)
- [Practical use cases](#practical-use-cases)
- [Why Piloterr?](#why-piloterr)
- [FAQ](#faq)
- [Related scrapers](#related-scrapers)
- [Support](#support)

## How it works

1. **Create a free Piloterr account** and copy your API key from the [dashboard](https://app.piloterr.com).
2. **Send a `GET` (or `POST`) request** to `https://api.piloterr.com/v2/...` with the header `x-api-key: YOUR_API_KEY`.
3. **Receive structured JSON** — Piloterr handles rendering, proxies, retries and parsing.

```bash
export PILOTERR_API_KEY="your-api-key"
pip install requests        # Python
# TypeScript: Node.js 18+ (native fetch), run with `npx tsx file.ts`
```

> Every endpoint accepts both `GET` (query string) and `POST` (JSON body with the same fields).

## Point.P Product API

Point.P Product API: scrape live `/p/` product pages from a full URL. Get title, images, EAN, pricing, specifications, and breadcrumbs as JSON via browser rendering.

`GET | POST https://api.piloterr.com/v2/pointp/product` · **2 credits per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Full Point.P product page URL (`/p/...-A{id}`). |
| `return_page_source` | boolean | — | When `true`, return the raw HTML page source as `{"result": "..."}` instead of parsed JSON. Defaults to `false`. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/pointp/product",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "https://www.pointp.fr/p/platre-isolation-ite/laine-de-verre-a-souffler-thermo-loft-knauf-insulation-sac-16-A3334230",
    },
    timeout=120,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type PointPProduct = {
  ean?: string;
  link?: string;
  name?: string;
  brand?: string;
  media?: string[];
  pricing?: {
    unit?: string;
    price?: number;
    price_currency?: string;
  };
  reference?: string;
  product_id?: string;
  breadcrumbs?: string[];
  description?: string;
  specifications?: {
    name?: string;
    value?: string;
  }[];
};

const params = new URLSearchParams({
  "query": "https://www.pointp.fr/p/platre-isolation-ite/laine-de-verre-a-souffler-thermo-loft-knauf-insulation-sac-16-A3334230",
});

const response = await fetch(`https://api.piloterr.com/v2/pointp/product?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as PointPProduct;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/pointp/product
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "https://www.pointp.fr/p/platre-isolation-ite/laine-de-verre-a-souffler-thermo-loft-knauf-insulation-sac-16-A3334230"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "ean": "5413031003370",
  "link": "https://www.pointp.fr/p/platre-isolation-ite/laine-de-verre-a-souffler-thermo-loft-knauf-insulation-sac-16-A3334230",
  "name": "Laine de verre à souffler Thermo Loft - sac de 16,6 kg",
  "brand": "KNAUF INSULATION",
  "media": [
    "https://www.pointp.fr/asset/40/21/AST18564021-L.jpg",
    "https://www.pointp.fr/asset/40/21/AST18564021-M.jpg"
  ],
  "pricing": {
    "unit": "Sac-sachet",
    "price": 59.9,
    "price_currency": "EUR"
  },
  "reference": "1463175",
  "product_id": "A3334230",
  "breadcrumbs": [
    "Point.P",
    "Plâtre, isolation, plafonds"
  ],
  "description": "La laine de verre à souffler Thermo Loft de Knauf Insulation est conçue spécifiquement pour l'isolation thermique haute performance des c…",
  "specifications": [
    {
      "name": "Matière",
      "value": "Laine de verre"
    },
    {
      "name": "Type de produit",
      "value": "Laine de verre"
    }
  ]
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `ean` | string |  |
| `link` | string |  |
| `name` | string |  |
| `brand` | string |  |
| `media` | string[] |  |
| `pricing` | object |  |
| `pricing.unit` | string |  |
| `pricing.price` | number |  |
| `pricing.price_currency` | string |  |
| `reference` | string |  |
| `product_id` | string |  |
| `breadcrumbs` | string[] |  |
| `description` | string |  |
| `specifications[]` | object[] |  |
| `specifications[].name` | string |  |
| `specifications[].value` | string |  |

## Point.P Search API

Point.P Search API: search by keyword or URL and get building-material listings with prices, brands, ratings, units, and pagination as JSON via browser rendering.

`GET | POST https://api.piloterr.com/v2/pointp/search` · **2 credits per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Search keyword or full Point.P search results URL. |
| `page` | number | — | 1-based page number for pagination. Overrides the page inferred from the URL. Defaults to `1` when omitted. |
| `return_page_source` | boolean | — | When `true`, return the raw HTML page source as `{"result": "..."}` instead of structured JSON. Defaults to `false`. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/pointp/search",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "colle",
    },
    timeout=120,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type PointPSearch = {
  results?: {
    unit?: string;
    brand?: string;
    price?: number;
    title?: string;
    rating?: number;
    currency?: string;
    image_url?: string;
    reference?: string;
    product_id?: string;
    listing_url?: string;
    review_count?: number;
  }[];
  pagination?: {
    next?: string;
    page?: number;
    per_page?: number;
    total_pages?: number;
    total_results?: number;
  };
};

const params = new URLSearchParams({
  "query": "colle",
});

const response = await fetch(`https://api.piloterr.com/v2/pointp/search?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as PointPSearch;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/pointp/search
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "colle"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "results": [
    {
      "unit": "Sac-sachet",
      "brand": "ULTIBAT",
      "price": 37.82,
      "title": "Colle Flex polyvalente - C2S1 ET/G - gris - sac de 25 kg",
      "rating": 5,
      "currency": "EUR",
      "image_url": "https://www.pointp.fr/asset/29/81/AST2272981-M.jpg",
      "reference": "7147449",
      "product_id": "A7147449",
      "listing_url": "https://www.pointp.fr/p/decoration-interieure/mortier-colle-flex-polyvalent-ultibat-c2s1-et-g-25kg-gris-A7147449",
      "review_count": 3
    },
    {
      "unit": "Sac-sachet",
      "brand": "WEBER",
      "price": 41.74,
      "title": "Colle pour carrelage webercol flex enaé - gris - sac de 25 kg",
      "rating": 5,
      "currency": "EUR",
      "image_url": "https://www.pointp.fr/asset/46/89/AST17024689-M.jpg",
      "reference": "3596886",
      "product_id": "A3596886",
      "listing_url": "https://www.pointp.fr/p/decoration-interieure/mortier-colle-deformable-non-irritant-pour-carrelage-weber-col-A3596886",
      "review_count": 1
    }
  ],
  "pagination": {
    "next": "https://www.pointp.fr/search/page-2?q=colle",
    "page": 1,
    "per_page": 24,
    "total_pages": 49,
    "total_results": 1165
  }
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `results[]` | object[] |  |
| `results[].unit` | string |  |
| `results[].brand` | string |  |
| `results[].price` | number |  |
| `results[].title` | string |  |
| `results[].rating` | integer |  |
| `results[].currency` | string |  |
| `results[].image_url` | string |  |
| `results[].reference` | string |  |
| `results[].product_id` | string |  |
| `results[].listing_url` | string |  |
| `results[].review_count` | integer |  |
| `pagination` | object |  |
| `pagination.next` | string |  |
| `pagination.page` | integer |  |
| `pagination.per_page` | integer |  |
| `pagination.total_pages` | integer |  |
| `pagination.total_results` | integer |  |

## Code examples in other languages

Ready-to-run examples live in the [`examples/`](examples) folder. Each one reads your key from the `PILOTERR_API_KEY` environment variable.

| Language | Product | Search |
|---|---|---|
| cURL (Bash) | [`product`](examples/curl/product.sh) | [`search`](examples/curl/search.sh) |
| PHP | [`product`](examples/php/product.php) | [`search`](examples/php/search.php) |
| C# | [`product`](examples/csharp/Product.cs) | [`search`](examples/csharp/Search.cs) |
| Go | [`product`](examples/go/product.go) | [`search`](examples/go/search.go) |
| Java | [`product`](examples/java/ProductExample.java) | [`search`](examples/java/SearchExample.java) |
| Ruby | [`product`](examples/ruby/product.rb) | [`search`](examples/ruby/search.rb) |
| Node.js | [`product`](examples/nodejs/product.mjs) | [`search`](examples/nodejs/search.mjs) |

## Practical use cases

- **Price monitoring** — Track prices, promotions and availability over time to react to competitors.
- **Assortment & catalog tracking** — Follow new products, delisted items and category changes.
- **Competitor analysis** — Benchmark ranges, brands and positioning across retailers.
- **Product research** — Spot best-sellers and trends from search results.
- **Catalog enrichment** — Pull titles, images and attributes into your PIM or marketplace listings.

## Why Piloterr?

- **No infrastructure** — no proxies, headless browsers or CAPTCHA solving to run yourself.
- **Structured JSON** — stable, documented fields instead of brittle HTML selectors.
- **Pay per request** — transparent credit cost per endpoint, failed requests are retried for you.
- **One API key, hundreds of sources** — Point.P plus many other e-commerce, search and company data endpoints in the [Piloterr library](https://www.piloterr.com/library).
- **Any language** — plain HTTPS + JSON, with examples for Python, TypeScript, cURL, PHP, C#, Go, Java, Ruby and Node.js.

## FAQ

### Is it legal to scrape Point.P?

Collecting publicly available data is generally allowed in many jurisdictions, but rules depend on your country, the data you collect and how you use it. Avoid personal data unless you have a lawful basis, respect the website's terms where they apply, and get legal advice for your specific use case. This is not legal advice.

### Do I need proxies or a headless browser?

No. Piloterr handles rendering, proxy rotation and retries on its side. You only send an HTTPS request with your `x-api-key` header and receive JSON.

### How much does a request cost?

Credits per call: `/v2/pointp/product` **2**, `/v2/pointp/search` **2**. See the [Piloterr website](https://www.piloterr.com) for plans.

### What data does the Point.P API return?

Each endpoint returns structured JSON: **Point.P Product** — `ean`, `link`, `name`, `brand`, `media`, `pricing`; **Point.P Search** — `results[]`, `results[].unit`, `results[].brand`, `results[].price`, `results[].title`, `results[].rating`.

### How do I paginate Point.P results?

Use the `page` parameter and keep requesting while the response indicates more results.

### Can I get the raw HTML instead of JSON?

Yes. Set `return_page_source=true` and the response becomes `{"result": "..."}` with the page source.

### Which languages are supported?

Any language that can make an HTTP request. This README shows Python and TypeScript; the [`examples/`](examples) folder contains cURL, PHP, C#, Go, Java, Ruby and Node.js.

## Related scrapers

- [About You Scraper API](https://github.com/piloterr/aboutyou-scraper)
- [Action Scraper API](https://github.com/piloterr/action-scraper)
- [Aldi Scraper API](https://github.com/piloterr/aldi-scraper)
- [Alibaba Scraper API](https://github.com/piloterr/alibaba-scraper)
- [AliExpress Scraper API](https://github.com/piloterr/aliexpress-scraper)
- [Allegro Scraper API](https://github.com/piloterr/allegro-scraper)
- [All Piloterr scrapers on GitHub](https://github.com/piloterr)

## Support

- 💬 Join the [Piloterr Discord](https://discord.gg/CFj3ph8aHU) to ask questions and share what you build.
- 📚 Browse the [API reference (OpenAPI)](https://app.piloterr.com/openapi) and the [Piloterr library](https://www.piloterr.com/library).
- 🔑 [Get your API key](https://app.piloterr.com).

---

<p align="center">
  <a href="https://www.piloterr.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="images/logo-dark.svg">
      <img src="images/logo.svg" alt="Piloterr" height="32">
    </picture>
  </a>
  <br>
  <sub>Built by <a href="https://www.piloterr.com">Piloterr</a> — web scraping APIs for e-commerce, search, real estate and more, one API key.</sub>
</p>

<sub>Point.P is a trademark of its respective owner. This repository is not affiliated with or endorsed by Point.P. Examples are released under the MIT license.</sub>
