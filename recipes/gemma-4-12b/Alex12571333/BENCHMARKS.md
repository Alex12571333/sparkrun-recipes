# Benchmarks — gemma-4-12b-it-nvfp4-w4a16-mtp

**Hardware:** NVIDIA DGX Spark (GB10, SM 12.1, 121 GiB unified memory)
**vLLM:** `vllm/vllm-openai:gemma4-unified`
**Profile:** `spark-arena-v2` via `llama-benchy 0.3.7`
**Status:** *partial — full benchmark in progress*

## Single-Spark, single-node, TP=1

All numbers are **aggregate** throughput across N concurrent users.
Per-user throughput = `gen t/s ÷ concurrency`.

### Prefill throughput (input tokens/sec)

| Depth (input tokens) | c=1 | c=2 | c=5 | c=10 |
|---:|---:|---:|---:|---:|
| 4,096 | — | 1,567.0 | 1,757.3 | — |
| 8,192 | — | 1,508.4 | 1,600.5 | 1,247.7 |
| 16,384 | — | — | — | — |
| 32,768 | — | — | — | — |
| 65,535 | — | — | — | — |
| 100,000 | — | 460.9 | 80.4 | — |

### Generation throughput (aggregate tokens/sec)

| Depth | c=1 | c=2 | c=5 | c=10 |
|---:|---:|---:|---:|---:|
| 4,096 | — | **55.3** | **84.1** | — |
| 8,192 | — | **56.9** | **82.0** | **70.0** |
| 100,000 | — | 23.6 | 3.9 | — |

### Generation throughput (per-user)

| Depth | c=1 | c=2 | c=5 | c=10 |
|---:|---:|---:|---:|---:|
| 4,096 | — | 27.7 | 16.8 | — |
| 8,192 | — | 28.5 | 16.4 | 7.0 |
| 100,000 | — | 11.8 | 0.8 | — |

### Time To First Response (TTFR, ms)

| Depth | c=2 | c=5 | c=10 |
|---:|---:|---:|---:|
| 4,096 | 2,621 | 4,653 | — |
| 8,192 | 2,666 | 5,252 | 9,005 |
| 100,000 | 8,664 | 72,205 | — |

## Reference single-stream (no benchmark harness)

| Test | tok/s |
|---|---:|
| Curl with `max_tokens=600` (decode only) | **~44** |

Author of model card (coolthor) reports **24.9 tok/s** baseline without MTP. With our MTP K=5 config we hit **~44 tok/s** — **+76% from speculative decoding**.

## Notes

- **Sweet spot:** depth=4K–8K, c=5 → ~82-84 tok/s aggregate (16-17 tok/s/user, ~5 users)
- **Long context degrades sharply:** at 100K c=5, per-user drops to <1 tok/s — KV pressure
- **TTFR scales with depth × concurrency:** 100K c=5 = 72 seconds first token (KV bound)
- For agent/chat with **16-32K context** — pending benchmark results

*Last updated: 2026-06-05. Updating as full benchmark completes.*
