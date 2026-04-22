# Sidecar format

A "sidecar" is a `.txt` file describing one training clip. They're the output of ACE-Step's `understand_music` / 4B LM tagging pass over a folder of audio — one `.txt` per `.wav`.

gary4local reads these to populate the **dice button** caption pool for your LoRA.

## Minimum

Only two lines matter:

```
caption: A warm ambient drone track, slow-evolving pads, distant bell textures, 60 BPM.
genre: ambient, drone, minimal
```

`caption:` — long prose, used as a dice-button entry verbatim.
`genre:` — short comma-separated tags. See "Genre expansion" below for what the pool builder does with these.

Either one alone is fine. If both are present, both contribute. If neither is present, that sidecar is skipped.

## Full example

This is the shape of what ACE-Step's tagger produces — everything after the two lines above is ignored by the pool builder, but there's no reason to strip it:

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

The BPM, key, duration and lyrics are set by the user in the DAW at generation time — they're not read here.

## Genre expansion

A `genre:` line is not just used whole. It's split into tokens and expanded into subsets so the dice button gives you re-rolls that feel stylistically close but not identical.

For `genre: glitch hop, neurobass, cinematic`, the pool gets:

- `glitch hop, neurobass, cinematic` (the full line)
- all 2-token subsets: `glitch hop, neurobass`, `glitch hop, cinematic`, `neurobass, cinematic`
- all 3-token subsets (in this case, same as the full line)

Subsets only mix tokens from **the same line**. Tokens from `track01.txt`'s genre line never cross-pollinate with `track02.txt`'s. That keeps the pool coherent.

Duplicates across all sidecars are deduped at the end, so a common `ambient, drone` tag that appears on twenty tracks only shows up once in the pool.

## Naming

Sidecar filenames don't matter — only the `.txt` extension is scanned. Pair them with your audio filenames (`track01.wav` + `track01.txt`) so your own life is easier.

Files ending in `.v4bak` (a historical artifact of some training runs) are skipped.
