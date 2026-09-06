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

The globe icons count what everyone playing has collected between them. `GLOBAL_ID` near
the top of the script decides where those numbers live. Leave it empty and they only count
for one browser.

### Option 1: jsonblob (no account)

1. Go to **jsonblob.com**
2. Delete everything in the box, type `{}`, press **Save**
3. Copy the long code at the end of the address bar
4. Paste it between the quotes in `const GLOBAL_ID='';`

Settings has a **Test connection** button that walks the whole round trip and says exactly
where it fails, if it does. Note that a free public store like this can be read and
overwritten by anyone who views your page source, and jsonblob deletes entries nobody has
touched for 75 days.

### Option 2: your own counter (if option 1 is blocked)

Some free services refuse requests coming from another website, which browsers enforce as
CORS. If the connection test says it could not get a reply at all, that is what happened,
and no change inside the game can fix it. Running your own tiny endpoint will:

1. Make a free account at **cloudflare.com** and open **Workers & Pages**
2. Create a Worker, then create a **KV namespace** and bind it to the Worker as `STATS`
3. Replace the Worker code with this:

```js
export default {
  async fetch(request, env) {
    const cors = {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET,PUT,OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type',
    };
    if (request.method === 'OPTIONS') return new Response(null, { headers: cors });
    if (request.method === 'PUT') {
      await env.STATS.put('stats', await request.text());
      return new Response('{"ok":true}', { headers: { ...cors, 'Content-Type': 'application/json' } });
    }
    const data = (await env.STATS.get('stats')) || '{}';
    return new Response(data, { headers: { ...cors, 'Content-Type': 'application/json' } });
  },
};
```

4. Deploy it and copy the address it gives you
5. Put that whole address in `GLOBAL_ID` instead of a jsonblob code

`GLOBAL_ID` accepts either a jsonblob code or a full address, so nothing else changes.

Inside Claude none of this applies. `window.storage` provides a shared bucket and it is
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
