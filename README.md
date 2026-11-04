# Taskrabbit moving calculator

A self-contained, embeddable quiz that asks up to seven questions about a move, matches the answers to Taskrabbit categories, and gives a rough Tasker-and-hours estimate. No backend, no build step: it is one file, `index.html`.

## Files

- `index.html`: the calculator. Host this.
- `embed-snippet.html`: the code to paste into the blog post.
- `README.md`: this guide.

## Before launch: check these

1. **Category URLs.** All paths live in the `CONFIG.markets` block at the top of the script. Only `/services/help-moving` (US and UK) was confirmed live; the rest are best guesses at your slugs. Open each one and fix any that 404.
2. **Brand styling.** Colours and type follow the Taskrabbit Brand Guidelines (Oct 2023): Agave, Monstera, Pampas, and Lily as the primaries, Teal used once as an accent, and Inter throughout. They live as variables at the top of the `<style>` block if Creative wants tweaks.
3. **Estimate numbers.** Base hours, Tasker counts and packing hours are on each `home_size` option (`est: { hours, taskers, pack }`); stair multipliers are on the `access` options. Tune these against your real task data.

## Hosting

### Option A: GitHub Pages (free, about 10 minutes)

1. Create a GitHub repo, e.g. `taskrabbit-moving-calculator` (public, or private on a paid plan).
2. Upload `index.html` into a folder called `moving-calculator/`.
3. Repo **Settings → Pages**, source: deploy from branch `main`, folder `/ (root)`. Save.
4. After a minute the tool is live at `https://YOUR-ORG.github.io/taskrabbit-moving-calculator/moving-calculator/index.html`.
5. Put that URL in the iframe `src` in `embed-snippet.html`.

For production on the Taskrabbit blog, a Taskrabbit-owned domain is better long term (Option B), but GitHub Pages is fine for a pilot.

### Option B: your own CDN or Contentful

Upload `index.html` as a static asset wherever the web team hosts static files, and use that URL in the iframe `src`. If the blog has a content-security policy, the hosting domain needs to be allowed under `frame-src`.

## Embedding

Paste `embed-snippet.html` into the blog post's HTML or custom code block. The snippet:

- resizes the iframe to fit each step, so there are no scrollbars;
- scrolls the tool back into view when a step changes;
- pushes events to `window.dataLayer` for GTM: `moving_calc_start`, `moving_calc_step_view`, `moving_calc_complete` (with primary match, add-ons and answers), `moving_calc_result_click` (with category and slot), `moving_calc_copy_description` and `moving_calc_restart`.

Result links open in the main window (`target="_top"`) and carry `utm_source=taskrabbit_blog`, `utm_medium=embedded_tool`, `utm_campaign=moving_calculator` and `utm_content=primary_<category>` or `addon_<category>`. Any UTM can be overridden from the iframe URL, e.g. `?market=en-US&utm_campaign=moving_guide_q1`.

## Markets

`?market=en-US` (default) or `?market=en-GB`. The UK version switches to taskrabbit.co.uk and UK naming (Man with a Van, House Removals, Rubbish Removal, flat, van). To add France or Germany, copy the `en-GB` block, set `base` to the local domain, translate category names and blurbs, and add every question and option to `overrides` using `questionId` and `questionId.optionId` keys. The shared `CONFIG.copy` strings also need a per-market version at that point.

## How results work

Results are built around booking one Tasker per job, since most moves on Taskrabbit are booked with one Tasker. The best match shows a time estimate for one Tasker, and every recommended job gets its own Book a Tasker link plus a pre-written task description drafted from the quiz answers. People can edit the description, copy it, and paste it in when they book. Long or heavy moves get a note suggesting a second Tasker, booked separately.

The iframe needs `allow="clipboard-write"` (already in the embed snippet) for the copy button to work everywhere. Copies are tracked as `moving_calc_copy_description`.

A Start Over button appears on every question after the first and on the results screen.

## How matching works

Every answer adds points to one or more categories (the `score` on each option). The top scorer is the best match; up to five more with 3+ points become "You’ll probably also want" (other versions of the same move are never offered twice). Change the weights to change the recommendations.

## SEO note

Search engines largely don’t credit iframe content to the host page, and the tool page is set to `noindex`. Keep the moving guide’s headings, copy and FAQ on the blog post itself.
