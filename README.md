# gary-lora-examples

Folder format and examples for loading your own ACE-Step LoRAs into [gary4local](https://github.com/betweentwomidnights/gary-localhost-installer).

This repo is a **spec**, not a collection of pretrained adapters. No checkpoints are distributed here. You train your own LoRA (or obtain one legitimately), arrange it in one of the layouts below, and point gary4local at it.

## How gary4local sees a LoRA

Carey's **add lora** UI takes two folder paths:

1. **Checkpoint folder** (required) — where `adapter_config.json` and `adapter_model.safetensors` live.
2. **Captions / source folder** (optional) — where the `.txt` sidecars from your training run live. If left blank, the checkpoint folder itself is scanned for `.txt` files.

Plus a few small fields on the entry:

- a lowercase **name** (becomes the dropdown entry in the plugin)
- a **model family** (`standard` or `xl`) — Gary4JUCE only sees LoRAs whose family matches the current Carey XL toggle
- the model family also gets baked into `metadata.json` if you want it captured per-LoRA

That's the whole contract. Everything below is just two ways to lay the files out on disk.

## Layout 1 — colocated (sidecars next to the checkpoint)

```text
koan/
├── adapter_config.json
├── adapter_model.safetensors
├── metadata.json            ← optional
├── sample-01.txt            ← sidecars sit right next to the adapter
├── sample-02.txt
└── sample-03.txt
```

In **add lora**, point the checkpoint field at `koan/` and leave the captions field blank. `build_captions.py` will scan the same folder for sidecars.

This is the simplest layout and is what we recommend if you're starting fresh.

See [`examples/colocated/koan/`](examples/colocated/koan/).

## Layout 2 — separate checkpoint and staging folder

```text
john-xlbase-v1-best-ep56/    ← checkpoint export
├── adapter_config.json
├── adapter_model.safetensors
└── metadata.json

john/                        ← original training sidecars, kept where they were
├── John Mayer - 3x5.txt
├── John Mayer - 83.txt
└── John Mayer - Back To You.txt
```

In **add lora**, point the checkpoint field at `john-xlbase-v1-best-ep56/` and the captions field at `john/`.

This is the workflow we actually used in practice — exported adapter in one place, original training sidecars in another. Nothing has to move.

See [`examples/separate-folders/`](examples/separate-folders/).

## What's optional

- **`metadata.json`** — fully optional. If missing, defaults are `scale: 1.0`, `backends: ["base", "turbo"]`, and `model_family` is inferred from the checkpoint path and current Carey XL toggle. Set it explicitly if you care. Schema: [docs/metadata.md](docs/metadata.md).
- **Captions / source folder** — optional. Without sidecars, the LoRA still loads and runs; the plugin's dice button just falls back to its built-in default pool. See [docs/dice-button.md](docs/dice-button.md).
- **`genre:` line** in sidecars — optional. So is `caption:`. At least one of the two has to be present for that sidecar to contribute to the pool. See [docs/sidecar-format.md](docs/sidecar-format.md).

## Quickstart

1. Train a LoRA. We use the [Side-Step](docs/training.md) flow on top of ACE-Step. See [docs/training.md](docs/training.md) for pointers.
2. Lay out your files using either Layout 1 or Layout 2 above.
3. In gary4local, open Carey's **add lora** UI. Pick a name, set the model family, point at the checkpoint folder, and (optionally) the captions folder.
4. Click **Create Captions** to build the pool. The LoRA now appears in the plugin's dropdown and the dice button is biased toward your training distribution.

## Docs

- [Sidecar format](docs/sidecar-format.md) — how the dice button captions are derived from your training txts (and what `build_captions.py` actually reads)
- [metadata.json](docs/metadata.md) — the full shape, with `model_family`
- [Dice button behavior](docs/dice-button.md) — what sidecars actually unlock in the plugin
- [Training](docs/training.md) — Side-Step pointers; we don't bundle a training pipeline
- [Legal](LEGAL.md) — read this before you point a LoRA folder at anything

## Examples

- [`examples/colocated/koan/`](examples/colocated/koan/) — Layout 1, sidecars sitting next to the (omitted) checkpoint
- [`examples/separate-folders/`](examples/separate-folders/) — Layout 2, exported adapter folder + original training sidecar folder
- [`examples/captions-only/`](examples/captions-only/) — sidecar txts with no checkpoint, useful for previewing the caption-pool build

## Links

- gary4juce (the JUCE plugin — where the LoRA dropdown and dice button actually live): https://github.com/betweentwomidnights/gary4juce
- gary4local (the local backend that consumes this folder format, so you don't have to use our hosted backend): https://github.com/betweentwomidnights/gary-localhost-installer
- ACE-Step (the upstream model): https://github.com/ace-step/ACE-Step
- Side-Step (the training fork we use for `custom_tag` / `genre_ratio`): https://github.com/koda-dernet/Side-Step (see [docs/training.md](docs/training.md))

If you don't want to train your own LoRA, all of the LoRAs in our remote backend registry (`koan`, `john`, `third_eye`, …) are free to use from gary4juce against the hosted backend. This repo is for the case where you *do* want to train your own and load it locally.
