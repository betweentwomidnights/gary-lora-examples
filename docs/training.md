# Training

We don't bundle a training pipeline. For our own LoRAs (`koan`, `john`, `third_eye`) we use the **Side-Step** training flow built on top of ACE-Step.

- ACE-Step (the upstream model): https://github.com/ace-step/ACE-Step
- Side-Step (the training fork we actually use): https://github.com/koda-dernet/Side-Step

If you're new to LoRA training on ACE-Step, start with Side-Step's README and `sidestep_documentation/Dataset Preparation.md` — that's the path of least resistance for ending up with files that drop straight into one of the layouts described in the top-level [README](../README.md).

## Side-Step config knobs that matter for the dice button

A few Side-Step options shape what the LoRA actually learned, and therefore what kinds of sidecars you should be writing.

### `custom_tag` and `tag_position`

`custom_tag` is the trigger word for the LoRA. During preprocessing Side-Step inserts it into the training prompt according to `tag_position`:

- `prepend` (default) — `koan, <caption>`
- `append` — `<caption>, koan`
- `replace` — the trigger word replaces the caption entirely

`custom_tag` can be set once at the dataset level (in the dataset JSON's `metadata` block) and Side-Step uses it as a fallback for any sample that doesn't define its own. ACE-Step's Dataset Builder typically copies it to every sample anyway.

The model learns to associate that token with your training distribution. At inference time, including the trigger word in your prompt tends to push generations harder toward what you trained.

**Note on the dice pool:** `build_captions.py` does **not** read `custom_tag:` lines from sidecars, so the trigger word doesn't end up in the dice pool. That's how we use it in practice — the trigger word lives in `custom_tag:`, the dice button rolls descriptive captions, and you type the trigger word into your prompt yourself when you want a stronger pull on the LoRA. See [sidecar-format.md](sidecar-format.md#trigger-words-and-the-pool) if you'd rather bake the trigger token into the pool itself.

### `genre_ratio`

`genre_ratio` is an integer percentage (0–100) set in the dataset JSON's `metadata` block. It controls what fraction of training samples use the `genre` field as the text prompt instead of the full `caption`.

- Default is `0` — every sample trains on its full caption.
- `90` (the value Side-Step's docs reach for first, and the value we used) means about 90% of samples train on `genre` and 10% on `caption`. The selection is random but seeded for reproducibility.
- A per-sample `prompt_override: "caption"` or `"genre"` field overrides `genre_ratio` for that sample.

Why we use it: the model sees both richer descriptive prompts (full captions) and concise stylistic prompts (genre tokens) during training. That maps cleanly onto our dice button, which exposes both the verbatim `caption:` strings and the shorter `genre:` subsets via the pool builder.

**Practical recipe:**

1. In your dataset JSON, fill in **both** `caption` and `genre` per sample. If `genre` is empty, `genre_ratio` has nothing to use for that sample.
2. Set `metadata.genre_ratio` to something like `90` for a strong style-token bias, or `50` for a more even mix.
3. After training, the same per-sample `caption` and `genre` strings show up in your `.txt` sidecars, so `build_captions.py` reflects the same distribution the model trained on.

`genre` is itself an optional field — a sidecar (or training sample) with only `caption:` is valid. But if you want genre rerolls to feel rich, include genres both in training and in the sidecars.

See `sidestep_documentation/Dataset Preparation.md` in the [Side-Step repo](https://github.com/koda-dernet/Side-Step) for the full dataset JSON schema.

## Producing sidecars after training

If you trained with Side-Step end-to-end, you already have `.txt` sidecars sitting next to your training audio. The handoff into gary4local is:

- **Layout 1 (colocated):** copy the sidecars next to your exported adapter files.
- **Layout 2 (separate folders):** leave the sidecars where they are — point the **captions / source folder** field at the original folder.

If you trained without sidecars (e.g. you wrote captions by hand into the dataset JSON only), you can produce sidecars after the fact by running ACE-Step's `understand_music` over your training folder, or by writing `.txt` files yourself following the format in [sidecar-format.md](sidecar-format.md).

## Backend compatibility

LoRAs trained on `acestep-v15-base` load cleanly on both xl-base and xl-turbo in carey. That's what the `backends: ["base", "turbo"]` default in `metadata.json` reflects.

If your adapter only works on one of the two, set `backends` explicitly in `metadata.json` — carey will reject requests that ask for it on the wrong backend. See [metadata.md](metadata.md).
