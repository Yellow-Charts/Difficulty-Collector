# Difficulty Collector

An idle collecting game. A machine drops difficulties onto the ground, you click them
to collect them, and everything you buy tilts the odds a little further in your favour.

Inspired by [E_E-12345's project on Scratch](https://scratch.mit.edu/projects/864016756).

## Running it

Open `index.html` in a browser. That's the whole thing — no build step, no dependencies,
one self-contained file.

For sound and music, keep the `audio` folder next to `index.html`. If the browser refuses
to read local files, Settings has a picker that loads them by hand for the session. The
game works fine with no audio at all.

To put it on GitHub Pages, drop `index.html` (and `audio/`, if you're including it) in the
repository root or a `docs/` folder and enable Pages in the repository settings.

## Audio and copyright

The music that ships with this is commercial: Daft Punk, Crypt of the NecroDancer,
Rhythm Tengoku and Solkrieg. **Publishing those files in a public repository is
copyright infringement**, and it's the kind that gets repositories taken down rather
than the kind nobody notices.

Three ways round it, roughly in order of how much hassle they are:

1. Add `audio/` to `.gitignore` and let players drop their own files in. Settings already
   has a loader for exactly this, and the track list in `TRACKS` tells them what to find.
2. Replace the tracks with something openly licensed and edit `TRACKS` to match.
3. Keep the repository private.

The sound effects are yours, so those are only a question if you didn't make them.

## Global statistics

The globe icons count what everyone playing has collected between them. Where those
numbers actually live depends on where the page is running:

- **Inside Claude**, `window.storage` provides a shared bucket, and the numbers are
  genuinely shared.
- **Anywhere else, including GitHub Pages**, there is no shared storage, because Pages
  serves static files and nothing else. Without a backend the tally quietly falls back to
  this-browser-only, and the labels say so rather than pretending.

To make them real, set `GLOBAL_ENDPOINT` near the top of the script to a URL that answers
`GET` with the current object and accepts `PUT` of a new one:

```js
const GLOBAL_ENDPOINT = 'https://your-worker.example.workers.dev/stats';
```

The object looks like this:

```json
{
  "total": 12345,
  "diff":   { "Insane": 42 },
  "mod":    { "Golden": 7 },
  "combo":  { "Insane|Golden,Large": 2 },
  "rarest": { "name": "HELL", "mods": ["Golden"], "rarity": 4194304000, "when": 1757000000000 }
}
```

A Cloudflare Worker with a KV namespace does this in about twenty lines and costs nothing
at this traffic. Note that the client does read-modify-write with last-write-wins, so
simultaneous players can lose a count or two — fine for a counter, not fine if you ever
want it to be authoritative. Anything public and writable will eventually be written to
by someone unpleasant, so treat the numbers as decoration and don't put a rate limit past
it and call it secure.

## Editing the game

Everything worth changing sits in a table near the top of the script.

| What | Where | Notes |
|---|---|---|
| Difficulties | `DIFFS` | Name, odds, stage (1–4), colours, centre symbol |
| Modifiers | `MODS` | Odds, size multiplier, colour stop, money multiplier |
| Money upgrades | `UP` | Cost base and growth rate per level |
| Diamond upgrades | `SUP` | Same shape as `UP` |
| Music and sounds | `TRACKS`, `SFX_TIERS` | Filenames inside `audio/` |

Difficulties are drawn as SVG rather than loaded as images, so every modifier applies to
every difficulty automatically. A new one needs a row in `DIFFS` and nothing else.

`s` sets which layer of the background a difficulty lands on: 1 grassland, 2 rocks,
3 city, 4 eye city. The landing heights are generated with the background, so they stay
correct at any window shape.

### A note on balance

The upgrade costs grow fast. Market deal at level 20 costs far more than the highest
difficulty pays, so money plateaus and diamonds become the real progression. If that
feels wrong, `grow` in `UP` is the number to lower.

## Credits

Inspired by E_E-12345 on Scratch. Original project:
<https://scratch.mit.edu/projects/864016756>

You'll want to add a licence file before publishing. Bear in mind you can only licence
the parts you actually own — not the bundled music, and not the logo if someone else
made it.
