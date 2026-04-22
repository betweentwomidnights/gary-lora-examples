# Dice button behavior

The **dice** in the gary4juce plugin fills the caption text box with a random prompt. With a LoRA selected, that prompt is biased toward how the LoRA was trained.

## With `captions/`

If your LoRA folder has a populated `captions/` subfolder and you've clicked **Create Captions** in gary4local:

- The dice button pulls from a pool built from **your** training sidecars.
- Each press gives you either a full caption from one of your training clips, or a genre-tag variant derived from one of them (see [sidecar-format.md](sidecar-format.md)).
- The result reflects the stylistic space the LoRA was trained on, not generic ACE-Step examples.

## Without `captions/`

- gary4local reports the LoRA as registered with **0 captions**.
- The plugin detects this and falls back to its built-in default pool — the same prompts it uses when no LoRA is selected.
- The LoRA still loads and runs fine. You just don't get LoRA-flavored dice rolls.

## Rebuilding the pool

Whenever you add or edit sidecars, click **Create Captions** again in gary4local. It re-scans every LoRA folder and rewrites the captions JSON the carey service reads. Then carey reloads.

There's no harm in clicking it repeatedly. It's a ~100ms operation.
