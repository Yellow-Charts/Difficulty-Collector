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

The `audio` folder has to be committed alongside `index.html` for sound to work on a
hosted copy. There is deliberately no `.gitignore` excluding it, because excluding it is
the usual reason the music goes silent after publishing.

Be aware of what that means though. The bundled music is commercial: Daft Punk, Crypt of
the NecroDancer, Rhythm Tengoku and Solkrieg. Committing those files to a public
repository is copyright infringement, and it is the kind that gets repositories taken
down rather than the kind nobody notices.

If you would rather not risk it:

- Swap the tracks for something openly licensed and edit the `TRACKS` list to match.
- Or leave the folder out and let players load their own through Settings, accepting that
  it will be silent for everyone else by default.
- Or keep the repository private.

The folder must be named `audio` in lower case and sit next to `index.html`. Hosting is
case sensitive even though your own computer usually is not. **Settings has a "Check audio
files" button** that loads every file and lists any it cannot reach.

## Global statistics

The globe icons count what everyone playing has collected between them. Out of the box
they only count for one browser. To make them shared:

1. Go to **jsonblob.com**
2. Delete everything in the box, type `{}`, press **Save**
3. Copy the long number at the end of the address bar
4. Open `index.html`, find `const GLOBAL_ID='';` near the top of the script, and paste the
   number between the quotes

That's it. Everyone loading your page now shares one counter.

You can also paste the code into Settings inside the game to test it without editing the
file, but that only applies to you — the number has to go in `GLOBAL_ID` for everyone to
share it. Settings also has a **Make me one** button that creates the counter for you, if
your browser lets it read the response header.

Things worth knowing:

- Anyone who views the page source can see the code and could overwrite or wipe the
  numbers. Treat them as decoration, not a leaderboard worth defending.
- jsonblob deletes counters that go 75 days without being touched. If yours stops working
  after a long quiet spell, make a new one.
- Players write in bursts every 30 seconds, and simultaneous saves use last-write-wins, so
  the odd collect can go missing from the total.
- If the counter is unreachable the game carries on and the labels go back to saying the
  numbers are local.

Inside Claude none of this applies — `window.storage` provides a shared bucket and it is
used automatically.

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
