# Gemma 4 12B-it NVFP4 (W4A16) + MTP — by @Alex12571333

Tested on DGX Spark GB10.

| Metric | Value |
|---|---|
| Baseline decode (no spec) | ~25 tok/s |
| With MTP K=5 | **~44 tok/s** |
| Mean acceptance length | 2.86–3.32 |
| Avg draft acceptance | ~40% |
| Disk size | 7.7 GB |
| Multimodal | text + image + audio + video ✅ |
| Context | 128K |

## Why W4A16 and not W4A4

Pure NVFP4 (W4A4 = both weights and activations 4-bit) breaks the multimodal capability of Gemma 4 because image/audio embeddings go out-of-distribution under text-only activation calibration. Weight-only NVFP4 (W4A16) keeps all modalities working and is actually slightly faster on bandwidth-bound dense models.

## Stack

- Container: `vllm/vllm-openai:gemma4-unified`
- Model: [`coolthor/gemma-4-12B-it-NVFP4A16`](https://huggingface.co/coolthor/gemma-4-12B-it-NVFP4A16)
- Drafter: `google/gemma-4-12b-it-assistant`
