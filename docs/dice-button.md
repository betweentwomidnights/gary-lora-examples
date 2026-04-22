# Dice button behavior

The **dice** in the gary4juce plugin fills the caption text box with a random prompt. With a LoRA selected, that prompt is biased toward how the LoRA was trained.

## With sidecars

If your LoRA has sidecars (either next to the adapter in Layout 1, or in the separate captions/source folder in Layout 2) and you've clicked **Create Captions** in gary4local:

- The dice button pulls from a pool built from **your** training sidecars.
- Each press gives you either a full caption from one of your training clips, or a genre-tag variant derived from one of them. See [sidecar-format.md](sidecar-format.md).
- The result reflects the stylistic space the LoRA was trained on, not generic ACE-Step examples.

## Without sidecars

The captions / source folder in **add lora** is optional. If you leave it blank and your checkpoint folder also contains no `.txt` files:

- gary4local registers the LoRA with **0 captions**.
- The plugin detects this and falls back to its built-in default pool — the same prompts it uses when no LoRA is selected.
- The LoRA still loads and runs fine. You just don't get LoRA-flavored dice rolls.

## Trigger words and the dice pool

If you trained with a Side-Step `custom_tag` (e.g. `patch`), that trigger word lives in `custom_tag:` on each sidecar and is **not** added to the dice-button pool — `build_captions.py` reads `caption:` and `genre:` only. The dice button gives you stylistic variety; you type the trigger word into the prompt yourself when you want it. Both pieces work together at inference time. See [sidecar-format.md](sidecar-format.md#trigger-words-and-the-pool) if you'd rather bake the trigger token into the pool itself.

## Rebuilding the pool

Whenever you add or edit sidecars, click **Create Captions** again in gary4local. It re-scans every registered LoRA's source folder and rewrites the captions JSON the carey service reads. Then carey reloads.

There's no harm in clicking it repeatedly. It's a ~100ms operation.
