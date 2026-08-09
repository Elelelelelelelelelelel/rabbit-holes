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
│   ├── rabbit-hole-request.yml  ← the form readers fill in when suggesting a question
│   └── reader-note.yml          ← the form readers fill in to send a note
├── .nojekyll                 ← keeps GitHub Pages from hiding the _template folder
├── README.md                 ← what visitors see on the repository's front page
└── CONTRIBUTING.md           ← this file
```

Each rabbit hole is a single self-contained file: a **CONFIG section at the top** (the content — this is all you edit) and a shared **ENGINE section below it** (the game mechanics — leave it alone). The engine handles the HUD, depth bar, unlock animations, checkpoints, the "You surfaced" recap, and saving each reader's progress privately in their own browser.

## Publishing on GitHub — step by step

1. **Create the repository.** On github.com choose *New repository*, name it (e.g. `rabbit-holes`), make it **Public**, and upload everything in this folder — `index.html` must sit at the repository root. (Uploading via the web interface is fine: *Add file → Upload files*, drag the lot in.)
2. **Fill in your details.** Replace the placeholder `username/repo` in three places — it powers every suggestion and note link:
   - `index.html` — the `GITHUB_REPO` constant at the top of the script
   - `coffee/index.html` and `dejavu/index.html` — `githubRepo` inside CONFIG
   - `README.md` — the live-site link near the top
3. **Check Issues are on.** In the repository: *Settings → General → Features* — **Issues** should be ticked (it is by default). That's the only thing the site needs: both the "Dig your own hole" suggestion form and every reader note post there, using the two templates in `.github/ISSUE_TEMPLATE/`. Discussions aren't used, and no email address appears anywhere on the site.
4. **Enable Pages.** *Settings → Pages → Source: Deploy from a branch → main → / (root) → Save.* A minute later your site is live at `https://yourusername.github.io/rabbit-holes/`.
5. **Test the loop.** Visit the live site, descend to level 10 (checking a reward character greets you at each new level), and confirm the contact panel opens your email app; then try the "Dig your own hole" box — it should open a ready-filled issue on your repository.

Later you can buy a domain (roughly £8–10 a year) and connect it under *Settings → Pages → Custom domain*.

## How readers interact with the site

- **The descent reward (the game loop):** choosing "Go deeper" is itself the reward moment. The next level opens with a card at the top: one of a shared cast of animal characters holding this hole's **token**, a warm line, +1 to the token counter in the HUD, and — where the previous level defined a `fact` — an obscure bonus nugget one layer deeper than what they just read. The cast and its lines are shared across the series and deliberately subject-neutral; only the token changes per hole, set by `token` in CONFIG (coffee earns ☕ beans, déjà vu earns 🌀 echoes). There are no quizzes or gates: readers move at will, and stopping stays a legitimate ending at every checkpoint.
- **Suggesting questions:** the homepage's "Dig your own hole" box opens a pre-filled GitHub issue using the form in `.github/ISSUE_TEMPLATE/`. Readers propose the question; you build the descent. Suggestions arrive labelled `rabbit-hole-request`, with fields for the question, why it deserves ten levels, and how to credit the suggester.
- **Reader notes:** level 10 of each hole ends with a note panel linking to a ready-filled GitHub issue (the `reader-note.yml` template), plus a "read what others sent" link filtered to the `reader-note` label. The homepage carries the same pair in its suggestion box and footer. Everything routes through Issues, so **no email address is exposed anywhere** and there's nothing to set up beyond having Issues enabled. Notes arrive in your repository's Issues tab, labelled and separable from question suggestions (`rabbit-hole-request`).

## Adding a new rabbit hole

1. Copy the `_template` folder and rename it to your question's slug, e.g. `dejavu/`.
2. Edit **only the CONFIG section** of the new `index.html` (the comments walk you through every field), plus the `<title>` and meta tags in the `<head>`. Give the hole its own `token` — the emoji and noun readers collect for descending — so the reward cast fits the subject rather than always handing out coffee beans.
3. Follow the depth curve: levels 1–2 the plain answer, 3–4 the mechanism, 5–6 the real machinery, 7–8 the complications, 9 the plot twist, 10 the synthesis plus something the reader can *do*. Give levels 1–9 an optional `fact` each — the bonus nugget shown on the reward card when the reader opens the following level. Put the ready-made `notePanel` widget on level 10.
4. Add a card to the `HOLES` array in the root `index.html` with `status: "live"`. Questions you're planning can sit there as `status: "soon"` teaser cards.
5. Commit, push, and Pages redeploys automatically.

House style, kept deliberately: one signature interactive per hole; checkpoints that make stopping feel legitimate; level titles that are claims, not topics.

## Notes

- The only thing stored is each reader's level progress, privately in **their** browser via `localStorage` (`rh-progress-*` keys) — nothing is sent anywhere and nothing about them is logged. Each page has a "Reset my progress" link in its footer.
- `.nojekyll` matters: without it, GitHub Pages' Jekyll processing silently drops any folder starting with an underscore, including `_template`.
- The pages use `color-mix()` in CSS, supported by all major browsers since 2023.
- Before sharing widely, consider adding an `og:image` (a 1200×630 screenshot) to each page's `<head>` so links preview nicely on social media.
