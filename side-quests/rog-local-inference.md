# ROG Local Inference

**Status:** Local inference performance baseline established; real code-review controls show one false-positive classification and one correctly identified metadata bug with flawed reproduction and incomplete fix (2026-10-13)  
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
- **Experimental Linux SSD installed:** Samsung 960 EVO 250 GB, intentionally expendable due to extensive historical media errors. Linux system and test model files reside here.
- **Boot observed:** Ubuntu has its own EFI partition on the 960 EVO. Windows partitions remain on the existing Samsung 1 TB disk and were not mounted in Ubuntu; UEFI Windows boot entries are still present. A successful Windows boot after installation has not yet been reconfirmed.
- **Linux deployed:** Ubuntu 24.04.5 LTS, kernel 7.0.0-38-generic, NVIDIA 595.99.02; native installation.
- **RAM:** Retain 32 GB for the initial tests. Reassess a matched 2 x 32 GB kit only after identifying the bottleneck.

The 960 EVO is already fitted and running. Use the 970 EVO Plus for reliable benchmark iteration, then reserve the new 990 PRO for final deployment only if warranted by measurements. Do not confuse unvalidated performance on the damaged 960 with hardware limits.

## Phase 1: Spare SSD inventory and selection

Photograph and identify available drives before buying anything. Prefer a compatible M.2 NVMe drive with adequate capacity, endurance, sustained reads, thermals, and healthy SMART data. Advertised peak sequential speed alone is not a sufficient selection criterion; the laptop may limit newer drives to its available PCIe link.

| Candidate | Model / controller | Capacity | Interface / form factor | NAND / endurance | SMART / wear | Tested throughput / thermals | Decision |
| --- | --- | --- | --- | --- | --- | --- | --- |
| SSD-1 | Samsung 960 EVO | 250 GB | M.2 NVMe, PCIe 3.0 | TLC | 9,377 media/data errors; 1% used; 99% spare | Not benchmarked | Installed expendable development drive |
| SSD-2 | Intel SSDPEKKF256G8L / Pro 7600p | 256 GB | M.2 NVMe, PCIe 3.0 | TLC | 4% used; no media errors; SMART tests passed | Not benchmarked | Healthy spare |
| SSD-3 | Samsung 970 EVO Plus | 500 GB | M.2 NVMe, PCIe 3.0 | TLC | 0% used; no media errors; about 253 GB written | Not benchmarked | Phase 2: repeatable benchmarks |
| SSD-4 | Toshiba KXG50ZNV1T02 / XG5 | 1,024 GB | M.2 NVMe, PCIe 3.0 | TLC | Inconsistent SMART fields over USB bridge; needs verification | Not benchmarked | Reserve pending health verification |
| SSD-5 | Samsung 990 PRO (box identification) | 1 TB | M.2 NVMe, PCIe 4.0 | TLC | Uninstalled/new; no SMART sample obtained | Not benchmarked | Reserved for potential final setup |

Inspection order: label identification, single-/double-sided component layout, interface, SMART health, then non-destructive benchmarks. Do not initialize or erase an unknown spare during inventory.

**Decision:** Use failing 960 EVO for disposable setup, the 970 EVO Plus for credible comparative tests, and the 990 PRO for a possible final configuration if a 64 GB upgrade is justified. Five candidate drives identified; physical/SMART validation of the 990 PRO and direct retesting of the Toshiba remain outstanding.

## Phase 2: Linux/CUDA validation

1. Preserve the existing Windows boot path and record recovery options.
2. Install Linux only to the chosen second drive.
3. Record distro/kernel, BIOS, NVIDIA driver, CUDA runtime, GPU memory, power reporting, CPU governor, and thermal state.
4. Confirm the RTX 3080 is used by the inference process, not just detected.
5. Benchmark SSD sequential/random reads and latency under the installed Linux environment.
6. Record tool versions and commands so results can be repeated.

**Current result:** Ubuntu is installed and boots independently from the 960 EVO. NVIDIA GPU detection works; Strata setup advanced through its engine-build step and reached the model download. Actual model inference, benchmark repeatability, and a subsequent Windows boot test are pending.

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
- A disposable Ubuntu environment is running on the 960 EVO, and the Windows SSD's partitions were preserved.
- NVIDIA driver 595.99.02 recognizes 16 GiB VRAM. Strata setup detects PCIe 3.0 x8 and selects a low-RAM Coder profile; loaded inference speed is not yet measured.
- Historical 960 EVO media/data errors remain at 9,377 across Windows and direct Linux SMART inspections. Later error log entries include command-level errors rather than newly observed media errors.
- **No completed model generation or performance benchmark yet.**

## Open items

- [x] Identify four used NVMe drives individually through JMicron bridge and record new 990 PRO package.
- [x] Fit and install Ubuntu on the expendable 960 EVO without mounting Windows partitions.
- [x] Gather initial direct Linux SMART, filesystem, boot and GPU telemetry.
- [x] Verify NVIDIA device recognition and advance Strata setup into model download.
- [ ] Finish Strata Coder download and verify successful CUDA-backed generation.
- [ ] Confirm Windows remains bootable from its own EFI partition.
- [ ] Benchmark the healthy 970 EVO Plus under Linux and measure storage latency/throughput.
- [ ] Establish reproducible 32 GB quantization, context, and throughput benchmarks.
- [ ] Test the supported NVMe offloading modes, including a 4-bit attempt where feasible.
- [ ] Record quality and interactive-utility tradeoffs alongside raw token rates.
- [ ] Decide whether 64 GB is warranted.
- [ ] Publish an evidence-based outcome; keep negative results.

## Engineering log

This section records the build as it happened. Commands, observed outputs, chosen settings, exceptions, and incomplete work are preserved separately from the research plan. The failing 960 EVO is suitable for disposable experimentation only; benchmark conclusions must be reproduced on healthy hardware.

### 2026-10-12: Original Windows configuration

PowerShell system inventory captured: ASUS G533QS, AMD Ryzen 9 5900HX (8 cores/16 threads), RTX 3080 Laptop with **16,384 MiB GDDR6 VRAM**, BIOS G533QS.331, two matching 16 GB Samsung M471A2G43AB2-CWE DDR4-3200 modules at 3200 MT/s, and Samsung MZVLQ1T0HBLB-00B00 1 TB NVMe hosting Windows 11 Home build 26200. The Windows C: volume showed 63.4 GiB available out of 932.9 GiB. The system was planned for a separate native Linux installation rather than modifying the Windows storage.

### 2026-10-12: Spare SSD identification and triage

Four SSDs were connected **one at a time** through a JMicron USB NVMe bridge. Windows identified the adapter as "JMicron Tech SCSI Disk Device" instead of showing SSD model numbers. Installed smartmontools 7.5 and used the NVMe-over-JMicron passthrough:

~~~powershell
& "C:\Program Files\smartmontools\bin\smartctl.exe" --scan-open
& "C:\Program Files\smartmontools\bin\smartctl.exe" -a -d sntjmicron /dev/pd1
~~~

| Device | Firmware | Power-on hours | Writes | Other observations |
| --- | --- | ---: | ---: | --- |
| Samsung 960 EVO 250 GB, S3ESNX0K214383X | 3B7QCXE7 | 839 | ~4.46 TB | 9,377 media/data integrity errors; 11,117 error log entries; 1% used; 99% spare |
| Intel Pro 7600p 256 GB, BTHH83940BQL256B | L15P | 10,346 | 9.87 TB | 4% used; 100% spare; no media errors; both short and extended NVMe tests passed |
| Samsung 970 EVO Plus 500 GB, S4P2NF0M401709R | 1B2QEXM7 | 4 (reported) | ~253 GB | 0% used; 100% spare; zero media errors; 51 unsafe shutdowns; ~121 GB namespace utilization |
| Toshiba XG5 1 TB, 28AS10CBTYAT | AADA4102 | 0 (unreliable) | 0 (unreliable) | USB SMART returned PASSED yet 0% available spare and unpopulated counters; retest directly |

An additional **Samsung 990 PRO 1 TB** was identified from a new retail box. It has not been installed, directly identified, or tested. Its PCIe 4.0 advertised speeds are not a prediction of performance in the ROG PCIe 3.0 environment.

**Disposition:** The 960 EVO is intentionally expendable because of historical unrecovered read errors. The operator confirmed no valuable data needs preservation. The 970 EVO Plus is the preferred validation drive; 990 PRO is reserved for a possible final install. No fresh SSD or RAM was purchased.

### 2026-10-13: Ubuntu 24.04.5 installation

**Verified platform:**

~~~text
Ubuntu 24.04.5 LTS (Noble Numbat)
Kernel 7.0.0-38-generic
Ryzen 9 5900HX, 8C/16T, AVX2
32 GB installed; Linux free -h reports ~30 GiB visible RAM and 8 GiB swap
NVIDIA driver 595.99.02
nvidia-smi CUDA API compatibility: 13.2 (not itself proof nvcc/toolkit installed)
RTX 3080 Laptop: 16,384 MiB VRAM, reported power cap 115 W
GPU at inventory: 50 C; 22 W reported, ~120 MiB VRAM consumed by Xorg
UEFI boot
~~~

**Actual storage partition layout:**

~~~text
nvme0n1  Samsung SSD 960 EVO 250 GB, 232.9 GiB
  nvme0n1p1  1 GiB FAT mounted /boot/efi
  nvme0n1p2  231.8 GiB ext4 mounted /
nvme1n1  Samsung MZVLQ1T0HBLB-00B00 1 TB, 953.9 GiB
  Existing Windows EFI, MSR, BitLocker and recovery partitions detected, not mounted
~~~

~~~text
BootCurrent: 0003
BootOrder: 0003,0004,0001
Boot0003: Ubuntu, \EFI\UBUNTU\SHIMX64.EFI, on 960 EVO
Boot0004: Windows Boot Manager on Windows EFI partition
Boot0001: additional Windows Boot Manager entry
~~~

`df -h /` showed 228 GiB filesystem size, 19 GiB used, **197 GiB free (9% used)**. Linux mounts and loads from the 960 EVO, and the Windows partitions were not mounted. Windows restart/boot test remains outstanding.

**Collection commands used:**

~~~bash
cat /etc/os-release
uname -r
lscpu
free -h
lspci | grep -Ei 'VGA|3D|NVIDIA'
nvidia-smi
lsblk -o NAME,MODEL,SIZE,TRAN,FSTYPE,MOUNTPOINTS
df -h /
sudo efibootmgr -v
sudo journalctl -k -b --no-pager
sudo smartctl -a /dev/nvme0n1
~~~

**SSD diagnostics after installation.** Direct Linux SMART showed 9,377 media/data integrity errors, identical to the Windows count. Error-log entries grew from 11,117 to 11,181 (+64). The newest entries were primarily "Invalid Field in Command", "Feature Identifier Not Saveable", and "Invalid Log Page", so that increment cannot simply be equated to new NAND failures. Available spare remained 99%; endurance-used stayed at 1%; the reported temperature was 31 C (second sensor 42 C); host writes grew from ~4.46 to ~4.48 TB.

Kernel output repeated `nvme nvme0: 7/0/0 default/read/poll queues` around 09:15, 09:51, 10:07, 10:38 and 10:43. Expanded context showed those lines during **suspend/resume** sequences, not during demonstrated NVMe read faults. ACPI/NVIDIA notifier warnings were also observed during resumes. No new ext4 I/O failures were shown in the examined excerpts. This does not rehabilitate the damaged SSD; it simply describes what was and was not observed.

### 2026-10-13: Strata setup and model download

Installed `git` and cloned [Niko1221/Strata](https://github.com/Niko1221/Strata), then ran:

~~~bash
sudo apt update
sudo apt install -y git
git clone https://github.com/Niko1221/Strata.git
cd Strata
./setup.sh
~~~

**Installer hardware detection:** RTX 3080 Laptop 16.0 GB, compute capability 8.6, NVIDIA 595.99.02; Ryzen 9 5900HX with AVX2; ~31 GB RAM. GPU PCIe connection reported as **3.0 x8** with an estimated ~8 GB/s theoretical one-direction transfer ceiling. Strata showed a generic warning that only eight of sixteen lanes were active. The GPU is soldered into a laptop; link topology/negotiation under actual load remains to be checked rather than attempting desktop-card reseating advice.

The laptop was initially powered through USB-C PD; the original ASUS AC adapter was connected during setup, before any benchmark. **Power source and power/performance mode must be fixed and recorded** for comparisons.

**Selected Strata configuration:**

| Setting | Value | Notes |
| --- | --- | --- |
| Model family | Qwen3.8-Flash-Next **Coder** (menu 3) | Reduced expert subset; not directly equivalent to full model |
| Quantization | Coder `IQ1_M` | Installer: ~58 GB download; ~23 GB RAM requirement |
| Memory placement | Low-RAM mode | Installer estimated ~47% of experts on the GPU |
| Context limit | 64K | Recommended for detected 16 GB GPU |
| KV cache | 8-bit | Baseline matching Strata-published comparison format |
| Vision | Off | Avoids extra model and ~1.4 GB VRAM reservation |
| Experimental speed/refusal-direction projection | Off | Keep normal model behavior for baseline |
| Power for future measurements | ASUS barrel adapter | Avoid USB-C power-limit confound |

**Build issue:** Strata v0.1.43.1's prebuilt inference engine URL returned HTTP 404; the "latest" binary fallback also returned 404. The installer therefore proposed a **local build**, requiring CUDA Toolkit 13.0 and build dependencies (~8-10 GB estimated). Operator accepted. The next prompt (model download) appeared in **under five minutes**; exact build logs, `nvcc` version, and binary hash have not yet been recorded. The installer reported a llama.cpp source checkout at **`3cf0325`**.

**Step 5 (completed):** Two Coder IQ1_M model shards downloaded under:

~~~text
/home/rog/Strata-data/models/coder-IQ1_M/
  Qwen3.8-Flash-Next-GSQ-RCO-IQ1_M-00001-of-00002.gguf
  Qwen3.8-Flash-Next-GSQ-RCO-IQ1_M-00002-of-00002.gguf
~~~

**GGUF shard retrieval and model preparation succeeded** based on the subsequent Step 6 output. End-to-end model validation and successful text generation remain unverified.

**Next steps:** Let model files finish downloading; check for download and storage errors; start a text generation smoke test; record peak VRAM/RAM/SSD metrics; run Strata's built-in benchmark on the 960 EVO for exploratory results; later reproduce on 970 EVO Plus before treating results as reliable. Benchmark 4-bit only with a supported configuration and document RAM prerequisites.


### 2026-10-13: Model preparation, expert packing, and MTP (Step 6)

**Observed successful commands** (executed by setup through the Strata Python venv):

~~~bash
/home/rog/Strata/.venv/bin/python /home/rog/Strata/tools/iq_pack.py \
  --gguf /home/rog/Strata-data/models/coder-IQ1_M/Qwen3.8-Flash-Next-GSQ-RCO-IQ1_M-00001-of-00002.gguf \
  --out /home/rog/Strata-data/packs/coder-iq1_m
/home/rog/Strata/.venv/bin/python /home/rog/Strata/tools/iq_pack.py \
  --gguf /home/rog/Strata-data/models/coder-IQ1_M/Qwen3.8-Flash-Next-GSQ-RCO-IQ1_M-00001-of-00002.gguf \
  --out /home/rog/Strata-data/packs/coder-iq1_m --experts-bin
~~~

Both input shards were discovered. The initial indexing report listed **1,079 tensors**, **302 served natively**, **0 in extra.bin**, and an **arena of 1.37 GiB**. Tokenizer report: vocab 248,320; merges 247,587; pre-tokenizer `qwen35`; EOS ID 248046 and padding/BOS ID 248044.

The low-RAM packing run concatenated experts from **48 layers** into:

~~~text
/home/rog/Strata-data/packs/coder-iq1_m/experts.bin
experts.bin: 48 layers, 23.42 GiB
[ok] model prepared: /home/rog/Strata-data/packs/coder-iq1_m
~~~

Mixed expert tensor encodings were observed across sampled layers: `IQ3_XXS/IQ4_NL` (0), `IQ2_S/Q2_0` (8), `IQ3_XXS/IQ4_NL` (16), `IQ2_S/IQ4_NL` (24, 32), and `IQ3_S/IQ4_NL` (40). Layer output offsets reached 19.04 GiB at layer 40. These are model packing facts, **not measurements of loaded VRAM residency or runtime quantization quality**.

**MTP speculative-decoding dependency:** The setup fetched only the MTP block from the source model's BF16 checkpoint:

~~~bash
/home/rog/Strata/.venv/bin/python /home/rog/Strata/tools/mtp_fetch.py \
  fetch --out /home/rog/Strata-data/mtp
~~~

It reported **31 BF16 tensors**, **5.214 GB across 28 checkpoint shards**. All listed tensor downloads reached 100%, including the large `mtp.layers.0.mlp.experts.down_proj` (1,677.7 MB) and `mtp.layers.0.mlp.experts.gate_up_proj` (3,355.4 MB).

**Observed next command, still running at last report:**

~~~bash
/home/rog/Strata/.venv/bin/python /home/rog/Strata/tools/mtp_pack.py \
  --src /home/rog/Strata-data/mtp --experts q2_0 \
  --out /home/rog/Strata-data/mtp/mtp-q2_0.gguf
~~~

The final visible line was `mtp.layers.0.mlp.experts.down_proj: q2_0 235.9 MB`. There was no error in the supplied output. **Do not mark MTP packing, model startup, GPU execution, or any benchmark complete until subsequent logs confirm them.**

**Engineering interpretation:** Strata's low-RAM preprocessing created a 23.42 GiB expert file separate from the source GGUF weights. This is not evidence that SSD page-in throughput dominates decoding, nor is the arena size a measured VRAM allocation. On the suspect 960 EVO, successful file creation does not establish data durability.


### 2026-10-13: Start-script memory policy and calibration prompt (Step 7)

Installer advanced to **Step 7: writing the start script** after the model/MTP preparation stage. **Subsequent output confirms successful loading and completion of calibration**; detailed results are recorded in the next entry.

**Automatic decision printed by installer:**

```text
[ok] KV streaming off: it needs ~0.9 GB of RAM beside the ~32 GB IQ1_M uses,
and this PC has 31; the KV cache stays in VRAM (fewer cached experts).
--kv-streaming on turns it on anyway
```

This is the installer's stated **working-memory requirement**; it is not the same figure as the **23.42 GiB** on-disk `experts.bin` created in Step 6. KV streaming was therefore disabled, leaving the KV cache in VRAM and reducing available VRAM for experts. The installer provided an explicit override, `--kv-streaming on`, but it is **not selected** in the baseline.

**Other installer guidance:**

- The RTX 3080 also drives the Linux display. If a full model load causes a freeze or black screen (upstream issue #779), rerun setup with `--vram-reserve-mib 1500`. No such failure has yet been observed.
- Installer cited an upstream community benchmark (issue #834) suggesting changing `"STRATA_RESIDENT_HEADROOM_GIB"` from default `4` to `6` on a 31 GB RAM system; it reportedly retained decoding speed while leaving 2 GiB more RAM available to the OS. **Not applied**; test as a separate variable later.
- Calibration prompt: `Tune Strata for this PC now? ... (about 15-30 minutes; later: START-HERE --calibrate) [y]`.
- Calibration was accepted and subsequently completed on ASUS AC power, with 64K context, 8-bit KV, no vision/projection. The measured results and resulting defaults are recorded below.

**Measurement caution:** The calibration is a machine-specific result, not yet a comparative GPU benchmark. The faulty 960 EVO, the GPU's role driving the display, RAM pressure, and power modes are confounding factors; repeat calibration on the healthy drive before final performance conclusions.

### 2026-10-13: First successful Strata calibration (Step 7 completed)

**Milestone:** Model fully loaded, GPU expert cache filled, and the calibration suite completed. The resulting startup script is \`run-coder-iq1_m.sh\`. The installer declared **"All set."** and reported that default settings were already fastest in its test at **47.4 generated tokens/s**. These are *calibration-workload measurements on the failing Samsung 960 EVO*, not a formal benchmark report or proof of general-purpose response speed.

**Engine startup observations:**

~~~text
[strata] starting the engine: reading the model's weights ...
[strata] loading the experts the GPU does not hold into RAM
[strata] filling the GPU's expert cache (4452 experts, 8.47 GiB of VRAM) ...
[strata] almost ready ...
[strata] still starting (32 s) - please wait ...
~~~

The engine was relaunched repeatedly for worker and expert-tier tests. Every shown launch progressed to throughput measurement. **4,452 GPU-resident expert slots using 8.47 GiB VRAM** is an observed cache allocation, not total VRAM usage. The startup log was still waiting at 32 seconds; an exact fully-ready duration and peak RAM/VRAM were not captured. No black-screen failure was reported. External monitor is connected by DisplayPort over USB-C, which should be treated as a dGPU-display/VRAM reservation variable in later comparisons.

**PCIe share sweep, generated tokens/s; three rounds per setting:**

| Share | Run 1 | Run 2 | Run 3 | Median |
| ---: | ---: | ---: | ---: | ---: |
| 0.00 | 41.7 | 41.5 | 42.4 | 41.7 |
| 0.15 | 43.8 | 41.9 | 42.9 | **42.9** |
| 0.20 | 41.7 | 41.4 | 43.4 | 41.7 |
| 0.35 | 37.0 | 37.1 | 37.2 | 37.1 |
| 0.55 | 31.6 | 31.1 | 30.5 | 31.1 |
| 0.75 | 27.1 | 26.6 | 27.5 | 27.1 |
| 0.90 | 23.4 | 25.6 | 25.6 | 25.6 |
| 1.00 | 22.2 | 22.2 | 21.8 | 22.2 |

**PCIe share finding:** 0.15 had the best median within this sweep, 42.9 tok/s; share 1.0 fell to 22.2 tok/s (~48% lower). This is sensitivity to the **engine's configured computation/transfer strategy**, not direct evidence of measured PCIe bus saturation. Compare only within a particular tuning subtest because others were run under different settings/restarts.

**Speculative-draft floor sweep, generated tokens/s:**

| Draft floor | Run 1 | Run 2 | Run 3 | Median |
| ---: | ---: | ---: | ---: | ---: |
| 0.3 | 42.5 | 42.1 | 41.7 | 42.1 |
| 0.5 | 42.5 | 41.9 | 43.6 | 42.5 |
| 0.7 | 43.6 | 43.5 | 41.7 | **43.5** |

**CPU workers** (one output reported per configuration; engine restarted before each):

| CPU workers | Generated tokens/s |
| ---: | ---: |
| 7 | **43.1** |
| 5 | 41.0 |
| 4 | 39.2 |
| 2 | 34.6 |

Seven CPU workers performed best of the four tested. Sample size and test type differ from the three-round sweeps, so this is a preliminary engine-specific configuration finding, not a general CPU scaling curve.

**Expert-tier policy** (one result per policy; engine restarted):

| Expert cache policy | Generated tokens/s |
| --- | ---: |
| Engine default | **47.4** |
| Every 1, 80 swaps, decay 0.97 | 45.0 |
| Every 1, 160 swaps, decay 0.97 | 45.2 |

The expert-cache **default** outperformed the two more aggressive swapping policies under this workload. Reported **47.4 tok/s** is the highest value in the shown calibration output, but is not a complete benchmark of model accuracy, prompt processing, or interactive latency.

**Startup and serving configuration announced:**

~~~text
[ok] tuned for this PC: the default settings are already the fastest here (47.4 tok/s)
[ok] start script: run-coder-iq1_m.sh
API (OpenAI):    http://127.0.0.1:8080/v1
API (Anthropic): http://127.0.0.1:8080/v1/messages
Next time: ./setup.sh (or run-coder-iq1_m.sh)
Parallel requests: kept one at a time
~~~

The setup documented an estimated 10-25% per-request speed cost from parallel batching on this card, but we **have not measured batching locally**. Localhost endpoints are installer-provided interface addresses and do not by themselves prove an external client connection test.

**Current stopping point:** Installer asks whether to run a roughly **10-minute quick benchmark** and write a shareable report (\`./bench.sh\` later); default answer \`[n]\`. Recommended answering **yes**, but **benchmark initiation/completion/report not yet confirmed**.

**Follow-up:** Capture the generated benchmark output and report path; test correctness and latency through the HTTP API; record peak RAM, swap, total VRAM, CPU/GPU temperatures, clock/power, PCIe link under load, and monitor routing. Keep 960 results explicitly provisional and repeat comparisons on the healthy Samsung 970 EVO Plus. Do not change resident headroom, KV streaming, context, or display configuration before preserving this baseline.

### 2026-10-13: Formal Strata benchmark, successful startup, and browser issue

**Milestone:** Operator selected \`y\` at the quick-benchmark prompt. Strata ran a warmup and three repetitions of short-chat, 4K, 16K, and six-turn agent-session tests. The suite completed successfully and generated a shareable benchmark report. A ZIP with three files was provided for this log:

~~~text
bench/results/2026-10-13-community-rtx-3080-laptop-gpu-qwen3-8-flash-next-coder-iq1-m/
  README.md
  results.json
  engine-log-excerpt.txt
~~~

**Measurement method:** \`tools/strata_bench.py\`, suite schema 1, 3 runs per test; warmup discarded; first token measured by the local HTTP client; throughput from engine telemetry. Fresh prompts use unique identifiers to avoid cache reuse. Six-turn agent tests accumulate synthetic file text and four tool definitions. Model **correctness, actual code comprehension, and answer quality were not assessed**. Total measured suite elapsed time: **183.805 s (~3.1 minutes)**; \`results.json\` lists **no failures**. Loading time excluded.

**Environment frozen from ZIP, 2026-10-13:**

| Field | Observed |
| --- | --- |
| Host | G533QS; Ryzen 9 5900HX (8 cores / 16 threads); 30.8 GiB visible RAM |
| OS | Ubuntu 24.04.5 LTS; kernel \`7.0.0-38-generic\`; Python 3.12.3 |
| GPU | NVIDIA RTX 3080 Laptop, 16 GiB, driver 595.99.02; reported CUDA compatibility 13.2 |
| Strata | Project/engine v0.1.43.1; project commit **\`a0116b27\`** |
| Model | \`qwen3.8-flash-next-coder-iq1_m\`, IQ1_M, context 65,536, concurrency 1 |
| Request sampling | Thinking off; temperature 0 |
| Main runtime options | \`--expert-cache auto --prefill auto --spec 4 --mtp rt --max-context 65536 --kv int8 --resident-experts --spec-min-p 0.5\` |
| Expert caching | 4,452 GPU expert slots using 8,672 MiB (8.47 GiB) |
| Engine status values | 7 CPU workers; \`pcie_frac=0.14\`; \`vram_free_mib=421\` |
| Power/display | ASUS barrel adapter connected; external display via USB-C DisplayPort (not captured independently in report machine metadata) |
| SSD | Faulty Samsung 960 EVO 250 GB; storage throughput/latency **not measured** |

**Benchmarks, medians (full observed range):**

| Test | Prompt / output tokens | Samples | Prompt processing (tok/s) | Decode (tok/s) | First token (s) |
| --- | --- | ---: | ---: | ---: | ---: |
| Short chat | 43-51 / 100 | 9 | Not recorded | **35.50** (34.40-44.80) | **0.71** (0.63-0.82) |
| Fresh 4K synthetic prompt | 4,019 / 24 cap | 3 | **892.70** (886.30-898.10) | Not recorded | **4.53** (4.50-4.56) |
| Fresh 16K synthetic prompt | 16,058 / 24 cap | 3 | **1,162.00** (1,161.60-1,162.10) | Not recorded | **13.85** (13.85-13.86) |
| Agent follow-up turns | Final prompt ~14.45K / up to 64 | 15 follow-up turns in 3 sessions | See cache details | **42.80** (35.30-52.80) | **4.14** (3.79-4.41) |

**All nine short-chat requests:**

| Run | q1 decode / TTFT | q2 decode / TTFT | q3 decode / TTFT |
| --- | --- | --- | --- |
| 1 | 44.8 tok/s / 0.771 s | 34.4 / 0.656 s | 34.8 / 0.692 s |
| 2 | 41.8 / 0.761 s | 35.5 / 0.632 s | 34.8 / 0.710 s |
| 3 | 42.7 / 0.823 s | 37.5 / 0.693 s | 35.3 / 0.708 s |

**Fresh-prompt repetitions:** 4K prompt rates 892.7, 886.3, 898.1 tok/s and TTFT 4.532, 4.562, 4.502 s; 16K rates 1161.6, 1162.0, 1162.1 tok/s and TTFT 13.859, 13.854, 13.851 s. The greater throughput at 16K comes with greater end-to-end latency; do not confuse throughput with responsiveness.

**Agent-session caching:** 68.74% median reused prompt tokens over follow-up turns (range 68.57%-68.83%); earlier-turn tokens reread per session median 5 (range 4-73), with ~13,634 new file tokens read once. First uncached turn TTFT 2.54 s (2.46-2.57). The third report describes six turns per session and four tool definitions. For run 1, token accounting and decoding were:

| Turn | Prompt | Cached | Read | TTFT (s) | Decode (tok/s) |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | 609 | 0 | 609 | 2.462 | 36.3 |
| 1 | 3,715 | 632 | 3,083 | 4.330 | 38.2 |
| 2 | 5,248 | 3,778 | 1,470 | 3.829 | 39.4 |
| 3 | 8,692 | 5,312 | 3,380 | 4.407 | 39.7 |
| 4 | 11,604 | 8,744 | 2,860 | 4.207 | 45.8 |
| 5 | 14,497 | 11,651 | 2,846 | 4.170 | 50.0 |

**Post-benchmark model startup:** Installer restarted the model and printed \`ready: http://127.0.0.1:8080/v1\` (OpenAI \`/v1/chat/completions\`, Anthropic \`/v1/messages\`) and offered \`http://127.0.0.1:8080/\` as browser UI. Engine startup again printed 4,452 experts / 8.47 GiB GPU cache and "still starting (32 s)" before ready.

**Observed issue:** Immediately after "Opening in existing browser session", Chromium emitted two \`zygote ... Broken pipe (32)\` messages. These are **browser process/IPC errors**, not demonstrated failure of the inference server, whose ready state was printed. Whether the browser UI actually opened was not reported. Check manually at \`http://127.0.0.1:8080/\` before debugging engine startup.

**Engineering assessment:** This is a **working local LLM inference baseline** with 35.5 tok/s median short-chat decoding and less than 1 second median TTFT on short prompts. The 47.4 tok/s calibration maximum was from a different workload and must not replace the formal short-chat median. Engine-reported 421 MiB free VRAM suggests limited GPU allocation margin, particularly while USB-C DisplayPort uses the dGPU, but is not an independent \`nvidia-smi\` measurement. No SSD read-rate, IOPS, CPU/GPU thermals, power draw, clock, peak RAM, swap, page fault or answer correctness data were collected. The test neither establishes nor refutes the practical value of 64 GB RAM or faster storage.

**Follow-up tests:** (1) confirm web UI or API interaction and request correctness, (2) capture \`nvidia-smi\`, \`free -h\`, \`vmstat\`, and \`iostat\` during a real coding/log-analysis prompt, (3) examine GPU display-routing and VRAM headroom under load, (4) re-run repeatable tests using the healthy 970 EVO Plus, (5) compare higher model precisions only if hardware requirements permit. Do not publicize the raw ZIP as benchmark proof of reasoning ability; the source report explicitly measures speed only. Archive the ZIP somewhere reliable rather than solely on the damaged 960 EVO.

### 2026-10-13: Real code-analysis attempt, reasoning exhaustion (negative result)

**Purpose:** Replace synthetic speed tests with a practical analysis of the author's Training Engine application. The operator supplied seven source/runtime files to the local Strata web UI: \`app.js\`, \`bootstrap.js\`, \`index.html\`, \`page.js\`, \`questions.js\`, \`README.md\`, and \`styles.css\`. User request: **"analyze my training engine"**. The engine includes question-bank selection and schema validation, exam/practice logic, mastery/progress persistence, answer review, and AI-assisted explanation integration.

**Result: failure to produce a final answer.** Strata produced a long chain of thought, reached the generation ceiling, and terminated the request without a response body:

~~~text
[strata] thinking: 39919 of max 39968 tokens, 42.1 tok/s, 966 s
[strata] done: 39968 tokens in 966 s (42.2 tok/s) (length, cancel=False), expert cache 90.0% hit (+0.1% of the routed experts over PCIe)
[strata] the reply reached max tokens while still thinking, so it has no answer: a thinking budget (reasoning_budget_tokens, in the request or in strata-<model>.json for every request) leaves room to answer
~~~

**Observed metrics:**

| Metric | Outcome |
| --- | --- |
| Prompt | "analyze my training engine" with seven uploaded project files |
| Generated reasoning | 39,968 tokens (entire generation allowance) |
| Final user-visible answer | **None** |
| Termination | Output length, \`cancel=False\`; not a reported crash |
| Runtime | 966 s (16 min 6 s), ~42.2 tok/s |
| Adaptive expert cache | 90.0% hit |
| Routed experts over PCIe | +0.1% per engine diagnostic |
| Accuracy / software defects found | **Unmeasured** because no answer was produced |
| Client input-token count / exact thinking level | Not captured |

**Diagnosis:** This run reached the **generation/output limit**, not a demonstrated out-of-memory, PCIe, or 65,536-token context-window limit. A steady decode rate near calibrated speeds indicates the engine sustained generation, but **throughput without useful output is not task success**. This is a distinct inference-quality/usability failure mode and should remain visible even if subsequent mitigations work.

**Mitigation for controlled retest:** Strata supports a separate hard reasoning allowance \`"reasoning_budget_tokens": 4096\`, either in an individual OpenAI/Anthropic request or as a top-level key in \`strata-coder-iq1_m.json\` (About > Model settings; restart model to apply). The engine closes reasoning and continues to the answer when the budget is reached; \`0\` removes the cap. Use a low thinking setting, a smaller total output allowance such as 8,192 tokens, and one bounded task. Example evaluation prompt: *"Review the supplied Training Engine JavaScript files. Identify the three highest-confidence functional bugs. For each, cite the file and function, describe a reproducible failure case, and propose the smallest correction. Do not suggest enhancements or refactoring."* This is **a proposed next test**, not a completed rerun. Preserve the original no-answer result as an independent data point.

**Follow-up:** Measure actual reasoning vs. answer tokens, elapsed time, and source-grounded accuracy on individual bug claims. Compare with another model/precision only after controlling prompt scope and budget. References: [Strata documentation on hard thinking budgets](https://github.com/Niko1221/Strata/blob/main/docs/DETAILS.md).

### 2026-10-13: Second code-analysis attempt, 4,096-token exhaustion

After the first 39,968-token / 966-second analysis attempt produced no final answer, the operator used Strata's web UI to adjust the thinking control as previously recommended. The rerun still produced no answer:

~~~text
[strata] thinking: 4090 of max 4096 tokens, 39.5 tok/s, 122 s
[strata] done: 4096 tokens in 122 s (39.5 tok/s) (length, cancel=False), expert cache 88.6% hit (+0.1% of the routed experts over PCIe)
[strata] the reply reached max tokens while still thinking, so it has no answer: a thinking budget (reasoning_budget_tokens, in the request or in strata-<model>.json for every request) leaves room to answer
~~~

| Metric | First code review | Second code review |
| --- | ---: | ---: |
| Total generated tokens | 39,968 | **4,096** |
| Wall time | 966 s | **122 s** |
| Generation rate | 42.2 tok/s | **39.5 tok/s** |
| Expert-cache hit rate | 90.0% | **88.6%** |
| PCIe routed experts (per engine log) | +0.1% | +0.1% |
| Final review delivered | No | **No** |

**Interpretation:** The message `thinking: ... of max 4096` indicates that 4,096 tokens were available **for total generation** on this request, rather than that the hard reasoning budget forced an early reasoning close with room left for an answer. A model-side thinking setting, a per-request cap, the config's `reasoning_budget_tokens` field, and the server restart state must be distinguished before blaming quantization or model comprehension. It is **not yet confirmed** what was actually saved in the JSON file or whether a new server process loaded it.

**Controlled diagnosis proposed:**

1. Examine `~/Strata/strata-coder-iq1_m.json` for a **top-level** `"reasoning_budget_tokens"` field; ensure it is not confused with `max_tokens` or nested under `args`. Check whether the engine was restarted after saving web About > Model settings.
2. Give generation room beyond the reasoning cap, e.g. `reasoning_budget_tokens=1024` or `2048`, with a separate **total output cap of 8192**, and a low thinking level. Request-side values override config.
3. For a minimal functionality check, select **Thinking: Off** in the chat UI, start a fresh conversation, and request a concise review of one uploaded JS file; this isolates the thinking loop from actual answer ability.
4. Keep the same RTX 3080, IQ1_M weights, context setting, and AC power so this is a software-control comparison.

No controlled correction has been confirmed yet. Strata's [DETAILS.md](https://github.com/Niko1221/Strata/blob/main/docs/DETAILS.md) describes both hard `reasoning_budget_tokens` and the independent generation cap.

### 2026-10-13: JSON syntax error after reasoning configuration edit

The operator tried adding a persistent reasoning budget/effort to `~/Strata/strata-coder-iq1_m.json`. Launching `./run-coder-iq1_m.sh` failed **before engine or model initialization**:

~~~text
File "/home/rog/Strata/serve/server.py", line 6348, in main
  cfg = json.loads(Path(a.config).read_text(encoding="utf-8-sig")) if a.config else {}
json.decoder.JSONDecodeError: Expecting ',' delimiter: line 39 column 2 (char 959)
~~~

The operator **removed the added setting and the server launched again**. The edited version of the JSON file was not supplied, so the exact malformed token is unknown. This is evidence of **invalid JSON formatting**, **not** evidence that the `reasoning_budget_tokens` key is unsupported or causes an engine failure. Upstream Strata source at the tested commit explicitly reads that top-level key.

Valid ending for the observed `strata-coder-iq1_m.json` file:

~~~json
  "port": 8080,
  "gpu": 0,
  "gpus_asked": true,
  "reasoning_budget_tokens": 1024,
  "reasoning_effort": "low"
}
~~~

**Preventative check**: after any manual edit, run `python3 -m json.tool ~/Strata/strata-coder-iq1_m.json >/dev/null && echo "JSON valid"` before restarting. Preserve existing settings and bracket/commas. The separate default `max_tokens` setting is supported in Strata's `strata-coder-iq1_m.shared-settings.json`; it is **not a supported top-level model-JSON output default**. No subsequent successful code-review inference was reported.

### 2026-10-13: First normally terminated code-analysis generation

Following an unsuccessful 39,968-token reasoning run and another 4,096-token run that both ended inside thinking with no response, the operator corrected a missing comma in the model JSON, then successfully started Strata with intended top-level `reasoning_budget_tokens: 1024` and `reasoning_effort: low`. The operator had **not** configured a separate persistent default `max_tokens` in the shared-settings JSON. An unspecified browser save action was missed; the browser's final answer text has **not** been supplied for inspection.

Latest console output:

~~~text
[strata] answering: 4015 of max 4096 tokens, 42.0 tok/s, 114 s
[strata] done: 4056 tokens in 115 s (42.1 tok/s) (stop, cancel=False), expert cache 85.5% hit (+0.2% of the routed experts over PCIe)
~~~

| Metric | Prior long run | Prior short run | Current run |
| --- | ---: | ---: | ---: |
| Generated tokens | 39,968 | 4,096 | **4,056** |
| Total output ceiling | 39,968 | 4,096 | **4,096** |
| Elapsed time | 966 s | 122 s | **115 s** |
| Generation rate | 42.2 tok/s | 39.5 tok/s | **42.1 tok/s** |
| Stop reason | `length` | `length` | **`stop`** |
| Phase at termination | Thinking | Thinking | **Answering** |
| Expert cache hit | 90.0% | 88.6% | **85.5%** |
| Answer content provided for review | No | No | **No** |

**Interpretation:** The new terminal telemetry is positive: Strata entered the `answering` phase and reached a normal stop, rather than exhaust the cap inside a thinking segment. Generation stopped 40 tokens below its 4,096-token ceiling. This **supports the reasoning-budget mitigation**, but does **not independently prove** the exact budget applied because no startup `thinking budget` log line or thinking-to-answer transition counter was included. Whether the actual resulting prose was complete, coherent, accurate, or saved remains unverified. The 4,096 cap still appears to come from the chat request/browser setting. No change in hardware/precision is justified by these results alone.

**Next:** Preserve the full browser response, verify `reasoning_budget_tokens` was loaded at startup, record elapsed time and separate thinking/answer tokens, then independently validate source-referenced findings in the Training Engine. Avoid confusing terminal stop success with code-review quality.

### 2026-10-13: First recovered code-review answer, analysis-quality failure

The operator provided the actual 230-line response from the previously recorded **4,056-token, 115-second** code-review run (Strata ended in `answering` with `stop`). Prompt asked for the **three highest-confidence functional bugs** in the seven-file Training Engine. The answer contains a discernible analytical loop: propose a possible bug, examine its own argument, retract it, then propose another. This is **not** a byte-identical repeated-token loop or a crash, and does not establish quantization as its cause.

**Self-corrections documented in the output:**

- Proposed an `expiresAt` timestamp sanitization defect, then admitted the scenario was not reproducible.
- Proposed a double-submit race between `updateTimer()` and `submit()`, then correctly concluded `clearInterval(ticker)` was sufficient.
- Proposed that unawaited `updateBuildNote()` was a functional bug, then classified it as a cosmetic loading detail.
- Proposed that the `renderResults` wrapper broke `currentResult`, then traced its call chain and retracted the claim.
- Proposed stale confidence `onchange` closure after navigation, then recognized `currentResponse()` deliberately reads the new current item.
- Proceeded to force a third bug candidate rather than report that fewer than three were established.

**Independent audit against the supplied 2026-10-13 Training Engine files:**

| Claimed defect | Evaluation | Evidence and nuance |
| --- | --- | --- |
| #1: Practice-mode locking leaves confidence and notes editable, allegedly corrupting reveal state | **Unproven / likely product-policy choice** | `app.js` `renderQuestion()` deliberately disables only answer radio inputs when the practice answer is locked; note and confidence handlers remain bound via `bind()`. Neither modifies `response.answer` or the canonical `question.answer`; `renderAnswerReveal()` recomputes correctness on render. The ability to revise a personal note or confidence later is not proof of data corruption. One proposed fix mistakenly wrote `response.answer=e.target.value` when discussing confidence, which would introduce a defect. If confidence is to be frozen at reveal as a measurement policy, specify that as a requirement, not an established bug. |
| #2: Exam-mode `move()` sends a fully answered, unflagged run back to index 0 | **False positive as characterized** | `app.js` `renderQuestion()` changes the final exam-mode Next button's label to **Review**. `move()` intentionally prioritizes unanswered questions, then flagged questions, otherwise the first question. This is an explicit review-navigation behavior, not an accidental unexplained jump. |
| #3: Completed result's `configuredQuestionCount` reads mutable `state.settings.questionCount` | **Valid metadata-integrity concern** | `app.js` `startNew()` selects `active.items`, but `submit()` reads the globally current `state.settings.questionCount` instead of a run-start snapshot; `page.js` `saveCustomize` can update settings while an active attempt exists, and `restartActiveInMode()` retains the same items even after settings change. This can report a configured count inconsistent with the run's originally selected question set. The model's `active.items.length` patch is incomplete because *requested/configured* count can exceed *selected/presented* count when mastered questions are excluded. Any proper patch must snapshot original settings at run creation, preserve them on mode restart, and restore them in `sanitizeActiveAttempt()` so resume/import does not discard the new field. |

**Overall:** Of three confident claims, **one source-grounded metadata defect**, **one false positive against deliberate review UX**, and **one unsubstantiated locking-policy criticism**; the suggested fixes include one direct field-assignment error and one incomplete data-lifecycle change. No bug fixes applied. The run demonstrates eventual answer generation after the thinking-budget mitigation but **fails the requested high-confidence engineering review standard**. It cannot be counted as successful source-based reasoning merely because the engine returned `stop`.

**Next controlled tests:** Ask for a single verifiable execution path, e.g. `"Trace how configuredQuestionCount is recorded when a run starts, settings change while it remains active, and it is submitted. Quote the exact functions, and distinguish configured vs presented counts."` Or ask the model to **verify** one candidate bug at a time, explicitly allowing `"no bug"` as an answer. Score source accuracy, self-contradictions, patch correctness and time-to-first-useful-answer in addition to tokens/sec. Keep model/hardware configuration constant until baseline quality is characterized.

### 2026-10-13: Negative-control test, exam review navigation (failed)

**Goal:** Test whether Qwen3.8-Flash-Next Coder IQ1_M can correctly reject a false-positive defect report, rather than manufacture a fix. Operator started a new Strata chat, uploaded `app.js` from [Training Engine](https://github.com/novovictus/training-engine/blob/main/practice-test/app.js), and asked it to audit the claim: in exam mode, when all questions are answered and unflagged, using the final-question navigation button returns to question 1. Explicitly asked for a classification of **CONFIRMED BUG**, **EXPECTED BEHAVIOR**, or **INCONCLUSIVE** and to inspect the button label and `renderQuestion()/move()`.

**Engine log:**

~~~text
[strata] answering: 1469 of max 60975 tokens, 44.2 tok/s, 34 s
[strata] done: 1470 tokens in 34 s (44.2 tok/s) (stop, cancel=False), expert cache 84.8% hit (+0.3% of the routed experts over PCIe)
~~~

The **60,975-token generation allowance** is the per-request ceiling in this run, **not the 1,024-token thinking budget**; the operator had not configured a persistent 8,192 output limit. The run ended normally after **1,470 total tokens** in **34 seconds**, expert cache **84.8%**. No evidence of a runaway generation loop.

**Model verdict:** **CONFIRMED BUG.** It correctly traced the `move(1)` branch: `index` falls back to 0 after neither an unanswered nor flagged question is found. It then asserted that users see a `Next` button with no review indication, called the fallback arbitrary, and recommended a stay-on-last or review-complete state.

**Verified source behavior in the current training-engine/main file:**

- `app.js` **line 380** explicitly sets the final exam-mode `next-btn` text to **`Review`**; all earlier questions use `Next`.
- `app.js` **lines 392–399** intentionally navigate to the first unanswered question, first flagged question, or question 1, then set `$('navigator').hidden=false` and call `renderQuestion()`. Returning to question 1 and opening the review navigator is explicit, not accidental control flow.
- Consequently the model's assertions **“The user sees Next”** and **“no ... review indicator”** are factually inconsistent with the source. A different UX design could be proposed, but the tested control path does not establish a functional defect.

**Verdict:** **Negative control failed**. The model gave a confident false-positive classification on the same issue it had misclassified in its original seven-file review, even with a smaller single-file prompt, constrained task, and functioning reasoning cap. The source-level execution trace was mostly correct; the final conclusion was biased by the overlooked UI label and navigator side effect. No Training Engine change warranted on this evidence.

**Follow-up:** Positive-control code-trace test against a suspected real `configuredQuestionCount` metadata bug, requiring `app.js` plus `page.js`. Continue scoring source fidelity separately from runtime performance, and record whether conclusions match concrete behavior.

### 2026-10-13: Positive-control test, mutable configured question count (partial success)

**Goal:** Test whether the same Strata model could identify a plausible genuine state-integrity defect. A new conversation used both [app.js](https://github.com/novovictus/training-engine/blob/main/practice-test/app.js) and [page.js](https://github.com/novovictus/training-engine/blob/main/practice-test/page.js). Asked for a trace of `startNew()`, `saveCustomize()`, `restartActiveInMode()`, and `submit()`, distinguishing requested, selected, and completed-run counts; instructed to classify and propose a minimal patch only if warranted.

**Observed telemetry:**

~~~text
[strata] answering: 1558 of max 60395 tokens, 43.1 tok/s, 38 s
[strata] done: 1567 tokens in 39 s (43.0 tok/s) (stop, cancel=False), expert cache 88.4% hit (+0.1% of the routed experts over PCIe)
~~~

The request ended normally after **1,567 generated tokens** in **39 seconds**, with expert cache **88.4%**; the 60,395-token maximum is the available generation ceiling rather than a reached limit.

**Model conclusion:** `CONFIRMED BUG`, with a correct core observation: `submit()` reads `state.settings.questionCount` while `restartActiveInMode()` retains the old `active.items`, so after customization the completed result can record a count different from the setting that originally selected those items.

**Independent source verification:**

- `app.js` **lines 321–325**: `startNew()` reads `state.settings.questionCount`, selects that many (or fewer eligible) questions and constructs `active` **without** a `configuredQuestionCount` snapshot.
- `app.js` **lines 274–283, 486–489**: `openCustomize()`, `updateCustomizeSummary()`, and `clampQuestionCount()` restrict the user-specified limit to the number of eligible questions/bank size; with only four bank questions, the normal UI **cannot configure 60** as Strata's reproduction alleged.
- `app.js` **lines 308–312**: `saveCustomize()` updates `state.settings` upon **Save**. The `question-limit` `oninput` handler updates the preview/limit; Strata incorrectly claimed it mutates `state.settings` directly.
- `page.js` **lines 21–25, 135–143**: switching modes, saving, and confirming a restart reconstructs `active` with the **same question IDs and option order**, but resets responses and elapsed time. Strata's phrase “carried-over responses” was inaccurate.
- `app.js` **lines 416, 424**: `submit()` reports the **current** global `state.settings.questionCount`, not the original setting under which `active.items` was selected.
- **Valid four-question reproduction**: start with 4 configured and 4 selected; open Customize, reduce to 2, switch mode and confirm restart; new attempt keeps 4 items; submit records configured count 2 vs total presented 4. The original-model “4 questions, request 60, change to 10” scenario is invalid under the app's clamping.
- **Proposed fix not sufficient**: storing `active.items.length` in `restartActiveInMode()` conflates the number of selected questions with the originally configured count, fails to capture it at `startNew()`, and adding a new field without also teaching `sanitizeActiveAttempt()` to preserve it would lose that state across page reload/import. A proper patch would record `configuredCount` on the attempt at creation, retain it through restart and sanitization, and use that snapshot on submission; exact metadata semantics of a restarted run should be explicitly defined.

**Assessment:** **Partial success**. It detected a real mismatch but hallucinated particulars of the reproduction and provided an incomplete remediation. **Do not claim verified patch correctness or apply Strata's code unreviewed.** Combined with the negative control, this strongly suggests distinguishing sound local control-flow tracing from unreliable assumptions about wider UI state and complete fixes. No changes to the Training Engine were made.

## References

- [ASUS ROG Strix SCAR 15 (2021) specifications](https://rog.asus.com/laptops/rog-strix/2021-rog-strix-scar-15-series/spec/)
- [Strata inference engine](https://github.com/Niko1221/Strata)
- [Strata model requirements](https://github.com/Niko1221/Strata/blob/main/docs/MODELS.md)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)

Hardware details above were collected from a local Windows 11 inventory on 2026-10-12. Any model requirements, compatibility limits, and third-party performance reports should be rechecked against their source version before running experiments.
