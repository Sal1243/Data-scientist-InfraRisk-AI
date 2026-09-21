# 🏗️ System Architecture — GitHub Profile Showcase

> A detailed architectural blueprint for an interactive, data-rich profile
> explorer built on top of Mohamed Salman's public GitHub account
> ([github.com/Sal1243](https://github.com/Sal1243)).

---

## 1. 🎯 Purpose & Goals

| Goal | Description |
|---|---|
| **Profile storytelling** | Convert raw GitHub API data into a human-friendly narrative about Mohamed Salman — an AI Automation Engineer & Web Developer. |
| **Live data** | Pull repos, languages, profile metadata, and contribution stats directly from GitHub so the page never goes stale. |
| **Visual depth** | Provide a multi-layered UI (hero → stats → projects → tech stack → timeline → contact) instead of a flat list. |
| **Architecture transparency** | Document every layer, data source, and design decision so the project itself becomes a portfolio piece. |

---

## 2. 🧱 High-Level Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                         USER  (Browser)                              │
└──────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────┐
│               FRONT-END  (Static SPA — index.html)                   │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────────────┐  │
│  │  Hero Panel  │  │ Stats Panel  │  │  Repo Cards (filtered)     │  │
│  ├──────────────┤  ├──────────────┤  ├────────────────────────────┤  │
│  │ Skill Radar  │  │ Lang Chart   │  │  Tech-Stack Tiles          │  │
│  ├──────────────┤  ├──────────────┤  ├────────────────────────────┤  │
│  │ Timeline     │  │ Contribution │  │  Contact / Social Footer   │  │
│  └──────────────┘  └──────────────┘  └────────────────────────────┘  │
│                                                                      │
│  Rendering: TailwindCSS (CDN) + vanilla JS + Chart.js (CDN)          │
└──────────────────────────────────────────────────────────────────────┘
                                  │
                  HTTP/HTTPS  (fetch JSON, no key)
                                  ▼
┌──────────────────────────────────────────────────────────────────────┐
│                       GITHUB  REST  API  v3                          │
│   • /users/Sal1243              (profile metadata)                   │
│   • /users/Sal1243/repos        (24 public repos)                    │
│   • /repos/:owner/:repo/readme  (profile README)                     │
│   • /users/Sal1243/events       (recent activity — optional)         │
└──────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────┐
│                     CACHE LAYER  (in-memory)                         │
│   • 5-min TTL on profile + repos to stay inside 60 req/h unauth limit │
│   • Single-flight fetch to prevent duplicate in-flight calls          │
└──────────────────────────────────────────────────────────────────────┘
```

### Component responsibilities

| Component | Responsibility | Tech |
|---|---|---|
| **Hero** | Avatar, name, bio, location, hireable, blog link | HTML + CSS only |
| **Stats Strip** | Public repos, followers, following, years active, top language | JS computes from API |
| **Repo Grid** | Filterable, searchable cards with language pills | Vanilla JS DOM |
| **Tech-Stack Tiles** | Curated stack (React, Python, n8n, FastAPI, …) | HTML/CSS badges |
| **Skill Radar** | Donut chart of language distribution | Chart.js |
| **Timeline** | Repos sorted by creation date | JS renders DOM |
| **Footer** | Social links (Instagram, LinkedIn, email) | HTML badges |

---

## 3. 🗂️ Data Model (logical)

```ts
Profile {
  login:           string   // "Sal1243"
  name:            string   // "Mohamed Salman"
  avatar_url:      url
  bio:             string
  company:         string   // "@userspark"
  blog:            url      // portfolio on vercel
  location:        string   // "Chennai"
  public_repos:    number   // 24
  followers:       number
  following:       number
  created_at:      ISO-date // 2022-02-09
}

Repo {
  name:            string
  description:     string?
  language:        string?
  stargazers_count:number
  forks_count:     number
  updated_at:      ISO-date
  created_at:      ISO-date
  html_url:        url
  topics:          string[]
}
```

---

## 4. 🎨 UI / Information Architecture

```
 ┌─────────────────────────────────────────────────────────┐
 │   HEADER  ·  gradient bar ·  nav anchors                │
 ├─────────────────────────────────────────────────────────┤
 │   HERO                                                   │
 │   ┌────────┐   Mohamed Salman                            │
 │   │ avatar │   AI Automation Engineer & Web Developer    │
 │   │        │   📍 Chennai   🏢 @userspark                │
 │   └────────┘   ↗ portfolio    ✉ email                   │
 ├─────────────────────────────────────────────────────────┤
 │   STATS STRIP  [Repos 24] [Followers 0] [Following 2]    │
 │                 [Since 2022] [Top: Python]               │
 ├─────────────────────────────────────────────────────────┤
 │   LANGUAGE DISTRIBUTION  (donut chart)                   │
 ├─────────────────────────────────────────────────────────┤
 │   TECH STACK  (color-coded badge tiles)                  │
 ├─────────────────────────────────────────────────────────┤
 │   PROJECTS  (search box + language filter)               │
 │   ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │
 │   │ Repo 1  │ │ Repo 2  │ │ Repo 3  │ │ Repo 4  │ ...    │
 │   └─────────┘ └─────────┘ └─────────┘ └─────────┘        │
 ├─────────────────────────────────────────────────────────┤
 │   ACTIVITY TIMELINE  (newest repos first)                │
 ├─────────────────────────────────────────────────────────┤
 │   FOOTER  ·  socials + "Built with ❤️ by Arena Agent"    │
 └─────────────────────────────────────────────────────────┘
```

### Design tokens

| Token | Value | Use |
|---|---|---|
| `--bg` | `#0d1117` | page background (GitHub dark) |
| `--surface` | `#161b22` | cards |
| `--border` | `#30363d` | card borders |
| `--accent` | `#58a6ff` | links / chart highlights |
| `--text` | `#e6edf3` | body text |
| `--muted` | `#8b949e` | secondary text |
| Font | `Inter, system-ui` | UI |

---

## 5. 🔌 Data Flow (runtime)

```
1. PAGE LOAD
   └─► fetchProfile()      ──► GET /users/Sal1243
   └─► fetchRepos()        ──► GET /users/Sal1243/repos?per_page=100
   └─► fetchProfileReadme() ──► GET /repos/Sal1243/Sal1243/readme

2. TRANSFORM
   └─► aggregateLanguages(repos)   → {Python:3, HTML:2, TS:2, CSS:1}
   └─► sortByDate(repos, 'desc')   → timeline
   └─► groupByYear(repos)          → year buckets

3. RENDER
   └─► renderHero(profile)
   └─► renderStats(profile, repos)
   └─► renderLangChart(langMap)
   └─► renderTechStack()
   └─► renderRepoGrid(repos)
   └─► renderTimeline(repos)

4. INTERACTION
   └─► search input       → filters repo cards client-side
   └─► language dropdown  → filters repo cards client-side
   └─► sort selector      → re-sorts repo cards

5. ERROR / EMPTY STATES
   └─► rate-limit banner  → friendly message + retry button
   └─► no-results         → "No repos match your filter"
```

---

## 6. 🛡️ Robustness & Limits

| Concern | Mitigation |
|---|---|
| GitHub unauthenticated rate-limit (60 req/h) | Cache responses for 5 min; show a soft banner if `X-RateLimit-Remaining < 5`. |
| CORS | Use the public REST API (already CORS-enabled for browsers). |
| Missing fields (e.g. `null` language) | Guard with `?? 'Other'`. |
| Network failure | Render skeleton placeholders + retry button. |
| Large repo count | Paginate; we cap at 100 (current account only has 24). |
| Accessibility | Semantic HTML, focus rings, `aria-label` on icon buttons, prefers-color-scheme respected. |

---

## 7. 📁 Project Structure

```
Data-scientist-InfraRisk-AI/
├── ARCHITECTURE.md     ← you are here
├── README.md           ← user-facing docs
├── index.html          ← single-page UI (loads everything from CDN)
└── assets/
    └── screenshot.png  ← preview of the running UI
```

No build step. Drop into any static host (GitHub Pages, Vercel, Netlify, `python -m http.server`).

---

## 8. 🚀 Deployment Options

| Option | Command |
|---|---|
| Local | `python3 -m http.server 8000` → http://localhost:8000 |
| GitHub Pages | push `index.html` to `gh-pages` branch |
| Vercel | `vercel --prod` |
| Netlify drop | drag-and-drop the folder at app.netlify.com/drop |

---

## 9. 🔮 Future Enhancements

* Pull **contribution graph** via the unofficial `/users/:user/contributions` SVG endpoint.
* Add a **commit activity sparkline** per repo using the commits API.
* Dark / light mode toggle persisted in `localStorage`.
* Lazy-load repo **READMEs** for the top 5 most-recent repos and render as markdown.
* Optional **GitHub token** input to raise the rate limit to 5,000 req/h.

---

*Built by Arena Agent for **Mohamed Salman** ([@Sal1243](https://github.com/Sal1243)).*
