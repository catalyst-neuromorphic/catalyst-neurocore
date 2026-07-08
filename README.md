# Catalyst Neurocore

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19332513.svg)](https://zenodo.org/records/19332513)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)
[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.18728256-blue)](https://zenodo.org/records/18728256)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

## N1, and N2 are open source

The N1, and N2 neuromorphic processors are fully open source under the Apache 2.0 license. Full RTL, testbenches, SDK, and FPGA build scripts included.

**[catalyst-n1](https://github.com/catalyst-neuromorphic/catalyst-n1)** — 128 cores, 131K neurons, Loihi 1 parity. 14-opcode learning ISA, FPGA-validated on AWS F2 at 62.5 MHz.

**[catalyst-n2](https://github.com/catalyst-neuromorphic/catalyst-n2)** — 128 cores, 131K neurons, full Loihi 2 feature parity. 5 neuron models, 16-opcode programmable microcode, variable-precision weights (1-16 bit), graded spikes.

---

## What is Catalyst?

Catalyst Neuromorphic designs spiking neural network processors for low-power edge inference. Four generations designed and FPGA-validated, each extending the previous.

> **How to read the numbers**: anything marked *measured* was taken on physical FPGA hardware and is reproducible from this repo family. Anything marked *target* is an ASIC design goal with an engineering path behind it, not a measurement. The two are never mixed in one figure.

---

## Architecture at a Glance

| Feature | N1 | N2 | N3 | **N4 (measured, FPGA silicon)** | N4 ASIC (targets) |
|---|---|---|---|---|---|
| Cores | 128 | 128 | 128 (16 tiles) | **48 @ 62.5 MHz (VU47P)** | 512 |
| Neurons | 131K | 131K | 524K-1M | **3,072 fabric / 1,044 deployed** | millions (SRAM-bound) |
| Synaptic connections | — | — | — | **1.5M weighted, programmed and verified** | scaled CSR tables |
| Neuron models | 1 (CUBA LIF) | 5 | 7+ | **3 (LIF, CUBA, adLIF), bit-exact on silicon** | 3 + on-chip learning |
| Delivery fabric | — | — | — | **broadcast CSR, zero event loss (measured)** | same, multi-chip |
| Determinism | — | — | — | **bit-exact vs golden model, every timestep** | carried by construction |
| SHD on hardware | — | — | — | **90.28%, full 2,264-sample test set on chip** | — |
| Open source | Apache 2.0 | Apache 2.0 | — | — | — |

---

## Catalyst N4: Fourth Generation

N4 is a deliver-mode spiking inference processor built around weighted CSR
spike delivery, three fixed-point neuron models, and a deterministic step
semantic. The defining property is verifiability: the silicon reproduces a
mathematical golden model bit-for-bit, timestep by timestep, across the whole
deployed network.

**Measured on AWS F2 silicon (VU47P, July 2026):**

| | |
|---|---|
| Configuration | 48 cores (6 tiles x 8), 62.5 MHz |
| Deployed network | 1,024 recurrent adLIF + 20 readout units, 1.5M connections |
| State trace check | every neuron, every timestep, bit-exact vs golden model |
| SHD accuracy | **90.28% (2,044/2,264), full test set executed on chip** |
| Deployment loss | **zero** — silicon matches the trained int16 model exactly, sample for sample |
| Event integrity | zero dropped spikes across 18M+ routed events; peak queue depth 548/1024 |
| Timing | WNS +0.444 ns at 62.5 MHz |
| Edge variant | N4-Edge on Kria K26: 2.59% LUT, 0.378 W measured, 100 MHz timing met |

Two variants share the verified core:

- **N4-Edge** — SWaP-constrained deployments (UAV, interceptor, sensor-fusion
  nodes). ASIC target: 28 nm, 100-300 mW.
- **N4** — rack-scale inference. ASIC targets: 512 cores, neuron counts set by
  SRAM area, on-chip learning, RV64GC management complex, high-bandwidth
  external memory. Each target moves to the measured column when it is built
  and measured, not before.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19332513.svg)](https://zenodo.org/records/19332513)

---

## Catalyst N3: Third Generation

N3 introduces 20 architectural features beyond N2. Selected differences from Loihi 2:

| Feature | Catalyst N3 | Loihi 2 |
|---|---|---|
| **ANN INT8 MAC mode** | Per-core hybrid ANN/SNN toggle | Not available |
| **4-level memory** | L1→L2→L3(HBM)→L4(CXL), 500M+ synapses | 2-level on-chip |
| **Hardware virtualization** | NeurOS: 680+ virtual networks, TDM context switching | Not available |
| **Per-tile learning** | 16 independent accelerators (28-opcode ISA, 80 registers) | 1 global engine |
| **Hardware metaplasticity** | 3-bit consolidation per synapse | Not available |
| **Silicon proven** | FPGA-validated | Silicon-proven, in production |
| **Multi-chip scaling** | Single-die | Native multi-chip mesh (Pohoiki Springs, Hala Point) |
| **Software ecosystem** | Catalyst SDK | Lava framework, Intel toolchain |

FPGA validation: 8-core tile on AWS F2, 14,512 timesteps/sec, 62.5 MHz, 1.923 W, 262,317 LUTs. Energy efficiency: 4.04 nJ/neuron-op (3.7x improvement over N2).

**Benchmarks**: SSC **76.4%** (Loihi 2: 69.8%), SHD **91.0%** (Loihi 2: 90.9%).

[![N3 Paper](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)

---

## FPGA Validation

### AWS F2 Cloud FPGA (Xilinx VU47P)

| Processor | AFI | Validation | Frequency | Power | LUTs |
|-----------|-----|------------|-----------|-------|------|
| N4 (48-core) | `agfi-06fc2ebd50bfcb624` | bit-exact state trace + full SHD test set on chip | 62.5 MHz | — (F2 exposes no tenant power interface) | 324,737 (CL partition) |
| N3 | `agfi-0df16698ef37c59d9` | 14,512 ts/sec | 62.5 MHz | 1.923 W | 262,317 |
| N2 | `agfi-0326f183a3aa95780` | 8,690 ts/sec | 62.5 MHz | 1.913 W | 228,393 |
| N1 | `agfi-03e071bc88f912e77` | — | 62.5 MHz | 1.847 W | 189,970 |

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
| N4 silicon | **bit-exact vs golden model; SHD 90.28% on chip, full test set; zero event loss** |
| N4-Edge K26 | **2.59% LUT**, 0.378 W, 100 MHz timing met |
| ASIC projection (28 nm) | **9.3 mm², 19-38 mW** (N2) |

---

## Benchmarks

Full benchmark suite: **[catalyst-neuromorphic/catalyst-benchmarks](https://github.com/catalyst-neuromorphic/catalyst-benchmarks)**. Clone, train, deploy, reproduce.

### N4 (silicon, July 2026)

| Benchmark | Classes | Trained int16 model | On-chip (full test set) | Loihi 2 hardware |
|---|---|---|---|---|
| **SHD** | 20 | 90.28% | **90.28% (2,044/2,264)** | 90.9% |

The on-chip figure is the entire SHD test set executed on the FPGA silicon,
not a sample. Deployment cost is exactly zero: the chip's prediction matches
the trained quantised model on every one of the 2,264 samples, a consequence
of the bit-exact execution semantics. Additional benchmarks move into this
table as they are deployed and measured on hardware; software-only results
for other datasets live in the N1-N3 sections below.

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
| **SHD** | 20 | 700→512→20 (rec) | adLIF | **91.0%** | — |
| **SSC** | 35 | 700→1024→512→35 (rec) | adLIF | **72.1%** | Loihi 2: 69.8% |
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

Current work, in order: neuron capacity scale-up on the existing FPGA
(state arrays to URAM/BRAM), on-chip learning built to the same bit-exact
verification standard, RV64GC management core integration (CVA6), external
memory hierarchy, then the N4-Edge ASIC program (28 nm target, 100-300 mW).
Every roadmap item ships with a hardware measurement or it stays on the
roadmap.

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


