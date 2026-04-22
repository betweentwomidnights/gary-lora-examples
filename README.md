# gary-lora-examples

Folder format and examples for loading your own ACE-Step LoRAs into [gary4local](https://github.com/betweentwomidnights/gary-localhost-installer).

This repo is a **spec**, not a collection of pretrained adapters. No checkpoints are distributed here. You train your own LoRA (or obtain one legitimately), arrange it in the folder layout below, and point gary4local at it.

## The layout

```
my-loras/                       ← point gary4local at this directory
├── koan/                       ← LoRA name — becomes the dropdown entry in the plugin
│   ├── adapter_config.json     ← your trained checkpoint files
│   ├── adapter_model.safetensors
│   ├── captions/               ← optional; enables the dice button for this LoRA
│   │   ├── track01.txt
│   │   └── track02.txt
│   └── metadata.json           ← optional; overrides scale / backends
├── mayer/
│   ├── adapter_config.json
│   ├── adapter_model.safetensors
│   └── captions/
│       └── ...
└── <your-lora>/
    └── ...
```

One rule: the LoRA folder name becomes the name in the plugin's LoRA dropdown. Use lowercase letters, digits, hyphens, underscores. Everything else (`captions/`, `metadata.json`) is optional.

## Quickstart

1. Train a LoRA with [ACE-Step](https://github.com/ace-step/ACE-Step). See [docs/training.md](docs/training.md) for pointers.
2. Drop the trained checkpoint files into `<lora_name>/` at the root of your LoRA folder.
3. (Optional) Put your training sidecar `.txt` files into `<lora_name>/captions/`. Format: [docs/sidecar-format.md](docs/sidecar-format.md).
4. Open gary4local → carey model panel → **Folder** button → pick your LoRA folder.
5. Click **Create Captions** to build the pool. The LoRA now appears in the plugin's dropdown.

If a LoRA has no `captions/`, it still works — the plugin's dice button falls back to its built-in default caption pool.

## Docs

- [Sidecar format](docs/sidecar-format.md) — how the dice button captions are derived from your training txts
- [Dice button behavior](docs/dice-button.md) — what `captions/` actually unlocks in the plugin
- [Training](docs/training.md) — pointers; we don't bundle a training pipeline
- [Legal](LEGAL.md) — read this before you point a LoRA folder at anything

## Examples

- [`examples/stock-demo/`](examples/stock-demo/) — a full populated LoRA folder (checkpoint omitted; captions + metadata included)
- [`examples/captions-only/`](examples/captions-only/) — sidecar txts with no checkpoint, useful for previewing the caption-pool build

## Links

- gary4local (the Tauri app that consumes this format): https://github.com/betweentwomidnights/gary-localhost-installer
- ACE-Step (the upstream model): https://github.com/ace-step/ACE-Step
