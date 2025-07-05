# URL-Parser-MD-Worker

A Cloudflare Worker that converts any webpage into clean, readable Markdown using a headless browser.

## Core Principles

This worker utilizes Cloudflare's Browser Rendering (powered by `@cloudflare/puppeteer`) to render a webpage in a headless browser instance. The core logic hinges on two key JavaScript libraries that are injected into the page:

1.  **`@mozilla/readability`**: The engine behind Firefox's "Reader View". It excels at extracting the core article content from a webpage, stripping away distracting elements like navigation bars, ads, and footers.
2.  **`turndown`**: A library that converts the cleaned HTML content into well-formatted Markdown.

The process is straightforward:
1.  Puppeteer navigates to the target URL.
2.  `Readability.js` and `turndown.js` are injected into the page.
3.  Readability parses the DOM to isolate the main article content.
4.  Turndown takes the resulting HTML and converts it into Markdown.
5.  The final Markdown is returned as the response.

## Limitations

This worker is built on Cloudflare's Browser Rendering API, which has significant limitations:

*   **Rate Limiting**: There are strict limits on the number of concurrent browser sessions and overall requests.
*   **Timeouts**: Long-running pages may time out.
*   **Resource Constraints**: The browser environment is resource-constrained (e.g., no GPU acceleration).

For detailed and up-to-date information, please refer to the official Cloudflare documentation:
[Cloudflare Browser Rendering Platform Limits](https://developers.cloudflare.com/browser-rendering/platform/limits/)

## Usage

Make a `GET` request to your worker's endpoint.

**Example:**
```bash
curl "https://your-worker-url.com/?url=https://example.com"
```

### Parameters

*   **`url`** (string, **required**): The URL of the webpage to convert.
*   **`enableDetailedResponse`** (boolean, optional): Returns the full page HTML converted to markdown, not just the article content. Defaults to `false`.
*   **`crawlSubpages`** (boolean, optional): Crawls and returns markdown for up to 10 subpages. Defaults to `false`.
*   **`llmFilter`** (boolean, optional): Filter out unnecessary information using LLM. Defaults to `false`.

### Response Format

Add the corresponding `Content-Type` header to your request:
*   For plain text: `text/plain`
*   For JSON: `application/json`

## Self-Hosting

This project is designed to be easily self-hosted on Cloudflare Workers. You will need the [Workers paid plan](https://developers.cloudflare.com/workers/platform/pricing) to use Browser Rendering.

1.  **Clone & Install**:
    ```bash
    git clone https://github.com/Leizhenpeng/url-parser-md-worker
    cd url-parser-md-worker
    npm install
    ```
2.  **Configure `wrangler.toml`**: You may need to adjust the configuration in `wrangler.toml` to match your Cloudflare account details.

3.  **Deploy**:
    ```bash
    npm run deploy
    ```
4.  That's it! Your worker is live.

## Development

To develop and test the worker, run the following command:

```bash
npm run dev
```

This will start a local development server using `wrangler dev --remote`, which connects to the Cloudflare network. This allows you to test your worker on a real Cloudflare endpoint, with live-reloading on code changes.

## Support

Support me by simply starring this repository! ⭐
