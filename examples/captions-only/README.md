# captions-only

A folder that contains sidecar `.txt` files but no checkpoint. Useful for:

- Previewing the caption-pool build process without having to train anything.
- Sanity-checking your own sidecar formatting before you move your real training output into place.
- Demoing what `caption:` only and `genre:` only sidecars do under the hood (the pool builder accepts either).

gary4local will **not** register this as a loadable LoRA — there's no checkpoint to load. But you can point a throwaway entry's captions field at this folder and watch the pool build to confirm your sidecar shape parses cleanly.

## What's here

```
captions-only/
├── README.md
└── captions/
    ├── 01.txt           ← both caption: and genre:
    ├── 02.txt           ← caption: only
    └── 03.txt           ← genre: only (see what expansion does)
```

See [docs/sidecar-format.md](../../docs/sidecar-format.md) for what each line does and how `genre:` expansion produces 2-token and 3-token subsets in the pool.
