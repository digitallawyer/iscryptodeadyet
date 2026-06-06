# Is Crypto Dead Yet?

Live at **https://digitallawyer.github.io/iscryptodeadyet/**.

A single-page infographic comparing the **Bitcoin price** against
**worldwide Google search interest for "is crypto dead?"** since 2010.

The page is one self-contained HTML file. The trend data is embedded in
the page (and parsed client-side), the Bitcoin price chart uses monthly
closes from CryptoCompare baked into the JS, and the *current* Bitcoin
price is fetched live from the public CoinGecko API on every page load
(and refreshed every 60s).

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The page. Self-contained: HTML + CSS + JS + raw CSV in one file. |
| `time_series_Worldwide_20031231-1600_20260606-1519.csv` | The same Google Trends CSV the page is built on, kept beside the page for raw inspection. |
| `README.md` | This file. |

The same CSV content is also embedded inside `index.html` (look for
`<script id="trends-csv">`), so the page works correctly even when opened
straight from disk (`file://`). The **Download data (CSV)** button on the
page builds a Blob from that embedded copy, so the download is identical
to the file in this repo.

## Live Bitcoin price

The current BTC price is pulled from CoinGecko's free public endpoint:

```
GET https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd
```

No API key is required, CORS is enabled, and the rate limits are well
within what a static GitHub Pages site will use (one request per visitor
per 60 seconds). The fetched price:

- replaces the embedded "current month" value in the chart (so the orange
  line tip moves in real time);
- updates the "BTC price · live" stat with a flash animation on change;
- recomputes the "BTC from peak" drawdown stat live;
- shows a small green pulsing dot + `LIVE · COINGECKO` label.

If the API call fails (rate limit, offline, blocked) the badge turns red
and reads "live feed offline", and the page falls back to the last
embedded monthly close so nothing breaks.

## Deploy to GitHub Pages (free)

GitHub Pages will happily host this — it is a plain static site.

1. Push the repo to GitHub:

   ```bash
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin git@github.com:digitallawyer/iscryptodeadyet.git
   git push -u origin main
   ```

2. On GitHub, go to **Settings → Pages**, set **Source** to
   *Deploy from a branch*, pick branch **`main`** and folder **`/ (root)`**,
   then **Save**.

3. After a minute the site will be live at
   `https://digitallawyer.github.io/iscryptodeadyet/`.

That's it — no build step, no server, no API key. The CoinGecko fetch
happens directly from the visitor's browser.

### Custom domain (optional)

If you want `iscryptodeadyet.com` (or any other domain), add a `CNAME`
file to the repo containing only the bare domain, then point a DNS
`CNAME` record at `<your-user>.github.io`.

## Local preview

The page is fully self-contained, so you can just double-click
`index.html`. For an exact preview of how it will behave on GitHub Pages
(including the live BTC fetch over HTTPS), run any static server, e.g.:

```bash
python3 -m http.server 4123
open http://127.0.0.1:4123
```

## Updating the data

To refresh the Google Trends data:

1. Re-run the [Trends query](https://trends.google.com/trends/explore?date=all&q=%22bitcoin%20dead%22,%22crypto%20dead%22,%22bitcoin%20is%20dead%22,%22crypto%20is%20dead%22,%22is%20bitcoin%20dead%22,%22is%20crypto%20dead%22) (Worldwide, all-time).
2. Replace the CSV file and **also** the contents of the
   `<script id="trends-csv">` block in `index.html`.
3. To extend Bitcoin price history, append monthly closes to the
   `BTC_MONTHLY` array. Update `BTC_ATH` and `BTC_ATH_DATE` if a new
   all-time high has been set.

The page recomputes the search index on every load: the peak month
becomes 100 and every other month is rescaled accordingly. So if a future
month surpasses June 2022, the chart simply re-anchors — no code change
required.

## Credits

- Bitcoin price history: [CryptoCompare](https://www.cryptocompare.com/)
- Live Bitcoin spot price: [CoinGecko](https://www.coingecko.com/)
- Search interest: [Google Trends](https://trends.google.com/trends/)
