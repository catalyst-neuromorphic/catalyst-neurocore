# Catalyst Neurocore

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094) 
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18728256.svg)](https://zenodo.org/records/18728256)

We design neuromorphic chips that beat the best, but at a fraction of the cost. Our latest benchmarks managed to beat Loihi 2 on SSC (73.5% vs 69.8%) and match it on SHD (90.7% vs 90.9%). Below you may find links to our papers, cloud API (still work in progress), and other details.

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

FPGA validation: 8-core tile on AWS F2, 19/19 tests passing, 14.5K timesteps/sec, 83.3 MHz.

N3 benchmarks and paper coming soon.

---

## Validation

| Metric | Value |
|---|---|
| SDK test suite | **<!-- STAT:TEST_COUNT -->3,091<!-- /STAT --> tests** |
| Feature coverage | **<!-- STAT:FEATURES_TOTAL -->155<!-- /STAT --> total** (<!-- STAT:FEATURES_FULL -->152<!-- /STAT --> FULL, <!-- STAT:FEATURES_HW_ONLY -->3<!-- /STAT --> HW_ONLY) |
| FPGA validation (N2) | 28/28 pass (16 cores, AWS F2, Xilinx VU47P, 62.5 MHz) |
| FPGA validation (N3) | 19/19 pass (8 cores / 1 tile, AWS F2, 83.3 MHz) |
| RTL testbenches | 25 (98 scenarios, 0 failures) |
| SHD benchmark | **90.7%** (adLIF) / **<!-- STAT:SHD_FLOAT -->85.9<!-- /STAT -->%** (LIF baseline) |

---

## Benchmarks (N2)

Full benchmark suite: **[catalyst-neuromorphic/catalyst-benchmarks](https://github.com/catalyst-neuromorphic/catalyst-benchmarks)** — clone, train, deploy, reproduce.

| Benchmark | Classes | Architecture | Neuron | Float Acc | vs Competition |
|---|---|---|---|---|---|
| **SHD** | 20 | 700→1024→20 (rec) | adLIF | **90.7%** | Beats Loihi 1 (89.0%) |
| **SSC** | 35 | 700→1024→512→35 (rec) | adLIF | **73.5%** | Beats Loihi 2 (69.8%) |
| **N-MNIST** | 10 | Conv2D+LIF→10 | LIF | **99.2%** | — |
| **GSC KWS** | 12 | 40→512→12 (rec, S2S) | adLIF | **88.0%** | — |
| **DVS Gesture** | 11 | — | — | *in progress* | — |

All models have been trained with surrogate gradient BPTT on N2 architecture, then deployed to Catalyst FPGA hardware with int16 quantization. N3 benchmarks are coming soon.

```bash
# Reproduce any benchmark
git clone https://github.com/catalyst-neuromorphic/catalyst-benchmarks.git
cd catalyst-benchmarks && pip install -e .
python shd/train.py --neuron adlif --hidden 1024 --epochs 200 --device cuda:0 --amp
```

### Simulation Performance

| Backend | 1K neurons, 1K timesteps | 32K neurons, 10K timesteps |
|---|---|---|
| CPU | 0.8s | 45s |
| GPU (RTX 3080) | 0.01s | 0.4s |
| FPGA (VU47P) | 0.016s | 0.5s |

---

## Feature Parity Scorecard

Full comparison against Intel Loihi 1 and Loihi 2:

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

> **Catalyst N3** — *coming soon*
>
> 128-Core Hybrid ANN/SNN Neuromorphic SoC with Hardware Virtualization and On-Chip Continual Learning

> **Catalyst N2: Full Loihi 2 Feature Parity in an Open Neuromorphic Processor with Programmable Neuron Microcode and Cloud FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18728256.svg)](https://zenodo.org/records/18728256)

> **Catalyst N1: A 131K-Neuron Open Neuromorphic Processor with Programmable Synaptic Plasticity and FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

**[N2 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n2.pdf)** | **[N1 Paper (Zenodo)](https://zenodo.org/records/18727094)**

---

## Contact

I am more than open to hear from anyone with any interest in my work or in the general space of neuromorphic computing.

- **Email**: henry@catalyst-neuromorphic.com
- **Website**: [catalyst-neuromorphic.com](https://catalyst-neuromorphic.com)
- **Cloud API**: [catalyst-neuromorphic.com/cloud](https://catalyst-neuromorphic.com/cloud)

---

*All of this was built by one person, all 3 generations including: <!-- STAT:TEST_COUNT -->3,091<!-- /STAT --> SDK tests, 1,011+ RTL tests, N3 with 68 features, hybrid ANN/SNN, FPGA validated, and beating Loihi 2 on SSC (73.5% vs 69.8%).*

