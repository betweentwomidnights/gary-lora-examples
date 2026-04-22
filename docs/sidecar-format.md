# Sidecar format

A "sidecar" is a `.txt` file describing one training clip. They're typically the output of ACE-Step's `understand_music` / 4B LM tagging pass over a folder of audio — one `.txt` per `.wav`. If you trained with Side-Step, you already have these sitting next to your training audio.

`build_captions.py` reads these to populate the **dice button** caption pool for your LoRA. It can read them from the checkpoint folder itself (Layout 1) or from a separate staging folder (Layout 2). See the top-level [README](../README.md).

## What the builder reads

Only two lines in each sidecar matter to the pool builder:

```
caption: A warm ambient drone track, slow-evolving pads, distant bell textures, 60 BPM.
genre: ambient, drone, minimal
```

- `caption:` — long prose. Used as a dice-button entry verbatim.
- `genre:` — short comma-separated tags. See [Genre expansion](#genre-expansion) below.

Either one alone is fine. If both are present, both contribute. If **neither** is present, that sidecar is skipped.

## What the builder does NOT read

These can be in your sidecar (and usually are, if `understand_music` produced them) but they don't end up in `captions.json`:

- `custom_tag:` — the trigger word from training. **This is the important one.** See [Trigger words and the pool](#trigger-words-and-the-pool) below.
- `lyrics:`
- `bpm:`, `key:`, `signature:`, `is_instrumental:`

The BPM, key, duration and lyrics are set by the user in the DAW at generation time — they're not read here.

## Full example

This is the shape `understand_music` produces. Everything past the first two lines is harmless to leave in:

```
caption: A warm ambient drone track, slow-evolving pads, distant bell textures, 60 BPM.
genre: ambient, drone, minimal
bpm: 60
key: D minor
signature: 4
is_instrumental: true
custom_tag: longform
lyrics:
[Instrumental]
```

## Genre expansion

A `genre:` line is not just used whole. It's split into tokens and expanded into 2-token and 3-token subsets so the dice button gives you re-rolls that feel stylistically close but not identical.

For `genre: glitch hop, neurobass, cinematic`, the pool gets:

- `glitch hop, neurobass, cinematic` (the full line)
- all 2-token subsets: `glitch hop, neurobass`, `glitch hop, cinematic`, `neurobass, cinematic`
- all 3-token subsets (in this case, same as the full line)

Subsets only mix tokens from **the same line**. Tokens from `track01.txt`'s genre line never cross-pollinate with `track02.txt`'s. That keeps the pool coherent.

Duplicates across all sidecars are deduped at the end, so a common `ambient, drone` tag that appears on twenty tracks only shows up once in the pool.

## Trigger words and the pool

Worth being explicit about because it catches people out.

If you trained with Side-Step's `custom_tag` (e.g. `patch`), that token is what the LoRA learned to associate with your training distribution. But because `build_captions.py` does not read `custom_tag:` lines, your trigger word will **not** automatically show up in the dice-button pool.

That's by design and matches how we actually work: the trigger word lives in `custom_tag:`, the dice pool is built from descriptive `caption:` and `genre:` text, and you type the trigger word into your prompt yourself when you want a stronger pull on the LoRA. The dice button is for stylistic variety; the trigger word is a separate dial.

If you specifically want dice rolls to *include* the trigger token (some users prefer this), put it into the sidecar `caption:` or `genre:` text yourself. Example:

```
caption: patch, a slow, evolving ambient drone with warm analog pads.
genre: patch, ambient, drone
```

Both styles are fine. The default sidecars `understand_music` produces follow the first pattern (trigger in `custom_tag:` only), which is what you'll see in [`examples/colocated/koan/`](../examples/colocated/koan/).

## Naming

Sidecar filenames don't matter — only the `.txt` extension is scanned. Pair them with your audio filenames (`track01.wav` + `track01.txt`) so your own life is easier.

Files ending in `.v4bak` (a historical artifact of some training runs) are skipped.

## Caption-writing tips

- Lean on arrangement, instrumentation, groove, and production character if that's what you trained on. Overloading every example with vocal language pulls generations toward vocals you may not want.
- Users can always tack on small prompt nudges like `male vocals` or `female vocals` later when they want lyrics-driven generations on top of a mostly instrumental caption — they don't need to be baked into every sidecar.
