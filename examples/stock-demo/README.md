# stock-demo

A fully populated example LoRA folder, minus the actual checkpoint (since we don't distribute adapters from this repo).

## What's here

```
stock-demo/
├── README.md           ← you are here
├── captions/           ← five example sidecars
│   ├── 01.txt
│   ├── 02.txt
│   ├── 03.txt
│   ├── 04.txt
│   └── 05.txt
└── metadata.json       ← optional config
```

## What's missing

Your own trained checkpoint files. For a real LoRA folder, you'd also have:

```
stock-demo/
├── adapter_config.json
├── adapter_model.safetensors
└── ... (whatever your training run produced)
```

Drop those in at the root of the folder (as siblings to `captions/` and `metadata.json`), rename `stock-demo/` to whatever you want the LoRA to be called in the dropdown, and you're done.

## metadata.json

The included `metadata.json` is the exact shape the pool builder expects. You can delete it entirely and gary4local will use the defaults (`scale: 1.0`, `backends: ["base", "turbo"]`).
