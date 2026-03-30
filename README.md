# Catalyst Neurocore

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19332513.svg)](https://zenodo.org/records/19332513)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)
[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.18728256-blue)](https://zenodo.org/records/18728256)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

## N1 and N2 are open source

Both the N1 and N2 neuromorphic processors are fully open source under the Apache 2.0 license. Full RTL, testbenches, SDK, and FPGA build scripts included.

**[catalyst-n1](https://github.com/catalyst-neuromorphic/catalyst-n1)** — 128 cores, 131K neurons, Loihi 1 parity. 14-opcode learning ISA, FPGA-validated on AWS F2 at 62.5 MHz.

**[catalyst-n2](https://github.com/catalyst-neuromorphic/catalyst-n2)** — 128 cores, 131K neurons, full Loihi 2 feature parity. 5 neuron models, 16-opcode programmable microcode, variable-precision weights (1-16 bit), graded spikes.

---

## What is Catalyst?

Catalyst Neuromorphic designs spiking neural network processors for low-power edge inference. Four generations designed and FPGA-validated, each extending the previous.

> **Note on FPGA validation**: Full core counts (128 for N1-N3, 512 for N4) are the ASIC design targets. FPGA validation runs reduced configurations that prove functional correctness of the core logic, NoC routing, learning engine, and spike processing.

---

## Architecture at a Glance

| Feature | N1 | N2 | N3 | **N4** |
|---|---|---|---|---|
| Cores | 128 | 128 | 128 (16 tiles) | **512 (dual chiplet)** |
| Physical neurons | 131K | 131K | 524K-1M | **4.19M** |
| Virtual neurons | — | — | 4.2M (TDM) | **134M (32-ctx TDM)** |
| Neuron models | 1 (CUBA LIF) | 5 | 7+ | **5 + LTC + programmable 32-opcode** |
| Synapse formats | 3 | 4 | 4 | **8 (inc. KAN B-spline)** |
| Learning engine | 14-opcode | 16-opcode | 28-opcode per-tile | **32-opcode, 8-rule, 8 threads** |
| Memory | 1 level | 1 level | 4 levels | **256 KB L1 + 2 MB L2 + 640 MB S3RAM + 48 GB HBM3E** |
| Attention | — | — | — | **8-head + KV cache** |
| Spike Tensor Core | — | — | — | **16×16, 256 ops/cycle** |
| Security | — | — | — | **AES-256, Kyber, SRAM PUF** |
| Embedded processors | 3× RV32IMF | 3× RV32IMF | 4× RV32IMC | **8× RV64GC + 14 custom ops** |
| Neuroscience | — | — | Metaplasticity | **HDC, Hopfield, gap junctions, glial, sleep, neurogenesis** |
| FPGA validated | 96/96 | 28/28 | 19/19 | **126/126** |
| Open source | Apache 2.0 | Apache 2.0 | — | — |

---

## Catalyst N4: Fourth Generation

512-core dual-chiplet architecture with 4.19M physical neurons expandable to 134M virtual via 32-context TDM. Introduces the Spike Tensor Core (16x16 conditional-add at 256 ops/cycle), 8-head spiking attention with KV cache, hardware backpropagation, and a suite of neuroscience primitives (hyperdimensional computing, Hopfield associative memory, gap junctions, glial cells, oscillators, sleep consolidation, neurogenesis).

| | |
|---|---|
| Cores | 512 (2 NCCs x 32 tiles x 8 cores) |
| Neurons | 4.19M physical, 134M virtual (TDM) |
| Synapse formats | 8 (Full, Inference, Compact, Factor, Dual-Weight, Block-Sparse, Delta, KAN) |
| Learning | 32-opcode ISA, 8 rules, 8 threads, 256 registers, hardware backprop |
| Memory | 256 KB L1 + 64 KB shadow + 2 MB L2 + 640 MB S3RAM + 48 GB HBM3E |
| Security | AES-256-GCM, CRYSTALS-Kyber, SRAM PUF, lockstep, 14-step secure boot |
| Embedded | 8x RV64GC with 14 custom neuromorphic opcodes |
| FPGA | 126/126 tests, 14,983 ts/sec at 62.5 MHz (AWS F2) |
| Edge | N4-Edge on Kria K26: 2.59% LUT, 0.378 W, 100 MHz timing met |
| Patent | UK filing, 69 claims |

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19332513.svg)](https://zenodo.org/records/19332513)

---

## Catalyst N3: Third Generation

N3 introduces many new architectural features coming in at a count of 20 beyond what N2 offers, some key differentiators over Intel Loihi 2 are:

| Feature | Catalyst N3 | Loihi 2 |
|---|---|---|
| **ANN INT8 MAC mode** | Per-core hybrid ANN/SNN toggle | Not available |
| **4-level memory** | L1→L2→L3(HBM)→L4(CXL), 500M+ synapses | 2-level on-chip |
| **Hardware virtualization** | NeurOS: 680+ virtual networks, TDM context switching | Not available |
| **Per-tile learning** | 16 independent accelerators (28-opcode ISA, 80 registers) | 1 global engine |
| **Hardware metaplasticity** | 3-bit consolidation per synapse | Not available |
| **Parameter groups** | 32/core → 4× neuron density | Not available |
| **Spike compression** | DELTA/BURST/ADAPTIVE encoding | Not available |
| **Homeostatic plasticity** | Hardware firing rate tracking + synaptic scaling | Not available |
| **FACTOR compression** | Low-rank SVD synapse format, 2–8× savings | Not available |
| **Winner-Take-All** | Hardware two-pass, configurable groups/k | Not available |

FPGA validation: 8-core tile on AWS F2, 14,512 timesteps/sec, 62.5 MHz, 1.923 W, 262,317 LUTs. Energy efficiency: 4.04 nJ/neuron-op (3.7x improvement over N2).

**Benchmarks**: SSC **76.4%** (+6.6 over Loihi 2's hardware deployment), SHD **91.0%** (matching Loihi 2's 90.9%).

[![N3 Paper](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)

---

## FPGA Validation

### AWS F2 Cloud FPGA (Xilinx VU47P)

| Processor | AFI | Tests | Throughput | Frequency | Power | LUTs |
|-----------|-----|-------|------------|-----------|-------|------|
| N4 | `agfi-0e19b3c801c4ba0ff` | 126/126 | 14,983 ts/sec | 62.5 MHz | — | — |
| N4-Edge | `agfi-0e706213dcd11d40e` | 126/126 | 15,668 ts/sec | 62.5 MHz | — | — |
| N3 | `agfi-0df16698ef37c59d9` | 19/19 | 14,512 ts/sec | 62.5 MHz | 1.923 W | 262,317 |
| N2 | `agfi-0326f183a3aa95780` | 28/28 | 8,690 ts/sec | 62.5 MHz | 1.913 W | 228,393 |
| N1 | `agfi-03e071bc88f912e77` | 96/96 | — | 62.5 MHz | 1.847 W | 189,970 |

### Kria K26 Edge Characterisation (xczu5ev-sfvc784-2-i, 100 MHz target)

Each processor synthesised as a 2-core edge variant with AXI-Lite PS interface on the Xilinx Kria K26 SOM, the same Zynq UltraScale+ fabric family used in edge deployment targets.

| Processor | LUTs | LUT% | FFs | FF% | BRAM | DSP | WNS | Fmax | Power |
|-----------|------|------|-----|-----|------|-----|-----|------|-------|
| **N4-Edge** | 3,036 | 2.59% | 6,496 | 2.77% | 0 | 0 | +3.301ns | 100 MHz | 0.378W |
| **N1** | 20,109 | 17.2% | 30,847 | 13.2% | 52.5 (36.5%) | 14 (1.1%) | +0.008ns | 100 MHz | 0.642W |
| **N2** | 26,431 | 22.6% | 38,666 | 16.5% | 52.5 (36.5%) | 16 (1.3%) | -0.168ns | ~97 MHz | 0.688W |
| **N3** | 53,420 | 45.6% | 80,395 | 34.3% | 24 (16.7%) | 20 (1.6%) | -7.075ns | ~58.5 MHz | 0.867W |

N1 meets timing at 100 MHz. N2 narrowly misses (97 MHz). N3's timing gap reflects its richer feature set (68 features, hardware ECC, asynchronous NoC); pipeline register insertion is expected to close this to 80-90 MHz.

### ASIC Projections (SKY130 130nm Synthesis)

| Processor | Gate Count | Est. Power |
|-----------|-----------|------------|
| N1 core | ~71K gates | 96–191 mW |
| N2 core | ~94K gates | — |
| N3 core | ~155K gates | — |

28nm projection: N2 at ~9.3 mm² / 19–38 mW. N3 128-core at ~450 mm².

### Validation Summary

| Metric | Value |
|---|---|
| FPGA clock | **62.5 MHz** (all generations) |
| N4 FPGA tests | **126/126** at 14,983 ts/sec |
| N4-Edge K26 | **2.59% LUT**, 0.378 W, 100 MHz timing met |
| ASIC projection (28 nm) | **9.3 mm², 19-38 mW** (N2) |

---

## Benchmarks

Full benchmark suite: **[catalyst-neuromorphic/catalyst-benchmarks](https://github.com/catalyst-neuromorphic/catalyst-benchmarks)**. Clone, train, deploy, reproduce.

### N4 (Latest)

| Benchmark | Classes | Float Acc | Quantised (int16) | vs Loihi 2 | SOTA |
|---|---|---|---|---|---|
| **SHD** | 20 | **91.0%** | 90.8% (-0.2%) | 90.9% | 96.41% |
| **SSC** | 35 | **76.4%** | 76.4% (0.0%) | 69.8% | 85.98% |
| **N-MNIST** | 10 | **99.2%** | — | — | ~99.7% |
| **DVS Gesture** | 11 | **89.4%** | — | — | 99.01% |
| **GSC-12** | 12 | **88.0%** | — | — | 97.08% |

N4 matches Loihi 2 on SHD and exceeds it on SSC by 6.6 points. Quantised inference shows 0.0-0.2% degradation, confirming hardware deployment readiness. The gap to software SOTA reflects training methodology (learnable delays, architecture search), not hardware limitation.

### N3

| Benchmark | Classes | Architecture | Neuron | Float Acc | Params |
|---|---|---|---|---|---|
| **SHD** | 20 | 700→1536→20 (rec) | adLIF | **91.0%** | 3.47M |
| **SSC** | 35 | 700→1024→512→35 (rec) | adLIF | **76.4%** | 2.31M |
| **N-MNIST** | 10 | Conv2D+LIF→10 | LIF | **99.1%** | 691K |
| **GSC-12** | 12 | 40→512→12 (rec, S2S) | adLIF | **88.0%** | 291K |
| **DVS Gesture** | 11 | Deep conv+rec | adLIF | **89.0%** | ~1.2M |

### N2

| Benchmark | Classes | Architecture | Neuron | Float Acc | vs Competition |
|---|---|---|---|---|---|
| **SHD** | 20 | 700→512→20 (rec) | adLIF | **84.5%** | — |
| **SSC** | 35 | 700→1024→512→35 (rec) | adLIF | **72.1%** | Beats Loihi 2 (69.8%) |
| **N-MNIST** | 10 | Conv2D+LIF→10 | adLIF | **97.8%** | — |
| **GSC KWS** | 12 | 40→512→12 (rec, S2S) | adLIF | **88.0%** | — |
| **MIT-BIH ECG** | 5 | 187→128→5 (rec) | adLIF | **90.9%** | — |

### N1

| Benchmark | Classes | Architecture | Neuron | Float Acc | vs Competition |
|---|---|---|---|---|---|
| **SHD** | 20 | 700→1024→20 (rec) | LIF | **90.6%** | Basic LIF baseline |
| **N-MNIST** | 10 | Conv2D+LIF→10 | LIF | **99.2%** | — |
| **DVS Gesture** | 11 | Deep conv+rec | LIF | **69.7%** | — |
| **GSC-12** | 12 | 40→512→12 (rec, S2S) | LIF | **86.4%** | — |

N1 uses only basic LIF neurons (no adaptation). The accuracy demonstrates that even the simplest spiking neuron model achieves competitive performance at sufficient scale.

All models trained with surrogate gradient BPTT and deployed to Catalyst FPGA hardware with int16 quantization.

### Quantised Inference (int16 fixed-point)

| Benchmark | Float32 | Int16 | Degradation |
|-----------|---------|-------|-------------|
| **SHD** | 91.0% | **90.8%** | -0.2% |
| **SSC** | 76.4% | **76.4%** | 0.0% |
| **N-MNIST** | 99.1% | **99.2%** | +0.1% |

Quantised models match hardware representation (int16 weights, 12-bit fixed-point membrane decay) with zero or near-zero accuracy loss.

```bash
# Reproduce any benchmark
git clone https://github.com/catalyst-neuromorphic/catalyst-benchmarks.git
cd catalyst-benchmarks && pip install -e .
python shd/train.py --neuron adlif --hidden 1536 --epochs 200 --device cuda:0 --amp
```

### Simulation Performance

| Backend | 1K neurons, 1K timesteps | 32K neurons, 10K timesteps |
|---|---|---|
| CPU | 0.8s | 45s |
| GPU (RTX 3080) | 0.01s | 0.4s |
| FPGA (VU47P) | 0.016s | 0.5s |

---

## Feature Parity Scorecard (N2)

N2 targets Loihi 2 feature parity. Three capabilities requiring physical multi-chip I/O are not applicable to FPGA development boards. N3 adds 20 additional capabilities (see above).

| Category | Loihi 1 | Loihi 2 | Catalyst |
|---|---|---|---|
| LIF neuron model | Yes | Yes | Yes |
| Dendritic compartments | Yes | Yes | Yes |
| Programmable learning | Yes | Yes | Yes |
| STDP | Yes | Yes | Yes |
| 3-factor learning | Yes | Yes | Yes |
| Graded spikes | No | Yes | Yes |
| Stochastic rounding | No | Yes | Yes |
| Homeostasis | Yes | Yes | Yes |
| Programmable delays | Yes | Yes | Yes |
| Embedded processors | 3x LMT | 6x LMT | 3x RV32IMF |
| **Score** | **10/10** | **10/10** | **10/10** |

---

## Roadmap

Four generations designed and FPGA-validated. N5 specification complete. Next steps: ASIC tapeout for N4-Edge (28 nm target, 100-300 mW) and N5 RTL implementation.

For partnerships, licensing, or collaboration: **henry@catalyst-neuromorphic.com**

---

## Papers

> **Catalyst N4: A 512-Core Dual-Chiplet Neuromorphic Processor with 134M Virtual Neurons, Spike Tensor Core, and Hardware Neuroscience Primitives**
>
> Henry Arthur Shulayev Barnes, Catalyst Neuromorphic Ltd

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19332513.svg)](https://zenodo.org/records/19332513)

> **Catalyst N3: A 128-Core Hybrid Neuromorphic Processor with Hardware Virtualisation, Per-Tile Learning, and Silicon Metaplasticity**
>
> Henry Arthur Shulayev Barnes, Catalyst Neuromorphic Ltd

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)

> **Catalyst N2: Full Loihi 2 Feature Parity in an Open Neuromorphic Processor with Programmable Neuron Microcode and Cloud FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.18728256-blue)](https://zenodo.org/records/18728256)

> **Catalyst N1: A 131K-Neuron Open Neuromorphic Processor with Programmable Synaptic Plasticity and FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

**[N4 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n4.pdf)** | **[N3 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n3.pdf)** | **[N2 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n2.pdf)** | **[N1 Paper (Zenodo)](https://zenodo.org/records/18727094)**

---

## Contact

I am more than open to hear from anyone with any interest in my work or in the general space of neuromorphic computing.

- **Email**: henry@catalyst-neuromorphic.com
- **Website**: [catalyst-neuromorphic.com](https://catalyst-neuromorphic.com)


