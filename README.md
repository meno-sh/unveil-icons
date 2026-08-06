# menoclaw-icons

Per-agent Slack avatars for [MenoClaw](https://github.com/meno-sh/Agent-Harnessing),
generated from the MenoClaw mark.

## Why this repository is public

It holds nothing but PNGs, and it is public for one mechanical reason: Slack's
`chat.postMessage` takes an `icon_url`, and **Slack fetches that URL from its own
servers**. A private repository returns 404 to Slack, so the images would never
render.

That is a hard constraint rather than a preference. The alternatives were
tested and none reach the avatar slot:

- a `data:` URI — `icon_url` is capped at 255 characters;
- `slack_file` — works in Block Kit image blocks, but cannot set a message avatar;
- a custom emoji via `admin.emoji.add` — Enterprise-plan only, needs a user
  token, and takes a `url` anyway, so it does not avoid hosting either.

No credentials, no source, and nothing about the harness lives here.

## What the images are

The MenoClaw mark is a square with an inscribed diamond — a *construction*
rather than an illustration, so it generates. Each avatar lights some subset of
three concentric diamonds inside the mark:

| axis | values |
| --- | --- |
| hue | 18, spaced 20° from the brand navy (217°) |
| polarity | 2 — mark reversed out of a saturated plate, or drawn on a tinted one |
| ring pattern | 7 (every non-empty subset of three rings) |

**252 images**, 128×128 PNG. The set is fixed; it does not grow with the fleet.

Both polarities carry a coloured plate on purpose. Nothing is ever pure white,
so an avatar is never a glaring block on Slack's dark theme nor a hole in its
light one.

## Naming

```
v1/h<hue>-<d|l>-p<pattern>.png     e.g. v1/h07-d-p13.png
```

A session's avatar is chosen by `sha256(cc_uuid)`, so it is the same picture
forever, on every host, across restarts.

**The `v1/` prefix is load-bearing.** Slack caches an avatar by its source URL
and does not re-fetch a URL it has already seen — verified against the live API.
New artwork published at an old path would therefore stay invisible forever, so
changing the art means bumping the prefix.

## Regenerating

These are build output. The generator is
[`tools/gen_avatars.py`](https://github.com/meno-sh/Agent-Harnessing/blob/main/tools/gen_avatars.py)
in the harness repository; the seed→image mapping lives beside the runtime in
`menoclaw_mcp/slack/avatars.py` so the two cannot disagree.

```
python3 tools/gen_avatars.py --out <dir>
```
