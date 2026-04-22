# metadata.json

`metadata.json` is **fully optional**. If you don't include one, gary4local fills in defaults and infers what it can from the checkpoint folder and the Carey XL toggle.

You only need it when you want to override a default or pin a value (e.g. lock a LoRA to a specific backend).

## Shape

```json
{
  "scale": 1.0,
  "backends": ["base", "turbo"],
  "model_family": "xl"
}
```

## Fields

### `scale`

LoRA scale applied at inference time. Defaults to `1.0` if missing.

### `backends`

Which Carey backends this LoRA is allowed to run on. Defaults to `["base", "turbo"]` if missing.

Accepted tags: `base`, `turbo`, `regular`.

If you set this explicitly and Carey is asked to run the LoRA on an excluded backend, the request is rejected.

### `model_family`

Either `"standard"` or `"xl"`.

If missing, gary4local infers it from the checkpoint path and the current Carey XL toggle in the UI. The inference is good enough for most cases, but pinning it explicitly avoids surprises — Gary4JUCE only shows LoRAs whose `model_family` matches the active backend.

## Where it lives

- **Layout 1 (colocated):** `metadata.json` sits in the same folder as `adapter_config.json` and your sidecars.
- **Layout 2 (separate folders):** `metadata.json` sits in the **checkpoint** folder, alongside the adapter files. It does not go in the captions/source folder.

See the top-level [README](../README.md) for the two layout patterns.
