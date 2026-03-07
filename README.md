# Catalyst Neurocore

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18728256.svg)](https://zenodo.org/records/18728256)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

We design neuromorphic chips that beat the best, but at a fraction of the cost. N3 achieves 76.4% on SSC (vs Loihi 2's 69.8%) and 91.0% on SHD (matching Loihi 2's 90.9%), with 3.7x better energy efficiency per neuron-op than N2 on identical FPGA hardware. Below you may find links to our papers, cloud API (still work in progress), and other details.

---

## What is Catalyst?

Catalyst is a company I founded in order to deal with the unsustainable amount of power that is being required every year for modern AI. So far I have made 3 designs, each better than the last, all of which have been validated on real FPGA hardware.

> **Note on FPGA validation**: The 128-core count is the architectural design target for ASIC. FPGA validation runs a subset: N2 fits 16 cores on the VU47P (1999/3576 BRAM36K), N3 fits 8 cores (1 tile). The FPGA proves functional correctness of the core logic, NoC routing, learning engine, and spike processing. Please note this is not a claim that the full 128-core design fits on a single FPGA.

**Catalyst Cloud** (still work in progress): REST API for neuromorphic simulation. Currently in early development, if you would like access feel free to email me at henry@catalyst-neuromorphic.com and I will be able to help you get access for free.

**FPGA Dev Boards** (work in progress): Physical hardware for neuromorphic simulation.

---

## Architecture at a Glance

| Feature | Catalyst N1 | Catalyst N2 | **Catalyst N3** |
|---|---|---|---|
| Cores | 128 | 128 | **128 (16 tiles)** |
| Neurons/core | 1,024 (CUBA LIF) | 1,024 (programmable) | **4,096 (24-bit) / 8,192 (8-bit)** |
| Total neurons | 131,072 | 131,072 | **524K–1M** |
| Neuron models | CUBA LIF | 5 (programmable) | **7+ (programmable + 1 custom)** |
| Weight precision | 8-bit fixed | 1–16-bit variable | **1–16-bit variable** |
| Learning engine | 14-opcode ISA | 16-register microcode | **16 × 28-opcode (per-tile)** |
| Spike traces | 2 | 5 | **6** |
| Memory hierarchy | 1 level | 1 level | **4 levels (L1→L4)** |
| ANN mode | — | — | **INT8 MAC** |
| Virtualization | — | — | **NeurOS (680+ networks)** |
| Spike compression | — | — | **DELTA/BURST/ADAPTIVE** |
| Metaplasticity | — | — | **Hardware (3-bit consolidation)** |
| Embedded processors | 3× RV32IMF | 3× RV32IMF | **4× RV32IMC + neuro ISA** |
| **Parity target** | **Loihi 1** | **Loihi 2** | **Beyond Loihi 2** |

> *N1 and N2 match Loihi 1 and Loihi 2 feature sets respectively. N3 adds capabilities that no other neuromorphic chip offers: hybrid ANN/SNN, hardware virtualization, per-tile learning accelerators, and 4-level memory hierarchy.*

---

## Catalyst N3 — Third Generation

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

FPGA validation: 8-core tile on AWS F2, 19/19 tests passing, 14,512 timesteps/sec, 62.5 MHz. Energy efficiency: 4.04 nJ/neuron-op (3.7x improvement over N2).

**Benchmarks**: SSC **76.4%** (+6.6 over Loihi 2's hardware deployment), SHD **91.0%** (matching Loihi 2's 90.9%).

[![N3 Paper](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)

---

## FPGA Validation

### AWS F2 Cloud FPGA (Xilinx VU47P)

| Processor | AFI | Tests | Pass Rate | Throughput | Frequency |
|-----------|-----|-------|-----------|------------|-----------|
| N1 | `agfi-03e071bc88f912e77` | — | PASS | — | 62.5 MHz |
| N2 | `agfi-0326f183a3aa95780` | 28/28 | 100% | 8,690 ts/sec | 62.5 MHz |
| N3 | `agfi-0df16698ef37c59d9` | 19/19 | 100% | 14,512 ts/sec | 62.5 MHz |
| N4-Edge | `agfi-0e706213dcd11d40e` | 126/126 | 100% | 15,668 ts/sec | 62.5 MHz |

### Kria K26 Edge Characterisation (xczu5ev-sfvc784-2-i, 100 MHz target)

Each processor synthesised as a 2-core edge variant with AXI-Lite PS interface on the Xilinx Kria K26 SOM — the same Zynq UltraScale+ fabric family used in edge deployment targets.

| Processor | LUTs | LUT% | FFs | FF% | BRAM | DSP | WNS | Fmax | Power |
|-----------|------|------|-----|-----|------|-----|-----|------|-------|
| **N1** | 20,109 | 17.2% | 30,847 | 13.2% | 52.5 (36.5%) | 14 (1.1%) | +0.008ns | 100 MHz | 0.642W |
| **N2** | 26,431 | 22.6% | 38,666 | 16.5% | 52.5 (36.5%) | 16 (1.3%) | -0.168ns | ~97 MHz | 0.688W |
| **N3** | 53,420 | 45.6% | 80,395 | 34.3% | 24 (16.7%) | 20 (1.6%) | -7.075ns | ~58.5 MHz | 0.867W |
| **N4-Edge** | 3,083 | 2.6% | — | — | 0 (0%) | 0 (0%) | +3.301ns | 100 MHz | 0.378W |

N1 meets timing at 100 MHz. N2 narrowly misses (97 MHz). N3's timing gap reflects its richer feature set (68 features, hardware ECC, asynchronous NoC) — pipeline register insertion is expected to close this to 80-90 MHz. N4-Edge uses zero BRAM and zero DSP — its Spike Tensor Core operates on binary spikes using conditional-add logic, leaving all dedicated blocks free for sensor interfaces.

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
| SDK test suite (N2) | **<!-- STAT:TEST_COUNT -->3,091<!-- /STAT --> tests** |
| N3 simulation tests | **1,011+** |
| Feature coverage | **<!-- STAT:FEATURES_TOTAL -->155<!-- /STAT --> total** (<!-- STAT:FEATURES_FULL -->152<!-- /STAT --> FULL, <!-- STAT:FEATURES_HW_ONLY -->3<!-- /STAT --> HW_ONLY) |
| RTL testbenches | 25 (98 scenarios, 0 failures) |
| Patents filed | N1 (2602902.6), N2 (2603866.1), N3 (filed 2 Mar 2026), N4 (filed 5 Mar 2026) |

---

## Benchmarks

Full benchmark suite: **[catalyst-neuromorphic/catalyst-benchmarks](https://github.com/catalyst-neuromorphic/catalyst-benchmarks)** — clone, train, deploy, reproduce.

### N3 (Latest)

| Benchmark | Classes | Architecture | Neuron | Float Acc | Params |
|---|---|---|---|---|---|
| **SHD** | 20 | 700→1536→20 (rec) | adLIF | **91.0%** | 3.47M |
| **SSC** | 35 | 700→1024→512→35 (rec) | adLIF | **76.4%** | 2.31M |
| **N-MNIST** | 10 | Conv2D+LIF→10 | LIF | **99.1%** | 691K |
| **GSC-12** | 12 | 40→512→12 (rec, S2S) | adLIF | **88.0%** | 291K |
| **DVS Gesture** | 11 | Deep conv+rec | adLIF | **89.0%** | ~1.2M |

All N3 models use adaptive LIF neurons with surrogate gradient BPTT and cosine LR scheduling.

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

N1 uses only basic LIF neurons (no adaptation) — the accuracy demonstrates that even the simplest spiking neuron model achieves competitive performance at sufficient scale.

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

N2 matches every documented Loihi 2 feature. N3 goes beyond with 20 additional capabilities (see above).

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

## Try It Now

### Catalyst Cloud API (Early Development)

Neuromorphic compute as a service. Define a network, submit a job, get spikes back.

```python
import catalyst_cloud as cc

# Create a client (email henry@catalyst-neuromorphic.com for a free key)
client = cc.Client("cn_live_...")

# Define a network
net = client.create_network(
    populations=[
        {"label": "input", "size": 100, "params": {"threshold": 1000}},
        {"label": "hidden", "size": 50},
    ],
    connections=[
        {"source": "input", "target": "hidden", "topology": "random_sparse",
         "weight": 500, "p": 0.3},
    ],
)

# Run simulation (blocking)
result = client.simulate(
    network_id=net["network_id"],
    timesteps=1000,
    stimuli=[{"population": "input", "current": 5000}],
)

print(result["result"]["firing_rates"])
```

```bash
pip install catalyst-cloud
```

- **Interactive demo**: [HuggingFace Spaces](https://huggingface.co/spaces/Catalyst-Neuromorphic/catalyst-cloud)

### FPGA Dev Boards

Physical hardware with the Catalyst bitstream. Planned for [Crowd Supply](https://www.crowdsupply.com/).

---

## Roadmap & Tapeout

Our long-term goal is to tape out Catalyst as physical silicon. As the architectures are FPGA validated and the RTL is ready all that would be needed now is funding for a shuttle run. TSMC 28nm HPC+ would be the target for N3 (128 cores & ~450 mm² die).

If you are interested in partnering, collaborating, or have any other inquiries, please reach out to **henry@catalyst-neuromorphic.com**

<p align="center">
  <a href="https://github.com/sponsors/Mr-wabbit">
    <img src="https://img.shields.io/badge/Sponsor-Support_Catalyst-EA4AAA?style=for-the-badge&logo=github-sponsors&logoColor=white" alt="Sponsor on GitHub" />
  </a>
</p>

You can also back development directly via [GitHub Sponsors](https://github.com/sponsors/Mr-wabbit).

---

## Papers

> **Catalyst N3: A 128-Core Hybrid Neuromorphic Processor with Hardware Virtualisation, Per-Tile Learning, and Silicon Metaplasticity**
>
> Henry Arthur Shulayev Barnes, Catalyst Neuromorphic Ltd

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18881283.svg)](https://zenodo.org/records/18881283)

> **Catalyst N2: Full Loihi 2 Feature Parity in an Open Neuromorphic Processor with Programmable Neuron Microcode and Cloud FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18728256.svg)](https://zenodo.org/records/18728256)

> **Catalyst N1: A 131K-Neuron Open Neuromorphic Processor with Programmable Synaptic Plasticity and FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

**[N3 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n3.pdf)** | **[N2 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n2.pdf)** | **[N1 Paper (Zenodo)](https://zenodo.org/records/18727094)**

---

## Contact

I am more than open to hear from anyone with any interest in my work or in the general space of neuromorphic computing.

- **Email**: henry@catalyst-neuromorphic.com
- **Website**: [catalyst-neuromorphic.com](https://catalyst-neuromorphic.com)
- **Cloud API**: [catalyst-neuromorphic.com/cloud](https://catalyst-neuromorphic.com/cloud)

---

*All of this was built by one person — 3 processor generations, <!-- STAT:TEST_COUNT -->3,091<!-- /STAT --> SDK tests, 1,011+ N3 RTL tests, 68 hardware features, hybrid ANN/SNN, all FPGA validated on AWS F2 and Kria K26, 4 patents filed, 3 papers published, and benchmark results competitive with Intel Loihi 2.*

