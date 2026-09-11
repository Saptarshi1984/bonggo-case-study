# BongGo — Case Study

**A culture-first Bengali learning platform, designed and built solo, end-to-end.**

🔗 **Live product:** [bonggo.app](https://bonggo.app)

<!-- SCREENSHOT: homepage hero (landing page above the fold, desktop) -->
<!-- ./assets/hero.png -->

---

## What is BongGo?

BongGo is a full-stack, production web app that teaches Bengali through culture rather than isolated drills — vocabulary and grammar are taught alongside food, festivals, comics, and everyday conversation. It serves two kinds of learners from day one: adults who sign up directly, and children who join through a parent-managed, consent-gated account flow.

It's a real, live product with paying-attention-level scope: structured lessons, reading & listening comprehension, guided speaking practice with live transliteration, a persistent writing editor, gamified flashcard games, illustrated comics, a leaderboard, and a comment-driven community layer — all sitting on top of a compliance system built for handling minors' data responsibly.

---

## My Role

I designed, built, and ship BongGo **solo** — there is no team behind this. That means owning every layer:

- **Product & UX design** — deciding what "culture-first" learning actually looks like as a product, not just a content list
- **Data modeling** — 25 MongoDB/Mongoose schemas covering users, lessons, progress, comics, speaking sessions, decks, consent, and more
- **Full-stack engineering** — Next.js App Router frontend, Route Handler backend, auth, and middleware
- **Compliance & legal flows** — a COPPA-aware signup and consent system, built and maintained without a legal or compliance team
- **Content authoring** — writing and structuring the lessons, comics, and blog content that live inside the app
- **DevOps** — containerizing and deploying the app to a production VPS behind nginx

---

## The Problem

Most language-learning apps treat vocabulary as a flashcard problem. BongGo starts from a different premise: language sticks better when it's tied to culture — food, festivals, comics, real conversation — not memorized in a vacuum. Building that meant more than writing lesson content; it meant designing a platform flexible enough to mix exercise types, reading, listening, speaking, and writing into one coherent learning path, while also supporting a real-world constraint most side projects skip entirely: **children use this product too**, which means age-aware compliance had to be part of the architecture from day one, not bolted on later.

---

## Feature Highlights

### 📚 Structured Lessons
Interactive lessons built from four interchangeable exercise types — multiple-choice audio drills, drag-to-match, sentence unjumbling, and guided speaking prompts — with XP rewards, retake credit, and an in-app flow for learners to flag bad content or audio.

<!-- SCREENSHOT: lesson player mid-exercise -->
<!-- ./assets/lesson-player.png -->

### 📖 Reading & Listening
Bilingual comprehension stories with tabbed Bengali / romanized / English views, paired with a custom audio player.

<!-- SCREENSHOT: reading & listening view -->
<!-- ./assets/reading.png -->

### 🗣️ Speaking Practice
Sentence-level speaking exercises with per-attempt tracking, backed by a **live English → Bengali transliteration API** so learners can type in Bengali script without needing a native keyboard.

<!-- SCREENSHOT: speaking practice screen -->
<!-- ./assets/speaking.png -->

### ✍️ Writing Editor
A full rich-text editor (built on Slate.js) for free-form Bengali writing practice, with autosaving, per-user document history, and plain-text previews.

<!-- SCREENSHOT: writing editor -->
<!-- ./assets/editor.png -->

### 🎮 Games & 📚 Comics
A flashcard game built on a deck/spaced-repetition-style model, and multi-episode illustrated comics organized by difficulty for culture-first reading practice outside the formal lesson track.

<!-- SCREENSHOT: comics reader / flashcard game -->
<!-- ./assets/comics.png -->

### 🔥 Gamification & Leaderboard
XP, daily streaks, a visual XP heatmap and activity chart, celebratory confetti on milestones, and a leaderboard so learners can see how they stack up.

<!-- SCREENSHOT: dashboard with streak / XP heatmap -->
<!-- ./assets/dashboard.png -->

<!-- SCREENSHOT: leaderboard -->
<!-- ./assets/leaderboard.png -->

### 💬 Community
A blog with likes and a full threaded comment system (upvote/downvote, moderation pipeline), plus a notification system and in-app feedback capture.

---

## Engineering Highlights

A few things in this build go beyond a typical portfolio project:

- **COPPA-aware compliance system.** Age is collected as a direct integer (never a birthdate field), learners and parents are routed through different signup paths, and child profiles are nested under the parent's own account rather than issued separate credentials. Consent is versioned and tracked through anonymous-to-authenticated handoff, and a live `isMinor` flag carried in the session token drives server-trusted ad-gating — not just a UI toggle.
- **A custom MCP (Model Context Protocol) server.** A standalone service exposing BongGo's lesson data as tools an AI agent can call (`get_lesson_details`, `get_lesson_vocab`, `get_lesson_matching_exercises`, and more) — containerized separately and wired into the main app over an internal network. A concrete example of shipping infrastructure for AI-agent integration, not just consuming an LLM API.
- **Security-hardened input handling.** A shared validation layer sanitizes plain-text input and blocks MongoDB operator-injection (`$where`, `$gt`, etc.) before any user-submitted content — feedback, comments, profile fields — reaches a database query.
- **Gamification systems built from scratch.** UTC-day-boundary streak logic with automatic reset, XP scoring with first-completion and perfect-score bonuses, and heatmap-based progress visualization.
- **Production deployment.** Dockerized (multi-stage build, non-root runtime) and deployed to a VPS behind nginx, replacing an earlier PM2-based deploy.

---

## Tech Stack

| Layer | Technologies |
|---|---|
| **Frontend** | Next.js 16 (App Router) · React 19 · TypeScript · Chakra UI v3 · Tailwind CSS v4 |
| **Backend** | Next.js Route Handlers · NextAuth v5 (Google OAuth, JWT sessions) |
| **Database** | MongoDB · Mongoose |
| **Content editing** | Slate.js (rich-text writing editor) |
| **Data visualization** | Chart.js / react-chartjs-2 |
| **AI / tooling** | Custom MCP server (Model Context Protocol SDK) |
| **Infrastructure** | Docker · nginx · VPS deployment |

---

## Scale Snapshot

| Metric | Count |
|---|---|
| Application code | ~215 TypeScript/TSX files, ~30,500 lines |
| Data models | 25 Mongoose schemas |
| API route handlers | 23 |
| Reusable UI components | 79 |
| Structured lessons | 25+ |

---

## Architecture at a Glance

- **Route-group separation**: public marketing pages, an authenticated dashboard shell, and a standalone writing-editor shell all live under the Next.js App Router as distinct route groups.
- **Middleware-enforced auth boundary**: a single proxy/middleware layer gates every protected route and redirects based on session state — the frontend never has to guess whether a page is allowed.
- **Decoupled media**: audio and images are served from a dedicated CDN host rather than the database or app bundle, keeping the deployment lean.
- **Server-trusted compliance state**: minor status and consent state live in the session token and the database — not just client-side flags — so downstream systems (ads, content-gating) can trust them.


---

## Try It

**Live at [bonggo.app](https://bonggo.app)** — sign in with Google to explore the full learner dashboard.
