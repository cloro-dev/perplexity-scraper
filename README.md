# Perplexity Scraper

[![Perplexity scraper by cloro](https://github.com/cloro-dev/perplexity-scraper/blob/main/perplexity-scraper-hero-image.png)](https://cloro.dev/perplexity/?utm_source=github)

[![cloro](https://img.shields.io/badge/Powered%20by-cloro-blue?style=for-the-badge)](https://cloro.dev/)

The [Perplexity Scraper](https://cloro.dev/perplexity/) by cloro enables developers to programmatically interact with Perplexity AI and automatically collect AI-powered search responses along with structured metadata. Instead of manual data collection, you can retrieve results as parsed JSON, raw HTML, or other formats for seamless integration into your workflows.

You can use cloro's Perplexity Scraper for current events monitoring, news tracking, research automation, and competitive analysis. It handles dynamic AI-generated content, supports real-time extraction, and eliminates the need to manage authentication, sessions, or anti-bot systems.

## How it works

The Perplexity scraper handles the rendering, parsing, and delivery of results in your requested format. You provide your search query, API credentials, and optional parameters as shown below.

### Request sample (Python)

```python
import json
import requests

# API parameters
payload = {
    'prompt': 'What are the latest developments in quantum computing 2025?',
    'country': 'US',
    'include': {
        'markdown': True
    }
}

# Get a response
response = requests.post(
    'https://api.cloro.dev/v1/monitor/perplexity',
    headers={'Authorization': 'Bearer YOUR_API_KEY'},
    json=payload
)

# Print response to stdout
print(response.json())

# Save response to a JSON file
with open('response.json', 'w') as file:
    json.dump(response.json(), file, indent=2)
```

### Request sample (cURL)

```bash
curl -X POST https://api.cloro.dev/v1/monitor/perplexity \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "What are the latest developments in quantum computing 2025?",
    "country": "US",
    "include": {
      "markdown": true
    }
  }'
```

### Request sample (Node.js)

```javascript
const axios = require("axios");

const payload = {
  prompt: "What are the latest developments in quantum computing 2025?",
  country: "US",
  include: {
    markdown: true,
  },
};

axios
  .post("https://api.cloro.dev/v1/monitor/perplexity", payload, {
    headers: {
      Authorization: "Bearer YOUR_API_KEY",
      "Content-Type": "application/json",
    },
  })
  .then((response) => {
    console.log(response.data);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

### Request parameters

| Parameter             | Description                                                                 | Default value |
| --------------------- | --------------------------------------------------------------------------- | ------------- |
| `prompt`\*            | The search query or question to ask Perplexity (1-10,000 characters)        | –             |
| `country`             | Optional country/region code for localized results (e.g., `US`, `GB`, `DE`) | `US`          |
| `include.markdown`    | Include response in Markdown format when set to true                        | `false`       |
| `include.html`        | Include URL to full HTML response when set to true (URL expires after 24h)  | `false`       |
| `include.rawResponse` | Include raw streaming response events for debugging                         | `false`       |

\* Mandatory parameters

---

### Output samples

The Perplexity Scraper API returns a structured JSON object containing Perplexity's AI-generated response and metadata.

**Structured JSON output snippet:**

```json
{
  "success": true,
  "result": {
    "text": "Recent developments in quantum computing include breakthrough error correction methods, increased qubit stability, and practical applications in cryptography...",
    "sources": [
      {
        "position": 1,
        "url": "https://example.com/quantum-breakthrough",
        "label": "MIT Technology Review",
        "description": "Scientists achieve 99.9% qubit fidelity in room temperature conditions..."
      },
      {
        "position": 2,
        "url": "https://example.com/quantum-applications",
        "label": "Nature Journal",
        "description": "Practical quantum computing applications emerge in drug discovery and materials science..."
      }
    ],
    "html": "https://storage.cloro.dev/results/c45a5081-808d-4ed3-9c86-e4baf16c8ab8/page-1.html", // URL expires after 24 hours
    "markdown": "**Recent developments in quantum computing** include breakthrough error correction methods..."
  }
}
```

## Comprehensive structured data extraction

The Perplexity endpoint automatically detects different types of queries and extracts rich structured data based on search intent:

- **Shopping queries**: Product information, pricing, and shopping cards
- **Travel queries**: Hotel listings, places, and location data
- **Media queries**: Videos and images relevant to the search
- **General queries**: Standard web search with sources and citations

### Query intent detection

The endpoint automatically analyzes your query to determine intent:

| Query Type | Detected Intent                 | Extracted Data                           |
| ---------- | ------------------------------- | ---------------------------------------- |
| Shopping   | E-commerce and product searches | Shopping cards with product details      |
| Travel     | Hotels, destinations, locations | Hotel listings, places, coordinates      |
| Media      | Images, videos, visual content  | Media items with thumbnails and metadata |
| General    | Informational searches          | Text response with sources               |

### Shopping cards (when shopping intent detected)

When shopping intent is detected, the response includes:

```json
{
  "result": {
    "shopping_cards": [
      {
        "products": [
          {
            "title": "Product Name",
            "url": "https://example.com/product",
            "description": "Product description",
            "price": "$99.99",
            "original_price": "$149.99",
            "rating": 4.5,
            "numReviews": 1250,
            "imageUrls": ["https://example.com/image1.jpg"],
            "merchant": "Store Name",
            "id": "product_123",
            "variants": [
              {
                "name": "Size",
                "value": "Large"
              }
            ],
            "offers": [
              {
                "price": "$99.99",
                "merchant": "Store Name",
                "url": "https://example.com/offer"
              }
            ]
          }
        ],
        "tags": ["category1", "category2"]
      }
    ]
  }
}
```

### Media content (videos and images)

For media-related queries, the response includes structured video and image data:

```json
{
  "result": {
    "videos": [
      {
        "title": "Video Title",
        "url": "https://youtube.com/watch?v=example",
        "thumbnail": "https://example.com/thumb.jpg",
        "medium": "video",
        "source": "youtube"
      }
    ],
    "images": [
      {
        "title": "Image Title",
        "url": "https://example.com/image.jpg",
        "thumbnail": "https://example.com/thumb.jpg",
        "medium": "image",
        "source": "stock_photo",
        "image_width": 1920,
        "image_height": 1080,
        "thumbnail_width": 320,
        "thumbnail_height": 180
      }
    ]
  }
}
```

### Travel and location data

For travel queries, extract hotel and place information:

```json
{
  "result": {
    "hotels": [
      {
        "name": "Hotel Name",
        "url": "https://example.com/hotel",
        "rating": 4.3,
        "num_reviews": 1250,
        "address": ["123 Main St", "City, State"],
        "phone": "+1-555-0123",
        "description": "Hotel description",
        "image_url": "https://example.com/hotel.jpg",
        "images": ["https://example.com/room1.jpg"],
        "lat": 40.7128,
        "lng": -74.006,
        "price_level": "$$$",
        "categories": ["Hotel", "Accommodation"]
      }
    ],
    "places": [
      {
        "name": "Place Name",
        "url": "https://example.com/place",
        "address": ["456 Oak Ave", "City, State"],
        "rating": 4.7,
        "lat": 40.7128,
        "lng": -74.006,
        "categories": ["Restaurant", "Tourist Attraction"],
        "map_url": "https://maps.google.com/example",
        "images": ["https://example.com/place.jpg"]
      }
    ]
  }
}
```

### Additional response data

The response also includes:

| Field                         | Type  | Description                                       |
| ----------------------------- | ----- | ------------------------------------------------- |
| `result.related_queries`      | array | Suggested follow-up search queries                |
| `result.search_model_queries` | array | Internal search queries used to generate response |

Each search model query includes the actual query, engine type, and result limit.

### Real-time web sources

Perplexity integrates with real-time web sources, providing current and up-to-date information:

| Field         | Type    | Description                                   |
| ------------- | ------- | --------------------------------------------- |
| `position`    | integer | Position order of the source in the response  |
| `url`         | string  | Direct URL to the source content              |
| `label`       | string  | Source name or publication                    |
| `description` | string  | Brief description of what the source contains |

## Practical Perplexity scraper use cases

1. **E-commerce monitoring:** Track product pricing, availability, and merchant offers across shopping queries.
2. **Travel research:** Extract hotel information, pricing, and location data for travel planning and competitive analysis.
3. **Media analysis:** Collect video and image content for content research, social media analysis, and visual data extraction.
4. **Current events monitoring:** Track breaking news and recent developments with real-time source verification.
5. **Research automation:** Gather comprehensive information with automatic source citations for academic and professional research.
6. **Market intelligence:** Monitor industry trends, product offerings, and competitive landscape with structured data extraction.
7. **Content verification:** Fact-check information using Perplexity's integrated source attribution and structured data.
8. **Location-based insights:** Extract place information, coordinates, and business details for geographic analysis.

## Why choose cloro?

- **Simple integration:** Clean API design with comprehensive documentation and examples.
- **Reliable performance:** >99% uptime and low latencies (P50 < 30s, P90 < 60s)
- **No infrastructure hassle:** We handle rate limiting and browser management.
- **Real-time data:** Access to current information with automatic source attribution.
- **Developer support:** Responsive support team to help with integration and troubleshooting.

## FAQ

### Is scraping Perplexity allowed?

Any website is legal to be scraped as long as the information is publicly accessible.

### What makes cloro's Perplexity scraper unique?

cloro's Perplexity endpoint provides reliable access to Perplexity's AI-powered search with:

- **Real-time web source integration** for current information and breaking news
- **Automatic source attribution** for verification and credibility
- **Query intent detection** that automatically extracts shopping cards, media content, and travel data
- **Comprehensive structured data extraction** for seamless integration into your workflows
- **Multi-format responses** including text, HTML, markdown, and rich structured objects

### What's the recommended timeout for requests?

We don't recommend putting any timeout, given that our system retries automatically. We recommend setting up a retry mechanism in case of failure.

### How current is the information from Perplexity?

Perplexity provides real-time access to current information and breaking news through its web integration, making it ideal for monitoring current events and recent developments in any field.

### Does the API support different countries?

Yes, you can specify country codes like `US`, `GB`, `DE`, `JP`, `CN`, `IN`, `BR` and more to get localized results and sources relevant to specific regions.

## Learn more

For detailed documentation, advanced features, and integration guides, visit:

- **API documentation:** [docs.cloro.dev](https://docs.cloro.dev/)
- **Perplexity scraper page:** [cloro.dev/perplexity](https://cloro.dev/perplexity/)

## Other available scrapers

- **[AI Mode](https://cloro.dev/ai-mode/)** - Extracts structured data from Google AI Mode for general knowledge queries, workflow optimization, and technical guidance.
- **[AI Overview](https://cloro.dev/ai-overview/)** - Extracts structured data from Google AI Overview for comprehensive search result analysis and AI-curated insights.
- **[ChatGPT](https://cloro.dev/chatgpt/)** - Extracts structured data from ChatGPT with advanced features including shopping cards, raw response data, and query fan-out.
- **[Copilot](https://cloro.dev/copilot/)** - Extracts structured data from Microsoft Copilot for development tools, Microsoft ecosystem research, and enterprise-focused queries.
- **[Gemini](https://cloro.dev/gemini/)** - Extracts structured data from Google Gemini for complex reasoning, content generation, and source confidence scoring.
- **[Google Search](https://cloro.dev/google-search/)** - Extracts structured data from Google Search results, including organic results, People Also Ask questions, related searches, and optional AI Overview data.
- **[Google News](https://cloro.dev/google-news/)** - Extracts structured news articles from Google News with titles, snippets, sources, dates, and thumbnail images for news monitoring and media tracking.
- **[Grok](https://cloro.dev/grok/)** - Extracts structured data from Grok for current events, news tracking, and real-time information gathering.
- **[Perplexity](https://cloro.dev/perplexity/)** - Extracts comprehensive structured data from Perplexity AI with real-time web sources, automatically detecting and extracting rich data objects.

## Contact us

If you have questions or need support, reach out to us at [support@cloro.dev](mailto:support@cloro.dev).

---

Built with ❤️ by the cloro team
