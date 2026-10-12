# ROG Local Inference

**Status:** Planning / hardware inventory pending  
**Started:** 2026-10-12  
**Platform:** ASUS ROG Strix G533QS  
**Scope:** Local LLM inference, Linux/CUDA, quantization, system-memory and NVMe offloading

## Purpose

Determine the practical limits of running local language models on an existing ROG Strix G533QS before purchasing additional hardware. The immediate research question is whether a 16 GB RTX 3080 Laptop GPU, 32 GB of system RAM, and a suitable second NVMe SSD can support useful inference at higher quantizations, including an experimental 4-bit path using disk-backed offloading.

This is a measurement-driven sidequest, not a commitment to a specific model, framework, storage device, or RAM upgrade. Keep the work in this single document until the observations justify a larger artifact.

## Verified baseline (Windows inventory, 2026-10-12)

| Component | Observed configuration |
| --- | --- |
| System | ASUS ROG Strix G533QS_G533QS |
| CPU | AMD Ryzen 9 5900HX, 8 cores / 16 threads |
| dGPU | NVIDIA GeForce RTX 3080 Laptop GPU, 16,384 MiB VRAM |
| NVIDIA driver | 596.36 (Windows inventory) |
| iGPU | AMD Radeon Graphics |
| RAM | 32 GB, 2 x 16 GB Samsung DDR4-3200 |
| RAM part number | Samsung M471A2G43AB2-CWE, both slots |
| RAM operating speed | 3200 MT/s, both modules |
| Current SSD | Samsung MZVLQ1T0HBLB-00B00 NVMe, approximately 1 TB |
| SSD health | Windows reports Healthy |
| Windows volume | C: 932.9 GiB, 63.4 GiB free at inventory |
| BIOS | G533QS.331 |
| Current OS | Windows 11 Home, build 26200 |
| Network | MediaTek MT7921 Wi-Fi 6, Realtek GbE, Tailscale |

The machine reports two memory devices. Its SMBIOS-reported maximum capacity conflicts with the model-specific 64 GB upgrade target; **64 GB operation has not been validated on this unit**. Do not record an upgrade as supported in practice until tested.

No inference benchmark has been run or validated for this sidequest yet.

## Proposed system layout

- **Existing SSD:** Preserve the working Windows installation and its data.
- **Second SSD (to select):** Native Linux installation, NVIDIA driver/toolchain, inference engines, model weights, and test outputs.
- **Boot:** Separate UEFI boot choices. Confirm target disk and EFI partition before installation; do not repartition or erase the Windows disk.
- **Linux candidate:** Ubuntu 24.04 LTS, subject to driver and inference-engine compatibility checks at build time. Native Linux is preferred over WSL for this experiment to reduce platform and I/O confounders.
- **RAM:** Retain 32 GB for the initial tests. Reassess a matched 2 x 32 GB kit only after identifying the bottleneck.

The second-slot physical fit, interface compatibility, and available storage cooling must be verified against the actual drive before installation.

## Phase 1: Spare SSD inventory and selection

Photograph and identify available drives before buying anything. Prefer a compatible M.2 NVMe drive with adequate capacity, endurance, sustained reads, thermals, and healthy SMART data. Advertised peak sequential speed alone is not a sufficient selection criterion; the laptop may limit newer drives to its available PCIe link.

| Candidate | Model / controller | Capacity | Interface / form factor | NAND / endurance | SMART / wear | Tested throughput / thermals | Decision |
| --- | --- | --- | --- | --- | --- | --- | --- |
| SSD-A | Pending | Pending | Pending | Pending | Pending | Pending | Pending |
| SSD-B | Pending | Pending | Pending | Pending | Pending | Pending | Pending |
| SSD-C | Pending | Pending | Pending | Pending | Pending | Pending | Pending |

Inspection order: label identification, single-/double-sided component layout, interface, SMART health, then non-destructive benchmarks. Do not initialize or erase an unknown spare during inventory.

**Exit criterion:** Select a suitable second SSD and record why it won.

## Phase 2: Linux/CUDA validation

1. Preserve the existing Windows boot path and record recovery options.
2. Install Linux only to the chosen second drive.
3. Record distro/kernel, BIOS, NVIDIA driver, CUDA runtime, GPU memory, power reporting, CPU governor, and thermal state.
4. Confirm the RTX 3080 is used by the inference process, not just detected.
5. Benchmark SSD sequential/random reads and latency under the installed Linux environment.
6. Record tool versions and commands so results can be repeated.

**Exit criterion:** Clean, reproducible Linux GPU inference environment with working dual-boot isolation.

## Phase 3: Baseline inference at 32 GB

Test two complementary tracks:

- **Architecture-specific offloading:** Strata with the supported Qwen3.8-Flash-Next variants and quantizations available when testing begins.
- **General-purpose inference:** llama.cpp and/or Ollama with suitable smaller and mid-sized models to compare fully GPU-resident execution against CPU/RAM offloading.

Start with models and quantizations that fit comfortably, then progress upward until capacity, latency, or throughput makes the workload impractical. Record actual model IDs, hashes, weight formats, backend builds, offload settings, and context sizes. **Do not assume reported results from other GPUs will transfer to this laptop.**

## Phase 4: NVMe offloading and highest practical quantization

Investigate whether higher quantizations, especially approximately 4-bit, are executable and useful through a combined VRAM + RAM + NVMe strategy.

Questions to answer:

1. Does the selected engine actually support disk-backed model storage at 32 GB RAM, or is more physical memory a hard requirement?
2. How much weight data remains in VRAM, DRAM, and NVMe during sustained decoding?
3. What throughput is achieved at 2-bit, 3-bit, and 4-bit for the **same model family and comparable test cases**?
4. Does fast NVMe materially improve decoding, or does the workload bottleneck on CPU, memory bandwidth, PCIe, GPU, or page/cache behavior?
5. At which context lengths do latency, memory pressure, or swap behavior become unacceptable?

Treat a 4-bit run at 32 GB as **experimental**, not guaranteed. If the chosen runtime enforces a higher RAM floor, record that as a constraint rather than bypassing it without documenting consequences. SSD-backed model weights, OS swapping, and filesystem cache are not interchangeable performance mechanisms.

## Benchmark protocol

For each run, capture:

| Field | Record |
| --- | --- |
| Timestamp / run ID | Pending |
| Linux / kernel / driver | Pending |
| Model ID / checksum / parameter count | Pending |
| Quantization / file size | Pending |
| Engine / commit or version / flags | Pending |
| Prompt dataset and token count | Pending |
| Context size / KV-cache settings | Pending |
| GPU layers, offload mode, and memory allocation | Pending |
| Time to first token | Pending |
| Prompt processing rate (tokens/s) | Pending |
| Decode rate (tokens/s) | Pending |
| Peak VRAM / system RAM / swap | Pending |
| NVMe read rate, IOPS, latency, and page-cache condition | Pending |
| GPU power / temperature / clocks; CPU temperature | Pending |
| Output correctness / quality observations | Pending |
| Errors, retries, throttling, and notes | Pending |

Use repeatable prompts and settings, distinguish model loading from inference, and perform multiple runs. Record warm-cache versus cold-start behavior separately. Compare the same tasks across quantizations; do not treat a higher token rate by itself as proof of better utility.

## Phase 5: Decide whether 64 GB is worth buying

The proposed upgrade is **2 x 32 GB DDR4-3200 SO-DIMM**, replacing the current matched 2 x 16 GB Samsung modules. Before purchase, check actual G533QS compatibility and contemporary kit pricing.

Upgrade only if the 32 GB results indicate that RAM capacity is the material limiter and a larger working set is likely to move enough data off NVMe to improve throughput, context length, stability, or output quality.

Decision record:

| Question | Measured answer |
| --- | --- |
| Best useful model at 32 GB | Pending |
| Highest executable quantization at 32 GB | Pending |
| Best interactive decoding rate | Pending |
| Primary performance bottleneck | Pending |
| Expected benefit of 64 GB | Pending |
| Cost of compatible 64 GB kit | Pending |
| Upgrade decision | Deferred |

If 32 GB performs adequately or the GPU/CPU is already limiting, retain the existing RAM. If upgrading, repeat the same benchmark suite at 64 GB before drawing conclusions.

## Initial findings

- The 16 GB RTX 3080 Laptop configuration is confirmed by local NVIDIA telemetry.
- Both Samsung memory slots are occupied by matching 16 GB DDR4-3200 modules at their rated speed.
- The present Windows SSD is substantially occupied; keeping the Linux models on a separate drive is desirable.
- The second-drive selection, Linux installation, and all performance claims remain **unmeasured**.

## Open items

- [ ] Photograph and identify spare SSDs.
- [ ] Check candidate SSD geometry and physical slot fit.
- [ ] Read SMART data and benchmark short-listed SSDs non-destructively.
- [ ] Select and install the Linux drive without modifying Windows partitions.
- [ ] Establish Linux/NVIDIA/CUDA telemetry and storage baseline.
- [ ] Establish 32 GB quantization and context benchmarks.
- [ ] Test the supported NVMe offloading modes, including a 4-bit attempt where feasible.
- [ ] Record quality and interactive-utility tradeoffs alongside raw token rates.
- [ ] Decide whether 64 GB is warranted.
- [ ] Publish an evidence-based outcome; keep negative results.

## References

- [ASUS ROG Strix SCAR 15 (2021) specifications](https://rog.asus.com/laptops/rog-strix/2021-rog-strix-scar-15-series/spec/)
- [Strata inference engine](https://github.com/Niko1221/Strata)
- [Strata model requirements](https://github.com/Niko1221/Strata/blob/main/docs/MODELS.md)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)

Hardware details above were collected from a local Windows 11 inventory on 2026-10-12. Any model requirements, compatibility limits, and third-party performance reports should be rechecked against their source version before running experiments.
