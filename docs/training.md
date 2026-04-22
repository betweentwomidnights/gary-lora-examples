# Training

We don't bundle a training pipeline. Use ACE-Step's upstream training workflow:

- ACE-Step repo: https://github.com/ace-step/ACE-Step
- LoRA training is covered in their docs under the training/finetuning sections.

## Producing sidecars

The `.txt` files the dice button reads are typically the output of ACE-Step's `understand_music` step — a tagging pass that runs a small LM over each audio file and emits one `.txt` per clip with `caption:`, `genre:`, `bpm:`, `key:`, and friends.

If you trained using ACE-Step's pipeline end-to-end, you probably already have these sitting next to your training wavs. Copy them into `<lora_name>/captions/` and you're done.

If you trained without sidecars (e.g. you wrote your own captions by hand into the dataset manifest), you can still produce sidecars after the fact by running `understand_music` on your training folder, or by writing `.txt` files yourself following the format in [sidecar-format.md](sidecar-format.md).

## Compatibility

LoRAs trained on `acestep-v15-base` load cleanly on both xl-base and xl-turbo in carey. That's what the `backends: ["base", "turbo"]` default in `metadata.json` reflects.

If your adapter only works on one of the two, set `backends` explicitly in `metadata.json` — carey will reject requests that ask for it on the wrong backend.
