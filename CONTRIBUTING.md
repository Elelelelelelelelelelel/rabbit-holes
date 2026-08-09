# Contributing & building The Rabbit Holes

This is the technical manual — how the site is put together, how to publish it, and how to add a new rabbit hole. If you're a visitor looking for what this project *is*, see [README.md](README.md) instead.

Interactive explainers where every question runs ten levels deep. Readers descend level by level, and every level ends with a checkpoint: stop with a complete answer, or go deeper.

No build tools, no frameworks, no server — plain HTML, CSS and vanilla JavaScript, designed to live on **GitHub Pages**.

## What's in this repository

```
├── index.html                ← homepage / level-select screen + "Dig your own hole" suggestion box
├── coffee/index.html         ← "Why can some people drink coffee at night and still sleep?"
├── dejavu/index.html         ← "Why do we get déjà vu?"
├── _template/index.html      ← annotated blank template for new questions
├── .github/ISSUE_TEMPLATE/
│   └── rabbit-hole-request.yml  ← the form readers fill in when suggesting a question
├── .nojekyll                 ← keeps GitHub Pages from hiding the _template folder
├── README.md                 ← what visitors see on the repository's front page
└── CONTRIBUTING.md           ← this file
```

Each rabbit hole is a single self-contained file: a **CONFIG section at the top** (the content — this is all you edit) and a shared **ENGINE section below it** (the game mechanics — leave it alone). The engine handles the HUD, depth bar, unlock animations, checkpoints, the "You surfaced" recap, and saving each reader's progress privately in their own browser.

## Publishing on GitHub — step by step

1. **Create the repository.** On github.com choose *New repository*, name it (e.g. `rabbit-holes`), make it **Public**, and upload everything in this folder — `index.html` must sit at the repository root. (Uploading via the web interface is fine: *Add file → Upload files*, drag the lot in.)
2. **Point the site at your repository.** Four places contain a placeholder you must replace with your real `username/repo`:
   - `index.html` — the `GITHUB_REPO` constant at the top of the script (powers the suggestion box)
   - `coffee/index.html` — `githubRepo` inside CONFIG (powers the discussion panel's link to Discussions)
   - `dejavu/index.html` — same `githubRepo` field
   - `README.md` — the live-site link near the top
3. **Turn on the community features.** In the repository: *Settings → General → Features* — tick **Issues** (for question suggestions) and **Discussions** (for shared results). Then open the *Discussions* tab and create one thread per hole — e.g. **"☕ The coffee hole — comments & ten-day experiment results"** and **"🌀 The déjà vu hole — comments & jamais vu results"** — that's where each hole's discussion panel sends people. Pin them so new visitors see them immediately.
4. **Enable Pages.** *Settings → Pages → Source: Deploy from a branch → main → / (root) → Save.* A minute later your site is live at `https://yourusername.github.io/rabbit-holes/`.
5. **Test the loop.** Visit the live site, pick a few depth locks on the way down to level 10, and check the discussion panel opens your Discussions; then try the "Dig your own hole" box — it should open a ready-filled issue on your repository.

Later you can buy a domain (roughly £8–10 a year) and connect it under *Settings → Pages → Custom domain*.

## How readers interact with the site

- **Depth riddles (the game loop):** every "Go deeper" button opens a riddle drawn from the level just read, in one of three flavours — multiple choice, true/false, or drag-a-slider-to-guess-the-number. A correct answer unlocks the next level, awards one of the hole's **tokens** (counted in the HUD), and introduces one of a small cast of animal characters holding that token, with a kind line — plus an optional bonus fact. The cast and its lines are shared across the whole series and deliberately subject-neutral; only the token changes per hole, set by the `token` field in CONFIG (coffee earns ☕ beans, déjà vu earns 🌀 echoes). Wrong answers get unlimited retries, with an in-character remark on the first miss and a genuine hint after the second. Levels without a `puzzle` field unlock freely. This gate applies everywhere a reader can move forward — including resuming from the "you surfaced" modal — so there's no back door around it.
- **Suggesting questions:** the homepage's "Dig your own hole" box opens a pre-filled GitHub issue using the form in `.github/ISSUE_TEMPLATE/`. Readers propose the question; you build the descent. Suggestions arrive labelled `rabbit-hole-request`, with fields for the question, why it deserves ten levels, and how to credit the suggester.
- **Discussing and sharing results:** level 10 of each hole carries a discussion panel that links straight to this repository's GitHub Discussions — that's where readers comment on the hole and share what happened when they ran the ten-day experiment. Nothing is stored or logged on the site itself; the only thing kept in the reader's own browser is their level progress.

## Adding a new rabbit hole

1. Copy the `_template` folder and rename it to your question's slug, e.g. `dejavu/`.
2. Edit **only the CONFIG section** of the new `index.html` (the comments walk you through every field), plus the `<title>` and meta tags in the `<head>`. Give the hole its own `token` — the emoji and noun readers collect for solving riddles — so the reward cast fits the subject rather than always handing out coffee beans.
3. Follow the depth curve: levels 1–2 the plain answer, 3–4 the mechanism, 5–6 the real machinery, 7–8 the complications, 9 the plot twist, 10 the synthesis plus something the reader can *do*. Give levels 1–9 a depth-lock `puzzle` each — mix the three types (`mcq`, `truefalse`, `slider`) rather than defaulting to one, and add an optional `fact` as a reward for getting it right. Put the ready-made `discussPanel` widget on level 10 so readers can comment.
4. Add a card to the `HOLES` array in the root `index.html` with `status: "live"`. Questions you're planning can sit there as `status: "soon"` teaser cards.
5. Commit, push, and Pages redeploys automatically.

House style, kept deliberately: one signature interactive per hole; checkpoints that make stopping feel legitimate; level titles that are claims, not topics.

## Notes

- The only thing stored is each reader's level progress, privately in **their** browser via `localStorage` (`rh-progress-*` keys) — nothing is sent anywhere and nothing about them is logged. Each page has a "Reset my progress" link in its footer.
- `.nojekyll` matters: without it, GitHub Pages' Jekyll processing silently drops any folder starting with an underscore, including `_template`.
- The pages use `color-mix()` in CSS, supported by all major browsers since 2023.
- Before sharing widely, consider adding an `og:image` (a 1200×630 screenshot) to each page's `<head>` so links preview nicely on social media.
