# separate-folders — Layout 2

The two-folder pattern: an exported adapter folder in one place, the original training sidecars left where they were. This is the workflow we actually used for `john`, `koan`, and `third_eye` in our remote registry.

```
separate-folders/
├── README.md                        ← you are here
├── john-xlbase-v1-best-ep56/        ← the exported checkpoint
│   └── metadata.json                ← optional
└── john/                            ← the original training sidecar folder
    ├── sample-01.txt
    ├── sample-02.txt
    └── sample-03.txt
```

The actual checkpoint files (`adapter_config.json`, `adapter_model.safetensors`) are not in this repo — we don't distribute adapters. In a real folder they'd sit inside `john-xlbase-v1-best-ep56/` next to `metadata.json`.

## Pointing gary4local at it

In Carey's **add lora** UI:

- **Name:** `john`
- **Model family:** `xl`
- **Checkpoint folder:** `<wherever>/john-xlbase-v1-best-ep56/`
- **Captions / source folder:** `<wherever>/john/`

`build_captions.py` will scan the captions folder you pointed at and ignore the checkpoint folder for `.txt` files.

## Why this layout

When you train with Side-Step, you end up with:

- a folder of audio + sidecars (your dataset, e.g. `john/`)
- a separate trained adapter export, often versioned by epoch (e.g. `john-xlbase-v1-best-ep56/`)

You don't have to copy anything. Both folders stay where they are; gary4local just stores the two paths.

## metadata.json placement

`metadata.json` belongs in the **checkpoint** folder, alongside the adapter files — not in the captions folder. See [docs/metadata.md](../../../docs/metadata.md).

## Sidecar filenames

Sidecar filenames don't matter to the pool builder — only the `.txt` extension is scanned. The files here are named `sample-NN.txt` for example purposes; in a real training folder you'd typically pair them with your audio filenames (`<title>.wav` + `<title>.txt`) to keep things tidy. Spaces and punctuation in filenames are fine.
