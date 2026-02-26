# Catalyst Neurocore

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094) 
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18728256.svg)](https://zenodo.org/records/18728256)

**Neuromorphic processor architecture — 128 cores, 131K neurons, full Loihi 2 feature parity, <!-- STAT:SHD_FLOAT -->85.9<!-- /STAT -->% SHD benchmark.**

> Two generations of neuromorphic silicon. Validated on real FPGA hardware. Accessible via cloud API or dedicated dev boards.

---

## What is Catalyst?

Catalyst is a neuromorphic processor family designed for energy-efficient spiking neural network inference and on-chip learning. The architecture runs hardware-accurate LIF neuron dynamics, programmable synaptic plasticity, and dendritic computation — all at a fraction of the power consumed by conventional GPUs.

**Two ways to use it:**

- **Catalyst Cloud** — REST API for neuromorphic simulation. No hardware, no install. Free tier available.
- **FPGA Dev Boards** — Physical hardware with the Catalyst bitstream. Deploy at the edge.

---

## Architecture at a Glance

| Feature | Catalyst N1 | Catalyst N2 |
|---|---|---|
| Cores | 128 | 128 |
| Neurons/core | 1,024 (CUBA LIF) | 1,024 (programmable microcode) |
| Total neurons | 131,072 | 131,072 |
| Synapses/core | 131K (CSR compressed) | 131K (CSR compressed) |
| State precision | 24-bit fixed-point | 24-bit fixed-point |
| Dendritic compartments | Yes (tree topology) | Yes (tree topology) |
| Spike traces | Dual (pre/post) | Dual (pre/post) |
| Graded spikes | Yes (Loihi 2 feature) | Yes |
| Programmable delays | 1-63 timesteps | 1-63 timesteps |
| Learning engine | 16 registers, 14 opcodes | 16 registers, 14 opcodes |
| STDP | Yes | Yes |
| 3-factor reward learning | Yes | Yes |
| Homeostatic normalization | Yes | Yes |
| Stochastic threshold noise | Per-neuron LFSR | Per-neuron LFSR |
| Synapse encodings | Sparse, Dense, Population | Sparse, Dense, Population + Convolutional |
| Neuron models | CUBA LIF | CUBA, Izhikevich, Adaptive LIF, Sigma-Delta, Resonate-and-Fire |
| Embedded processors | Triple RV32IMF RISC-V | Triple RV32IMF RISC-V |
| **Loihi parity** | **Loihi 1** | **Loihi 2** |

> *Catalyst matches or exceeds all Loihi functional features (neuron models, learning engine, compartments, graded spikes, delays, noise, synapse formats). The "parity" designation refers to architectural feature equivalence, not identical physical specifications.*

---

## Validation

| Metric | Value |
|---|---|
| SDK test suite | **<!-- STAT:TEST_COUNT -->3,091<!-- /STAT --> tests** |
| Feature coverage | **<!-- STAT:FEATURES_TOTAL -->155<!-- /STAT --> total** (<!-- STAT:FEATURES_FULL -->152<!-- /STAT --> FULL, <!-- STAT:FEATURES_HW_ONLY -->3<!-- /STAT --> HW_ONLY) |
| FPGA validation | 28/28 pass (AWS F2, Xilinx VU47P, 62.5 MHz) |
| RTL testbenches | 25 (98 scenarios, 0 failures) |
| SHD benchmark | **<!-- STAT:SHD_FLOAT -->85.9<!-- /STAT -->%** (float) / **<!-- STAT:SHD_QUANT -->85.4<!-- /STAT -->%** (quantized) |

---

## Benchmarks

### Spiking Heidelberg Digits (SHD)

Spoken digit classification (20 classes, 700 input channels, temporal spike patterns):

| Configuration | Accuracy |
|---|---|
| Float32 weights | **<!-- STAT:SHD_FLOAT -->85.9<!-- /STAT -->%** |
| 16-bit quantized | **<!-- STAT:SHD_QUANT -->85.4<!-- /STAT -->%** |
| 8-bit quantized | 83.1% |

Trained with surrogate gradient descent, deployed on Catalyst hardware model with full fixed-point dynamics.

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

### Catalyst Cloud API

Neuromorphic compute as a service. Define a network, submit a job, get spikes back.

```python
import catalyst_cloud as cc

# Sign up (once)
account = cc.Client.signup("you@lab.edu")
print(account["api_key"])  # Save this

# Create a client
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

- **Free tier**: 10 jobs/day, 1,024 neurons, no credit card
- **Paid tiers**: Higher limits, priority compute, dedicated support
- **Interactive demo**: [HuggingFace Spaces](https://huggingface.co/spaces/Catalyst-Neuromorphic/catalyst-cloud)

[Cloud Pricing](https://catalyst-neuromorphic.com/cloud/pricing) | [API Docs](https://catalyst-neuromorphic.com/cloud/docs)

### FPGA Dev Boards

Physical hardware with the Catalyst bitstream. Planned for [Crowd Supply](https://www.crowdsupply.com/).

---

## Support Development

<p align="center">
  <a href="https://github.com/sponsors/Mr-wabbit">
    <img src="https://img.shields.io/badge/Sponsor-Support_Catalyst-EA4AAA?style=for-the-badge&logo=github-sponsors&logoColor=white" alt="Sponsor on GitHub" />
  </a>
</p>

Back independent neuromorphic silicon development via [GitHub Sponsors](https://github.com/sponsors/Mr-wabbit). Sponsors get compute credits for the Catalyst Cloud API, priority access to hardware, and early access to new features.

---

## Papers

> **Catalyst N1: A 131K-Neuron Open Neuromorphic Processor with Programmable Synaptic Plasticity and FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18727094.svg)](https://zenodo.org/records/18727094)

> **Catalyst N2: Full Loihi 2 Feature Parity in an Open Neuromorphic Processor with Programmable Neuron Microcode and Cloud FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen

**[N1 Paper (Zenodo)](https://zenodo.org/records/18727094)** | **[N2 Paper (PDF)](https://catalyst-neuromorphic.com/papers/catalyst-n2.pdf)**

---

## Contact

- **Email**: henry@catalyst-neuromorphic.com
- **Website**: [catalyst-neuromorphic.com](https://catalyst-neuromorphic.com)
- **Cloud API**: [catalyst-neuromorphic.com/cloud](https://catalyst-neuromorphic.com/cloud)

---

*Built by one person. 238 development phases. <!-- STAT:TEST_COUNT -->3,091<!-- /STAT --> tests. Loihi 2 feature parity.*
