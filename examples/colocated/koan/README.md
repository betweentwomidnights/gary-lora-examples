# koan — Layout 1 (colocated)

Everything for one LoRA in a single folder: adapter files, sidecars, and (optional) `metadata.json`. This is the simplest layout.

```
koan/
├── README.md            ← you are here
├── sample-01.txt        ← real sidecars produced by understand_music, renamed for the example
├── sample-02.txt
├── sample-03.txt
├── sample-04.txt
├── sample-05.txt
└── metadata.json        ← optional
```

The actual checkpoint files (`adapter_config.json`, `adapter_model.safetensors`, anything else your training run produced) are not in this repo — we don't distribute adapters. In a real LoRA folder they'd sit alongside everything above:

```
koan/
├── adapter_config.json          ← from your training run
├── adapter_model.safetensors    ← from your training run
├── sample-01.txt
├── ...
└── metadata.json
```

Sidecar filenames don't matter to the pool builder — only the `.txt` extension is scanned. Pair them with your audio filenames in your real folder if it makes your life easier.

## Pointing gary4local at it

In Carey's **add lora** UI:

- **Name:** `koan`
- **Model family:** `xl`
- **Checkpoint folder:** `<wherever>/koan/`
- **Captions / source folder:** *leave blank*

Because the captions field is blank, `build_captions.py` scans the checkpoint folder itself and picks up the five `.txt` files sitting next to the adapter.

## What real `understand_music` sidecars look like

These five sidecars are copies of actual training data — what `understand_music` produced over Koan Sound tracks during the real run. Two things to notice about them:

- **The `caption:` field is rich and unique per track**, describing arrangement, instrumentation, and structure. That's exactly what you want feeding the dice button.
- **The `genre:` field is the same on every sidecar** (`glitch hop, neurofunk, electronica, intricate breakbeat`). This is intentional — the LoRA was trained on a stylistically coherent set of tracks, so a fixed genre line keeps the genre-expansion subsets focused on the actual training distribution rather than scattering across unrelated tags.

That's a legitimate authoring move: write captions per-track, but pin the genre line to the LoRA's overall style.

## What about the trigger word?

The trigger word for this LoRA is `patch` — that's what's in `custom_tag:` on every sidecar. It does **not** appear in `caption:` or `genre:`, and `build_captions.py` doesn't read `custom_tag:`, so the trigger word will not show up in the dice-button pool.

That's fine. You type the trigger word into your prompt yourself when you want a stronger pull on the LoRA. The dice button is for stylistic variety; the trigger word is a separate dial. See [docs/sidecar-format.md](../../../docs/sidecar-format.md#trigger-words-and-the-pool).

## metadata.json

The included `metadata.json` is the explicit shape gary4local expects. You can delete it entirely — the defaults (`scale: 1.0`, `backends: ["base", "turbo"]`, and inferred `model_family`) cover most cases. See [docs/metadata.md](../../../docs/metadata.md).
