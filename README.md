# Mohamed Salman — Interactive GitHub Profile Explorer

A detailed, data-rich, **single-page web app** that showcases Mohamed Salman
([@Sal1243](https://github.com/Sal1243)) — AI Automation Engineer & Web Developer.

The page pulls live data from the public GitHub REST API and renders:

* 👤 Hero panel (avatar, bio, location, company, blog)
* 📊 Stats strip (repos, followers, years on GH, top language)
* 🍩 Language distribution donut chart (Chart.js)
* 🧰 Tech-stack tiles (grouped by Frontend / Backend / AI / Data / Deploy / Design)
* 🔎 Filterable, searchable repo grid
* 🕒 Activity timeline (repos grouped by year)
* 💌 Contact + social links
* 🛡 Graceful offline / rate-limit fallback

See **[ARCHITECTURE.md](./ARCHITECTURE.md)** for the full system design —
data model, runtime flow, deployment options, and future enhancements.

---

## 🚀 Run it

No build step. Just open `index.html` or serve the folder.

```bash
# Option 1 — open directly
open index.html        # macOS
xdg-open index.html    # Linux

# Option 2 — local static server
python3 -m http.server 8000
# then visit http://localhost:8000
```

## 📁 Files

| File             | Purpose |
|------------------|---------|
| `index.html`     | The interactive UI (Tailwind + Chart.js via CDN) |
| `ARCHITECTURE.md`| System architecture document |
| `README.md`      | This file |

## 🔌 Data source

| Endpoint | Used for |
|---|---|
| `GET /users/Sal1243` | Profile metadata |
| `GET /users/Sal1243/repos?per_page=100` | All 24 public repos |
| `GET /repos/Sal1243/Sal1243/readme` | Profile README (for fallback copy) |

Responses are cached in `sessionStorage` for **5 minutes** to stay inside
GitHub's unauthenticated 60-req/hour rate limit.

## 🧱 Tech

* **TailwindCSS** (CDN) — utility-first styling
* **Chart.js** (CDN) — language donut
* **Vanilla JS** — no framework, no build, no lock-in
* **GitHub REST API v3** — live data

## 📜 License

MIT — feel free to fork and adapt.
