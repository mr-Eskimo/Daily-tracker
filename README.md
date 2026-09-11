# 112 Days

A daily dashboard covering fitness, diet, focus, craft and reading for the 112 days from 12 Sep 2026 to 1 Jan 2027.

## Files

| File | What it does |
|---|---|
| `index.html` | The whole app — markup, styles, plan data and logic in one file |
| `manifest.json` | Lets it install to your phone home screen as an app |
| `sw.js` | Service worker — makes it work offline |
| `icon-192.png`, `icon-512.png` | App icons |

No build step, no dependencies, no server code. Open `index.html` and it runs.

## Getting it online

### Netlify Drop — about a minute, no account needed to start

1. Go to https://app.netlify.com/drop
2. Drag this whole folder onto the page
3. You get a live URL immediately

Make a free account afterwards if you want to keep the URL and rename it to something memorable.

### GitHub Pages — better if you want to keep editing

1. Create a new repository on GitHub (public is fine, or private on a paid plan)
2. Upload these files to the root of the repo
3. Repo → **Settings** → **Pages** → under "Build and deployment", set Source to *Deploy from a branch*, branch `main`, folder `/ (root)`
4. Save. Your site appears at `https://<username>.github.io/<repo-name>/` within a couple of minutes

Every push to `main` redeploys automatically. This is the option I'd pick given you want to keep improving it — you get version history, and you can edit `index.html` directly in GitHub's web editor from your phone if an idea hits you on the commute.

## Installing it on your phone

Open your live URL in the phone browser, then:

- **Android / Chrome** — menu → *Add to Home screen*
- **iPhone / Safari** — Share → *Add to Home Screen*

It then opens full screen with no browser chrome, and works offline.

## How your progress is stored

Progress lives in `localStorage` — on the device, in that browser. Nothing is sent anywhere, which is why there's no login and nothing to pay for.

The trade-off: **your phone and laptop keep separate progress.** Two things follow from that.

1. Pick one device as the one you actually tick things off on. Probably your phone.
2. Use **Export progress** every week or two and keep the JSON file somewhere safe. Import it on another device to merge — the merge is additive, so nothing already ticked gets wiped.

Progress is also cleared if you clear site data for that domain, or if your browser evicts storage after long disuse. The export file is your backup.

If you'd rather have real sync across devices, that needs a small backend — Supabase's free tier is the usual answer, roughly an hour of work to wire up.

## Editing it

Everything is in `index.html`. The parts you'll most likely want to change:

| What | Where to look |
|---|---|
| Recipes | `var R = {` — each entry has a name, time, ingredients (`i`) and method (`s`) |
| Which meal falls on which weekday | `var MEALS = {` — `0` is Sunday through `6` is Saturday |
| Running progression | `var RUN = [` — one entry per week, `s` is the short run, `l` is the long one |
| Dumbbell exercises | `var LIFTS = {` |
| Exercise video links | `var VID = {` — a YouTube search phrase per exercise; `RUNVIDS` and `RESTVIDS` cover running and rest days |
| Pinning a specific video | `var PINS = {}` — see below |
| Phase names and monthly focus | `var PHASES = [` |
| Weekly professional / mind tasks | `var PRO_WEEK`, `var MIND_WEEK` |
| Daily timings | `var T =` inside `buildDay` — separate weekday and weekend schedules |
| Colours | the `:root` block at the top of the CSS |

Days are generated from these tables rather than written out one by one, so changing a run distance or a recipe updates every day it appears.

**After you edit and redeploy:** bump `CACHE = 'plan112-v2'` to `v3` in `sw.js`. Otherwise phones that installed it may keep serving the old cached version.

## The exercise videos

Every exercise, plus running form and stretches, has a **watch** link beside it. These open a YouTube *search* rather than one specific video. That's deliberate: individual videos get deleted, made private or re-uploaded, and a dead link on a Tuesday morning is worse than no link. A search always returns something current.

Once you find a demonstration you actually trust, pin it so the link goes straight there. Copy the ID from the YouTube URL — the part after `v=`:

```
https://www.youtube.com/watch?v=dQw4w9WgXcQ
                                ^^^^^^^^^^^ this bit
```

Then add it to the `PINS` table in `index.html`:

```js
var PINS = {
  'Goblet squat': 'dQw4w9WgXcQ',
  'Romanian deadlift': 'someOtherId'
};
```

The name on the left must match the exercise name exactly as it appears in `LIFTS`. Anything you haven't pinned keeps using search, so you can build the list up gradually over a few weeks rather than all at once.

## One health note

The run distances, meal sizes and the 10 kg target are a starting template, not a prescription. Adjust them to how your body actually responds. The alcohol side in particular is worth doing with a doctor's input rather than alone — withdrawal can be medically serious, not just uncomfortable.
