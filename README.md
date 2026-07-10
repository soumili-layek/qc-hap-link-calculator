# QC-HAP Telemetry & Quantum FSO Link Calculator

![Status](https://img.shields.io/badge/status-active-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Platform](https://img.shields.io/badge/platform-Web%20%7C%20GitHub%20Pages-lightgrey)
![Stack](https://img.shields.io/badge/stack-HTML%20%7C%20TailwindCSS%20%7C%20JavaScript-38bdf8)
![Domain](https://img.shields.io/badge/domain-Quantum%20Information%20%7C%20Aerospace%20Comms-a78bfa)

An interactive, client-side engineering calculator for comparing **classical RF telemetry capacity** against **quantum-secure Free-Space Optical (FSO) transmission rate** for a conceptual stratospheric **Quantum Data Center High-Altitude Platform (QC-HAP)**.

This tool was developed to support systems-engineering trade studies for next-generation high-altitude platform stations (HAPS) that must simultaneously downlink classical housekeeping telemetry and distribute quantum states (e.g., for QKD — Quantum Key Distribution) to ground receiver stations.

---

## 1. Motivation & Physical Rationale

A QC-HAP carries two fundamentally different payloads that cannot share the same physical channel:

**a) Classical Telemetry (RF Link)**
Spacecraft/platform health data — attitude, power, thermal state, command & control — is inherently classical information. RF channels are mature, weather-tolerant (to a degree), and can be encoded, amplified, and re-transmitted without loss of fidelity. Classical bits *can* be copied, amplified, and error-corrected using redundancy, because they are not subject to quantum no-cloning constraints.

**b) Quantum State Distribution (FSO Link)**
Quantum states (single photons encoding qubits, e.g., via polarization or time-bin encoding) **cannot** be amplified or cloned (no-cloning theorem) without destroying the encoded information. This makes them acutely vulnerable to two things:

1. **Absorption/scattering loss** — any amplifying repeater is physically prohibited, so link margin must come entirely from source brightness, aperture gain, and atmospheric transmittance.
2. **Thermal noise contamination** — RF/microwave carriers occupy energy scales comparable to ambient thermal photon occupation at the relevant frequencies, governed by the thermodynamic relation:

$$
E_{th} = k_B T
$$

where $k_B$ is the Boltzmann constant and $T$ is the ambient brightness temperature of the background/link environment. At RF/microwave frequencies, $h\nu \ll k_B T$ at typical terrestrial/atmospheric temperatures, meaning **thermal background photons dominate**, making single-photon quantum state discrimination effectively impossible over RF carriers. Optical frequencies ($h\nu \gg k_B T$ at ambient $T$) sit in a regime where thermal photon occupation number is vanishingly small (per Planck's law / Bose-Einstein occupation statistics), which is precisely why **FSO (optical) links are mandatory** for any practical free-space single-photon quantum channel — it is the only regime where a single photon's quantum state is not swamped by blackbody background noise.

This is the core engineering rationale for the dual-link QC-HAP architecture modeled here: **RF for robust, amplifiable classical telemetry; FSO for fragile, un-amplifiable quantum state transport.**

---

## 2. Mathematical Models

### 2.1 Classical RF Capacity — Shannon–Hartley Theorem

The theoretical maximum error-free classical data rate over a bandwidth-limited, noisy RF channel is given by the Shannon–Hartley theorem:

$$
C = B \cdot \log_2 \left( 1 + \text{SNR}_{\text{linear}} \right)
$$

where:

| Symbol | Meaning | Units |
|---|---|---|
| $C$ | Channel capacity | Mbps |
| $B$ | Channel bandwidth | MHz |
| $\text{SNR}_{\text{linear}}$ | Linear signal-to-noise ratio, $= 10^{(\text{SNR}_{dB}/10)}$ | dimensionless |

**Implementation:**
```js
const rfCapacity = bandwidth * Math.log2(1 + Math.pow(10, snr_dB / 10));
```

### 2.2 Quantum FSO Effective Transmission Rate

The effective secure quantum-state (qubit) throughput of the FSO link is modeled as the source repetition rate attenuated by cumulative channel and detection efficiencies, and discounted by the effective error rate (analogous to QBER — Quantum Bit Error Rate — in QKD protocols such as BB84):

$$
R_Q = f_{\text{rep}} \cdot \eta_{\text{atm}} \cdot \eta_{\text{det}} \cdot \left(1 - \text{QBER}\right)
$$

where:

| Symbol | Meaning | Units |
|---|---|---|
| $R_Q$ | Effective secure quantum rate | M-Qubits/sec |
| $f_{\text{rep}}$ | Laser source repetition rate | MHz |
| $\eta_{\text{atm}}$ | Atmospheric transmittance efficiency | fraction (0–1) |
| $\eta_{\text{det}}$ | Single-photon detector efficiency | fraction (0–1) |
| $\text{QBER}$ | Effective quantum bit error rate | fraction (0–1) |

**Implementation:**
```js
const fsoRate = repRate * (transmittance / 100) * (detectorEff / 100) * (1 - errorRate / 100);
```

> **Note:** This model captures first-order link budget behavior. A full engineering treatment would additionally include beam divergence/pointing loss, turbulence-induced scintillation (Fried parameter $r_0$), and finite-key security corrections from QKD post-processing — intentionally simplified here for pedagogical/trade-study use.

---

## 3. Application Features

- **Dual-panel live inputs** — independent slider controls for RF and Quantum FSO link parameters.
- **Real-time recomputation** — all outputs update instantly on every slider interaction, no page reload or server round-trip.
- **Result cards** — high-contrast, color-coded readouts (cyan for classical RF, violet for quantum FSO) for quick visual comparison.
- **Comparative link budget bar** — normalized visual proportion of RF vs. quantum throughput.
- **Zero backend, zero build step** — pure static HTML/CSS/JS, deployable directly via GitHub Pages.

---

## 4. How to Run

This is a fully static, client-side single-page application. No installation, package manager, or build pipeline is required.

### Option A — Run Locally
1. Clone this repository:
```bash
   git clone https://github.com/soumili-link/qc-hap-link-calculator.git
   cd qc-hap-link-calculator
```
2. Open `index.html` directly in any modern browser (Chrome, Firefox, Edge, Safari).

### Option B — Deploy via GitHub Pages
1. Push this repository to GitHub.
2. Navigate to **Settings → Pages**.
3. Under **Source**, select the `main` branch and `/root` folder.
4. Save. GitHub will publish the app at: https://soumili-layek.github.io/qc-hap-link-calculator/
5. No build step, no dependencies to install — Tailwind CSS is loaded via CDN at runtime.

---

## 5. Repository Structure

```text
.
├── index.html   # Application: markup, styling, and embedded logic
├── README.md    # Project documentation (this file)
└── LICENSE      # MIT License
```
---

## 6. Disclaimer

This calculator is intended for **educational and systems-engineering trade-study purposes** as part of an academic project exploring stratospheric quantum communication architectures. It is a simplified first-order model and does not constitute a certified link-budget tool for mission-critical design.

---

## 7. License

Released under the [MIT License](LICENSE).
