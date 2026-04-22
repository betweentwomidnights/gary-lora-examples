# captions-only

A folder that contains sidecar `.txt` files but no checkpoint. Useful for:

- Previewing the caption-pool build process without having to train anything.
- Sanity-checking your own sidecar formatting before you move your real training output into place.

gary4local will **not** register this as a loadable LoRA (there's no checkpoint to load). But `build_captions.py` will still process the `captions/` folder and report the pool size, which is often all you need while iterating on caption quality.

## What's here

```
captions-only/
├── README.md
└── captions/
    ├── 01.txt           ← both caption: and genre:
    ├── 02.txt           ← caption: only
    └── 03.txt           ← genre: only (see what expansion does)
```
