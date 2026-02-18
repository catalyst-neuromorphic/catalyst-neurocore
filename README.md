# Catalyst Neurocore SDK

**Python SDK for the Catalyst neuromorphic processor family — 128 cores, 131K neurons, full Loihi 2 parity, 85.9% SHD benchmark.**

> *Source-available under BSL 1.1. Free for non-commercial research. Commercial use requires a paid licence.*

---

## What is this?

Neurocore is a complete software development kit for designing, compiling, and simulating spiking neural networks on the Catalyst neuromorphic architecture. It includes:

- **Network builder** — Python API to define populations, connections, learning rules, and stimuli
- **Density-weighted compiler** — maps networks to hardware with CSR synapse encoding
- **CPU simulator** — cycle-accurate, bit-exact hardware model
- **GPU simulator** — 100-1000x speedup via PyTorch sparse CSR (CUDA)
- **FPGA backend** — deploy to Xilinx Arty A7-100T or AWS F2 (VU47P)

One SDK. Three backends. Zero cloud dependencies.

---

## Architecture at a Glance

| Feature | Catalyst N1 | Catalyst N2 |
|---|---|---|
| Cores | 128 | 128 |
| Neurons/core | 1,024 (CUBA LIF) | 1,024 (CUBA LIF) |
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
| Synapse encodings | Sparse, Dense, Population | Sparse, Dense, Population |
| Embedded processors | Triple RV32IMF RISC-V | Triple RV32IMF RISC-V |
| **Loihi parity** | **Loihi 1 (10/10)** | **Loihi 2 (10/10)** |

---

## SDK Scale

| Metric | Value |
|---|---|
| Test suite | **3,091 tests** |
| Features | **155 total** (152 FULL, 3 HW_ONLY) |
| Backends | CPU, GPU (CUDA), FPGA |
| RTL testbenches | 25 (98 scenarios, 0 failures) |
| SHD benchmark | **85.9%** (float) / **85.4%** (quantized) |
| SDK version | v3.7.0 |

---

## Quick Example

```python
import neurocore as nc

# Build a network
net = nc.Network()
inp = net.population(100, params={"threshold": 1000, "leak": 10}, label="input")
hid = net.population(50, params={"threshold": 1000}, label="hidden")
net.connect(inp, hid, topology="random_sparse", p=0.3, weight=500)

# Compile and run
sim = nc.Simulator(net, backend="cpu")  # or "gpu", "fpga"
sim.inject(inp, current=5000, timesteps=range(100))
result = sim.run(1000)

# Analyse
print(result.firing_rates())
print(result.spike_trains("hidden"))
```

---

## Benchmarks

### Spiking Heidelberg Digits (SHD)

Spoken digit classification (20 classes, 700 input channels, temporal spike patterns):

| Configuration | Accuracy |
|---|---|
| Float32 weights | **85.9%** |
| 16-bit quantized | **85.4%** |
| 8-bit quantized | 83.1% |

Trained with surrogate gradient descent, deployed on Catalyst hardware model with full fixed-point dynamics.

### Performance

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

## Licensing

Neurocore is source-available under the **Business Source License 1.1 (BSL 1.1)**.

- **Non-commercial research**: Free. Clone, read, modify, publish papers.
- **Commercial use**: Requires a paid licence via GitHub Sponsors.
- **Converts to Apache 2.0**: January 2030.

### Tier Comparison

| Tier | Price | What you get |
|---|---|---|
| **Researcher** | $5/mo | Full source access, non-commercial use |
| **Startup** | $25/mo | Commercial licence for teams up to 5 |
| **Lab** | $100/mo | Commercial licence, priority issues |
| **Enterprise** | $500/mo | Unlimited commercial, direct support channel |
| **Partner** | $1,000/mo | All above + architecture consulting calls |

All tiers include access to the private `neurocore` repository with full source code, tests, RTL, and documentation.

---

## Get Access

<p align="center">
  <a href="https://github.com/sponsors/Mr-wabbit">
    <img src="https://img.shields.io/badge/Sponsor-Get_Access-EA4AAA?style=for-the-badge&logo=github-sponsors&logoColor=white" alt="Sponsor on GitHub" />
  </a>
</p>

1. **Choose a tier** at [github.com/sponsors/Mr-wabbit](https://github.com/sponsors/Mr-wabbit)
2. **Access is granted automatically** to the private [`catalyst-neuromorphic/neurocore`](https://github.com/catalyst-neuromorphic/neurocore) repository
3. **Clone and start building** — full SDK, all tests, all backends

No waiting. No approval process. Sponsor, get access, start simulating.

---

## Try Without Installing

Don't want to install anything? Use **Catalyst Cloud** — neuromorphic compute as a service:

- **REST API**: `curl` in, spikes out. No SDK install required.
- **Python client**: `pip install catalyst-cloud`
- **Interactive demo**: [HuggingFace Spaces](https://huggingface.co/spaces/mrwabbit/catalyst-cloud)
- **Free tier**: 10 jobs/day, 1K neurons, no credit card

[cloud.catalyst-neuromorphic.com](https://catalyst-neuromorphic.com/cloud) | [API Docs](https://catalyst-neuromorphic.com/cloud/docs) | [RapidAPI](https://rapidapi.com/mrwabbit/api/catalyst-neuromorphic)

---

## Paper

> **Catalyst N1: A 131K-Neuron Open Neuromorphic Processor with Programmable Synaptic Plasticity and FPGA Validation**
>
> Henry Arthur Shulayev Barnes, University of Aberdeen
>
> 128-core neuromorphic processor, 131K neurons, programmable microcode learning engine (16 registers, 14 opcodes), STDP + 3-factor reward learning, triple RV32IMF RISC-V cluster, FPGA-validated (AWS F2 VU47P at 62.5 MHz), 85.9% SHD benchmark.

Paper PDF available to sponsors in the private repository.

---

## Contact

- **Email**: henry@catalyst-neuromorphic.com
- **Website**: [catalyst-neuromorphic.com](https://catalyst-neuromorphic.com)
- **Cloud API**: [catalyst-neuromorphic.com/cloud](https://catalyst-neuromorphic.com/cloud)

---

*Built by one person. 238 development phases. 3,091 tests. Full Loihi 2 parity.*
