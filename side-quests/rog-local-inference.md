# ROG Local Inference

**Status:** Experimental Linux installation complete; Strata Coder model download underway as of 2026-10-13  
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

\`df -h /\` showed 228 GiB filesystem size, 19 GiB used, **197 GiB free (9% used)**. Linux mounts and loads from the 960 EVO, and the Windows partitions were not mounted. Windows restart/boot test remains outstanding.

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

Kernel output repeated \`nvme nvme0: 7/0/0 default/read/poll queues\` around 09:15, 09:51, 10:07, 10:38 and 10:43. Expanded context showed those lines during **suspend/resume** sequences, not during demonstrated NVMe read faults. ACPI/NVIDIA notifier warnings were also observed during resumes. No new ext4 I/O failures were shown in the examined excerpts. This does not rehabilitate the damaged SSD; it simply describes what was and was not observed.

### 2026-10-13: Strata setup and model download

Installed \`git\` and cloned [Niko1221/Strata](https://github.com/Niko1221/Strata), then ran:

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
| Quantization | Coder \`IQ1_M\` | Installer: ~58 GB download; ~23 GB RAM requirement |
| Memory placement | Low-RAM mode | Installer estimated ~47% of experts on the GPU |
| Context limit | 64K | Recommended for detected 16 GB GPU |
| KV cache | 8-bit | Baseline matching Strata-published comparison format |
| Vision | Off | Avoids extra model and ~1.4 GB VRAM reservation |
| Experimental speed/refusal-direction projection | Off | Keep normal model behavior for baseline |
| Power for future measurements | ASUS barrel adapter | Avoid USB-C power-limit confound |

**Build issue:** Strata v0.1.43.1's prebuilt inference engine URL returned HTTP 404; the "latest" binary fallback also returned 404. The installer therefore proposed a **local build**, requiring CUDA Toolkit 13.0 and build dependencies (~8-10 GB estimated). Operator accepted. The next prompt (model download) appeared in **under five minutes**; exact build logs, \`nvcc\` version, and binary hash have not yet been recorded. The installer reported a llama.cpp source checkout at **\`3cf0325\`**.

**Current stopping point (Step 5):** Downloading model files under:

~~~text
/home/rog/Strata-data/models/coder-IQ1_M/
  Qwen3.8-Flash-Next-GSQ-RCO-IQ1_M-00001-of-00002.gguf
  Qwen3.8-Flash-Next-GSQ-RCO-IQ1_M-00002-of-00002.gguf
~~~

**Download completion, successful model load, inference correctness, GPU runtime allocation, token throughput, and thermal/PCIe measurements remain unverified.**

**Next steps:** Let model files finish downloading; check for download and storage errors; start a text generation smoke test; record peak VRAM/RAM/SSD metrics; run Strata's built-in benchmark on the 960 EVO for exploratory results; later reproduce on 970 EVO Plus before treating results as reliable. Benchmark 4-bit only with a supported configuration and document RAM prerequisites.


## References

- [ASUS ROG Strix SCAR 15 (2021) specifications](https://rog.asus.com/laptops/rog-strix/2021-rog-strix-scar-15-series/spec/)
- [Strata inference engine](https://github.com/Niko1221/Strata)
- [Strata model requirements](https://github.com/Niko1221/Strata/blob/main/docs/MODELS.md)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)

Hardware details above were collected from a local Windows 11 inventory on 2026-10-12. Any model requirements, compatibility limits, and third-party performance reports should be rechecked against their source version before running experiments.
