# Horizon Search

A self-hosted, privacy-first search engine — a custom theme + AI side panel built on top of [SearXNG](https://github.com/searxng/searxng).

- OLED true-black UI, Pacifico wordmark, sunset-orange accent
- Card mosaic for results, news, and videos
- Maps tab powered by Foursquare + OpenStreetMap (Leaflet)
- Optional AI side panel that connects directly to your own Ollama server — no inference data ever touches the Horizon backend
- Agentic loop: the AI can drive the search bar on your behalf (`goto_search`) or pull in extra context invisibly (`search`)

## Quick start

Prerequisites: Docker + Docker Compose.

```bash
git clone https://github.com/60MilesPerHour/horizon-search.git
cd horizon-search

# 1. Copy the example configs
cp searxng/settings.example.yml searxng/settings.yml
cp .env.example .env

# 2. Generate a fresh SearXNG secret_key
openssl rand -hex 32
# paste the output into searxng/settings.yml at server.secret_key

# 3. (Optional) Fill in Foursquare creds in .env for the Maps tab
#    Get them at https://foursquare.com/developers/

# 4. Bring it up
docker compose up -d
```

Default port: `:8888`. Open `http://localhost:8888`.

## AI side panel (optional)

Bring-your-own Ollama. Click `horizon` in the bottom bar → enable AI panel → enter your Ollama URL + pick a tool-capable model → save. The pill in the search row turns orange when the panel is open.

**Model requirements:** only tool-capable models are listed (capability filtered via Ollama's `/api/show`). Tested on:
- `llama3.1` / `llama3.2` / `llama3.3`
- `qwen2.5` / `qwen3`
- `mistral-nemo`
- `command-r`

**CORS gotcha:** Ollama refuses cross-origin browser requests by default. On the Ollama host, set:

```bash
OLLAMA_ORIGINS=*    # or your Horizon domain
```

…then restart Ollama. The settings modal also reminds you of this inline.

**What the panel can do:**
- Read every visible result card on the page as context (title, source, snippet, date)
- Cite with clickable `[N]` chips that scroll to + flash the matching card
- Stream answers with markdown (bold, lists, headers, links)
- Call `goto_search(query, category)` — types the query into the main search bar and navigates, restoring the conversation on the new page via `sessionStorage`
- Call `search(query, category)` — fetches results invisibly via `/search?format=json`, appends them to the citation map with continuation numbering
- Bounded at 3 tool hops per turn to prevent runaway loops

## What's in here

```
templates/         Jinja templates that override SearXNG's simple theme
  base.html        Globals: fonts, palette, settings modal
  index.html       Landing page
  results.html     All result tabs + AI side panel
searxng/           Files mounted into /etc/searxng inside the container
  settings.example.yml   Engine selection + features (copy to settings.yml)
  horizon-logo.png       Sunset logo
  favicon.svg
  sxng-ltr.min.css       Theme CSS overrides
  limiter.toml           Rate-limit rules
infopage/en/       About + search-syntax pages
docker-compose.yml SearXNG + Valkey
```

## Customising

- **Accent color:** change `--accent` (and friends) in `templates/base.html` at the top of the `<style>` block. Currently `#ff7200`.
- **Logo:** replace `searxng/horizon-logo.png` (1024×1024 recommended) and re-deploy.
- **Engines:** edit `searxng/settings.yml` — see [SearXNG docs](https://docs.searxng.org/admin/settings/index.html).
- **AI tools:** the agentic loop lives at the bottom of `templates/results.html` (inside the last `<script>` block). The tool definitions are in the `TOOLS` array; the system prompt is `SYSTEM_PROMPT`.

## Re-deploying after template edits

Templates are bind-mounted read-only, so a restart is enough:

```bash
docker restart searxng
```

For volume / compose changes:

```bash
docker compose up -d --force-recreate searxng
```

## Notes

- The AI panel is entirely opt-in; the rest of the search engine works without it.
- Image thumbnails for Bing News bypass SearXNG's `image_proxy` (the server IP is blocked) — the browser fetches them directly with `referrerpolicy="no-referrer"`.
- The Maps tab uses Foursquare Places for venue data and OpenStreetMap/Nominatim for geocoding. Both calls go through small backend proxies (Foursquare key never reaches the browser).

## License

MIT — see `LICENSE`.
