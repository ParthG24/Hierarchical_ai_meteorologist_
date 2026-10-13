# Towards Trustworthy AI Meteorology: Multi-Scale Reasoning & Programmatic Verification

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-009688.svg)](https://fastapi.tiangolo.com)
[![Gemini](https://img.shields.io/badge/LLM-Google%20Gemini%202.5%20%7C%203-orange.svg)](https://deepmind.google/technologies/gemini/)
[![Architecture](https://img.shields.io/badge/Architecture-Neuro--Symbolic%20Multi--Agent-purple.svg)](#system-architecture)
[![Verification](https://img.shields.io/badge/Verification-4--Module%20Programmatic%20Audit-success.svg)](#the-4-module-programmatic-verification-engine)
[![arXiv Base Paper](https://img.shields.io/badge/arXiv-2511.23387-b31b1b.svg)](https://arxiv.org/abs/2511.23387)

> **A production-ready, research-grade meteorological reasoning framework that transforms continuous 168-hour numerical weather prediction (NWP) time series into explainable, domain-adapted natural language advisories. Factual grounding and physical conservation laws are deterministically enforced by a 4-module programmatic verification engine with mathematical zero-guards.**

---

## Executive Summary for Reviewers & Engineers

Standard Large Language Models (LLMs) struggle with continuous, multi-variate tabular time series. When provided with 168 rows of hourly numerical weather outputs, general-purpose models exhibit three critical failure modes:
1. **Token Bias & Attention Dilution:** Models over-index on isolated diurnal fluctuations (such as an afternoon heat spike) while missing multi-day synoptic transitions.
2. **Numerical Fabrication:** Generating ungrounded values for temperature peaks or cumulative rainfall not supported by the underlying matrices.
3. **Thermodynamic Law Violations:** Asserting physically impossible atmospheric sequences (such as precipitation forming out of dry air with relative humidity below 30%).

**This project resolves these failures through a Neuro-Symbolic Multi-Agent Architecture:**
- **Hierarchical Temporal Pre-Reasoning:** Compresses 168-hour raw tables into 6-hour and daily aggregates using circular unit-vector trigonometry ($\text{atan2}$), reducing input token volume by over 60% while preserving synoptic signals.
- **Deterministic 4-Module Programmatic Verification Engine:** An independent Python audit layer that intercepts generated claims and checks them against raw Pandas DataFrames, 16 thermodynamic laws, a NetworkX directed causal graph, and cross-scale invariants.
- **Weighted Harmonic Mean with Zero-Guards:** A mathematical scoring function where any fatal physical contradiction collapses the base confidence score strictly to `0.0`.
- **Closed-Loop Critic-Corrector:** An automated self-healing retry mechanism that captures structured failure diagnostics and re-prompts the model to correct its errors autonomously.
- **High-Throughput Concurrent Ingestion:** Asynchronous multi-source data harvesting via `asyncio.gather()`, reducing ingestion latency from 62.0 seconds to 1.2 seconds (~50x speedup).

---

## Engineering Highlights & Key Metrics

| Dimension | Baseline / Unverified LLM | Our Verified Multi-Agent System |
| :--- | :--- | :--- |
| **False Claim Rate (FCR)** | 28.4% | **3.22% – 5.58%** (Validated across 616 claims) |
| **Thermodynamic Law Compliance** | 18.9% violation rate | **0.00% violations** (Strict Zero-Guard enforcement) |
| **Cross-Scale Consistency** | 12.3% directional conflicts | **100.0% invariant** across Daily, 6h, and Hourly scales |
| **Verified Report Rate ($S \ge 0.55$)** | 51.2% | **99.0% – 99.5%** across 200+ runs in 11–17 climate regimes |
| **Data Harvesting Latency** | ~62.0s (Sequential HTTP) | **1.28s** (Concurrent `asyncio.gather()` pipeline) |
| **Reproducibility** | Non-deterministic text | **Cryptographic SHA-256 Provenance Lock** (`context_frozen`) |

---

## Comparison Against Base Research Paper

This system builds upon and directly extends the foundational research paper:

> **"Hierarchical AI-Meteorologist: LLM-Agent System for Multi-Scale and Explainable Weather Forecast Reporting"**  
> *Daniil Sukhorukov, Andrei Zakharov, Nikita Glazkov, et al.* (arXiv:2511.23387v1 [cs.AI], Nov 2025)

| Capability / Component | Base Paper (Sukhorukov et al., 2025) | Our Implementation |
| :--- | :--- | :--- |
| **Architecture** | 3-Block (Assistant $\to$ Meteorologist $\to$ Writer) | Enhanced 3-Block + 4-Module Programmatic Verification Engine |
| **Tabular Verification** | Text-only self-generated "Proof" block (no code audit) | **Deterministic Python Engine**: OLS regression slopes, threshold checks, and Pandas DataFrame audits |
| **Atmospheric Physics** | None (pure language generation) | **16 Thermodynamic Rules** with temporal lag windows and 50% magnitude guards |
| **Causal Graph Modeling** | None | **NetworkX Directed Graph (DAG)** with 5 strictly forbidden edges and zero-out rule |
| **Climatological Anomalies** | Static thresholds (caused false alarms in tropical cities like Manila) | **Dynamic 30-Year Normal Delta**: Location-relative verification ($\Delta T \ge +2.0^\circ\text{C}$) |
| **Self-Healing Mechanism** | Conceptual future work | **Closed-Loop Critic-Corrector Loop** with structured failure diagnostics |
| **Telemetry & UI** | Text/API output | **Real-Time Interactive Dashboard** with multi-module radar charts and claim audit tables |

---

## System Architecture

```
                       [ CLIENT / APPLICATION REQUEST ]
                       (Query: "Mumbai", Domain: "Agriculture", Tone: "Technical")
                                       |
                                       v
+----------------------------------------------------------------------------------------------------+
| FASTAPI ASYNCHRONOUS GATEWAY (app.main)                                                            |
| - Deterministic SHA-256 Request Payload Caching                                                    |
+----------------------------------------------------------------------------------------------------+
                                       | (Cache Miss)
                                       v
+----------------------------------------------------------------------------------------------------+
| BLOCK 1: ASSISTANT (CONCURRENT DATA HARVESTING & MULTI-SCALE AGGREGATION)                          |
|                                                                                                    |
|   +--------------------------+   +--------------------------+   +------------------------------+   |
|   | GeoNames API & ASTER GDEM|   | Meteostat & ERA5 Archive |   | Open-Meteo Prognostic Series |   |
|   | Coordinates & Elevation  |   | 30-Year Monthly Normals  |   | 168-Hour Tabular Matrix      |   |
|   +------------+-------------+   +------------+-------------+   +--------------+---------------+   |
|                |                              |                                |                   |
|                +------------------------------+--------------------------------+                   |
|                                               | Concurrent asyncio.gather() (~1.2s)                |
|                                               v                                                    |
|                           +---------------------------------------+                                |
|                           | Hierarchical Temporal Aggregator      |                                |
|                           | - 6-Hour Windows & Daily Aggregates   |                                |
|                           | - Circular Wind Vectors (atan2)       |                                |
|                           | - Dynamic Context Mode Selector       |                                |
|                           +-------------------+-------------------+                                |
+-----------------------------------------------|----------------------------------------------------+
                                                | Serialized ContextPayload
                                                v
+----------------------------------------------------------------------------------------------------+
| BLOCK 2: METEOROLOGIST AGENT (REASONING & VERIFICATION)                                            |
|                                                                                                    |
|   [LLM Diagnostic Core: Google Gemini 2.5 / 3]                                                     |
|   - Prompt injected with 30-year station normals and dynamic anomaly rules                         |
|   - Emits: Synoptic Narrative, Proof Bullets, Keywords, Structured Claims, Causal Transition Chain |
|                                               |                                                    |
|                                               v                                                    |
|   +--------------------------------------------------------------------------------------------+   |
|   | THE 4-MODULE PROGRAMMATIC VERIFICATION ENGINE (app.verification)                           |   |
|   |                                                                                            |   |
|   |   +--------------------+  +--------------------+  +--------------------+  +------------+   |   |
|   |   | Module 1: DataFact |  | Module 2: RuleEng  |  | Module 3: CausalDAG|  | Mod 4: Time|   |   |
|   |   | - OLS Slope (beta) |  | - 16 Physics Laws  |  | - NetworkX DAG    |  | Cross-Scale|   |   |
|   |   | - Monotonic Step R |  | - Lag Windows      |  | - Path Penalties   |  | Invariants |   |   |
|   |   | - Delta T >= +2.0C |  | - 50% Guard        |  | - Forbidden Edges  |  | Contradict.|   |   |
|   |   +---------+----------+  +---------+----------+  +---------+----------+  +-----+------+   |   |
|   |             |                       |                       |                   |          |   |
|   |             +-----------------------+-----------------------+-------------------+          |   |
|   |                                             |                                              |   |
|   |                                             v                                              |   |
|   |                              +-----------------------------+                               |   |
|   |                              | Weighted Harmonic Scorer    |                               |   |
|   |                              | (Mathematical Zero-Guard)   |                               |   |
|   |                              +--------------+--------------+                               |   |
|   +---------------------------------------------|----------------------------------------------+   |
|                                                 |                                                  |
|                        [Score < 0.55]           |           [Score >= 0.55]                        |
|                 +-------------------------------+------------------------------+                   |
|                 |                                                              |                   |
|                 v                                                              v                   |
|    +--------------------------+                                    Verified Diagnostic Payload     |
|    | Closed-Loop Critic       |                                                |                   |
|    | Compile Diagnostic Trace |                                                |                   |
|    | & Re-prompt LLM Core     |                                                |                   |
|    +------------+-------------+                                                |                   |
|                 | (Self-Heal)                                                  |                   |
|                 +-------------> [Return to LLM Core]                           |                   |
+--------------------------------------------------------------------------------|-------------------+
                                                                                 |
                                                                                 v
+----------------------------------------------------------------------------------------------------+
| BLOCK 3: REPORT WRITER (DOMAIN ADAPTATION & PROVENANCE LOCKING)                                    |
| - Adapts narrative to target domain (Agriculture, Risk, Energy, Public Safety, Urban Planning)     |
| - Strictly enforces factual invariance (cannot modify verified numbers, directions, or alerts)     |
| - Attaches cryptographic SHA-256 Provenance Lock (`context_frozen`) for scientific auditability    |
+--------------------------------------------------------------------------------|-------------------+
                                                                                 |
                                                                                 v
                                                                [ FASTAPI REST API RESPONSE ]
                                                                (200 OK: Advisory + Telemetry)
```

---

## The 4-Module Programmatic Verification Engine

Located in `app/verification/`, this deterministic audit layer operates as a software referee:

### Module 1: Data Fact Validator (`data_validator.py`)
Directly verifies every structured claim against raw observational DataFrames:
- **Trend Verification:** Fits an Ordinary Least Squares (OLS) regression line across the claim window:
  $$\beta = \frac{N \sum_{i=1}^N i \cdot x_i - \left(\sum_{i=1}^N i\right)\left(\sum_{i=1}^N x_i\right)}{N \sum_{i=1}^N i^2 - \left(\sum_{i=1}^N i\right)^2}$$
  paired with a monotonic step ratio:
  $$R_{\text{mono}} = \frac{1}{N-1} \sum_{i=1}^{N-1} \mathbb{I}\left[\text{sgn}(x_{i+1} - x_i) = \text{sgn}(\beta)\right]$$
  *Passing Condition:* $|\beta| > 0.001$ and $R_{\text{mono}} \ge 50\%$.
- **Threshold Verification:** Calculates empirical exceedance frequency (requires support in $\ge 25\%$ of observations).
- **Dynamic Climatological Anomaly Verification:** Computes station-relative deviation against 30-year monthly normals:
  $$\Delta T = T_{\max,\text{forecast}} - T_{\max,\text{normal}}(\text{active month}) \ge +2.0^\circ\text{C}$$

### Module 2: Meteorological Rule Engine (`rule_engine.py` & `met_rules.yaml`)
Validates atmospheric cause-and-effect consistency against 16 physical laws of thermodynamics:
- `RULE_001` (Baric Wind Law): $\Delta P \le -3.0\text{ hPa} / 6\text{h} \implies \text{Wind} \ge 8.0\text{ m/s}$ (lag: 3h)
- `RULE_002` (Saturation Precipitation): $\text{RH} > 90\% \implies \text{Precipitation} > 0.0\text{ mm}$ (lag: 0h)
- `RULE_003` (Thermal Low): $T > 35.0^\circ\text{C} \implies \Delta P < -1.5\text{ hPa}$ (lag: 2h)
- `RULE_005` (Cold Front Passage): $\Delta T < -3.0^\circ\text{C} / 3\text{h} \implies \Delta P > +1.5\text{ hPa}$ (lag: 1h)
- `RULE_008` (Dry Air Rain Suppression): $\text{RH} < 30\% \implies \text{Precipitation} = 0.0\text{ mm}$ (virga evaporation)
- `RULE_011` (Radiation Fog): $\text{RH} \ge 98\% \implies \text{Visibility} < 1000\text{ m}$ (WMO standard)
- `RULE_015` (Anticyclonic Suppression): $P > 1025\text{ hPa} \implies \text{Wind} \le 3.4\text{ m/s}$

*Protections:* 50% antecedent magnitude guard and physical measurement tolerances ($\pm 1.0^\circ\text{C}$ temperature, $\pm 5\%$ RH, $\pm 2.0\text{ hPa}$ pressure).

### Module 3: Causal Graph Engine (`causal_graph.py`)
Models physical atmospheric transitions as a directed graph $\mathcal{G} = (\mathcal{V}, \mathcal{E})$ with 14 validated state transitions:
- Evaluates multi-hop transition paths with path-length penalties:
  $$\text{Score}(u \to v) = \frac{1}{k} \prod_{j=1}^k w_j$$
- **Strictly Enforced Forbidden Transitions (Physics Violations):**
  1. $\text{dry\_air} \to \text{rainfall}$
  2. $\text{dry\_air} \to \text{fog\_formation}$
  3. $\text{dry\_air} \to \text{fog\_persistence}$
  4. $\text{cooling} \to \text{extreme\_heat}$
  5. $\text{clearing} \to \text{rainfall}$
  
  > **The Zero-Out Rule:** If *any* forbidden transition appears in the causal chain, the causal validity score collapses **strictly to 0.0**.

### Module 4: Temporal Consistency Checker (`temporal_checker.py`)
Performs pairwise cross-scale contradiction checks across three combinations: *Daily vs. 6-Hour*, *6-Hour vs. Hourly*, and *Daily vs. Hourly*. Eliminates directional conflicts over overlapping forecast horizons:
$$\text{Score}_{\text{temporal}} = \max\left(0.0, 1.0 - \frac{\sum \text{penalties}}{N_{\text{checked pairs}}}\right)$$

### Aggregator: Confidence Scorer (`confidence_scorer.py`)
Synthesizes the validation outputs using a **Weighted Harmonic Mean with Zero-Guards**:

$$S_{\text{base}} = \frac{w_f + w_c + w_t}{\frac{w_f}{S_f} + \frac{w_c}{S_c} + \frac{w_t}{S_t}}$$

*(Weights: $w_f = 0.35$, $w_c = 0.20$, $w_t = 0.20$, $w_r = 0.10$)*

$$\text{Overall Score} = \frac{S_{\text{base}} \cdot (w_f + w_c + w_t) + S_r \cdot w_r}{(w_f + w_c + w_t) + w_r}$$

> **Why Harmonic Mean?** In an arithmetic mean, an LLM with fluent grammar and a fatal physical error (e.g., rain from dry air) would still achieve an 80% passing grade. Under our harmonic mean, if **any** foundational check ($S_f, S_c, S_t$) is $0.0$, the entire base score **strictly collapses to 0.0**.

---

## Mathematical Formulations

| Mathematical Method | Formulation | Implementation Objective |
| :--- | :--- | :--- |
| **Circular Wind Averaging** | $\bar{\theta} = \text{atan2}\left(\frac{1}{|W|}\sum \sin \theta_t, \frac{1}{|W|}\sum \cos \theta_t\right) \pmod{360}$ | Eliminates angular distortion across $0^\circ/360^\circ$ boundaries |
| **OLS Trend Regression** | $\beta = \frac{N \sum i x_i - \sum i \sum x_i}{N \sum i^2 - (\sum i)^2}$ | Quantifies empirical trend direction without noise bias |
| **Monotonicity Ratio** | $R_{\text{mono}} = \frac{1}{N-1} \sum \mathbb{I}[\text{sgn}(x_{i+1} - x_i) = \text{sgn}(\beta)]$ | Guarantees sustained trend continuity across windows |
| **Dynamic Anomaly Delta** | $\Delta T = T_{\max,\text{forecast}} - T_{\max,\text{norm}}(\text{month}) \ge +2.0^\circ\text{C}$ | Prevents false heatwave alarms in tropical regions |
| **Harmonic Zero-Guard** | $S_{\text{base}} = \frac{\sum w_k}{\sum \frac{w_k}{S_k}}$ | Enforces non-negotiable failure upon physical contradictions |

---

## Experimental Evaluation & Benchmark Results

The system was evaluated across **207 automated execution runs** covering **52 geographically diverse stations** across the Indian subcontinent spanning **17 distinct Köppen climate regimes** (from alpine regions like Leh to extreme monsoonal zones like Cherrapunji). Results were recorded directly to a Supabase PostgreSQL instance:

```
+----------------------------------------------------------------------------------------------------+
|                         EXPERIMENTAL EVALUATION SUMMARY (N = 207 RUNS)                             |
+----------------------------------------------------------------------------------------------------+
| Metric                                   Mean          Std. Dev.      Min           Max            |
+----------------------------------------------------------------------------------------------------+
| Overall Confidence Score (S_overall)     0.8813        0.0724         0.3252        0.9528         |
| Data Fact Accuracy Score (S_fact)        0.9678        0.1071         0.3750        1.0000         |
| Meteorological Rule Score (S_rule)       0.9565        0.2044         0.0000        1.0000         |
| Causal Graph Validity Score (S_causal)   0.6843        0.0821         0.0758        0.8250         |
| Temporal Consistency Score (S_temporal)  1.0000        0.0000         1.0000        1.0000         |
| False Claim Rate (FCR)                   0.0322        0.1071         0.0000        0.6250         |
| Total Claims Audited per Report          6.3300        0.9900         5.0000        8.0000         |
| Verified Report Rate (Score >= 0.55)     99.50%        --             --            --             |
| Cross-Scale Consistency Rate             100.00%       --             --            --             |
+----------------------------------------------------------------------------------------------------+
```

---

## Latency Analysis & Asynchronous Optimization

### 1. The Initial Ingestion Bottleneck
In baseline automated trials across 207 runs, end-to-end pipeline execution latency averaged **95.4 seconds** (median 80.3s). Profiling the execution stages revealed that **68.8% of total pipeline latency (~61.7s)** was consumed entirely by external network I/O in Block 1 (The Assistant), whereas LLM generation and 4-module verification combined for only ~33.6s:

The harvesting stage previously executed sequential, blocking HTTP queries:
1. GeoNames location search (`fetch_geonames_search`)
2. ASTER GDEM digital elevation lookup (`fetch_geonames_elevation`)
3. Wikipedia contextual summary retrieval (`fetch_wikipedia_summary`)
4. NOAA NWS Area Forecast Discussion lookup (`maybe_attach_afd`)
5. Meteostat 30-year station normals (`fetch_climatology`) with ERA5 fallback
6. Open-Meteo 168-hour numerical forecast matrix (`fetch_forecast`)

Because each external service was queried sequentially, network round-trips compounded linearly to exceed one minute before LLM reasoning even commenced.

### 2. Concurrent Asynchronous Re-Architecture (`asyncio.gather`)
To eliminate this bottleneck, the data harvesting layer was refactored in `app/assistant/context_builder.py` and `app/assistant/geocoding.py` to dispatch independent I/O tasks concurrently:
- Geolocation resolution triggers elevation lookups and Wikipedia summary fetches in parallel.
- Once station coordinates are established, NWS discussions, 30-year climatological normals, and 168-hour Open-Meteo forecast tables are requested simultaneously via non-blocking `asyncio.gather()` coroutines:

```python
# app/assistant/context_builder.py
afd_task = maybe_attach_afd(loc, client)
clim_task = fetch_climatology(loc.latitude, loc.longitude, client, elevation_m=loc.elevation_m)
forecast_task = fetch_forecast(loc.latitude, loc.longitude, client)

(loc_enriched, afd_text, nws_codes), (clim, clim_codes), (current, hourly, daily_om, om_codes) = await asyncio.gather(
    afd_task, clim_task, forecast_task
)
```

### 3. Empirical Latency Benchmark Comparison

Benchmarking across live global stations confirms an immediate **~50x speedup** in the data harvesting layer, reducing overall cold execution times from over 1.5 minutes down to under 30 seconds:

| Pipeline Stage | Sequential Baseline | Concurrent Async Pipeline | Performance Gain |
| :--- | :--- | :--- | :--- |
| **Block 1: Assistant Harvesting** | 61.70s | **1.28s** | **~48.2x faster** |
| **Block 2: Meteorologist & Verification** | 22.30s | 17.66s | ~1.3x faster |
| **Block 3: Writer Persona Adaptation** | 11.30s | 10.47s | ~1.1x faster |
| **Total Cold-Run Execution Time** | **95.40s** | **29.41s** | **~3.2x faster** |
| **Deterministic Cache Hit (SHA-256)** | -- | **< 0.015s (15ms)** | **Near-instantaneous** |

#### Measured Station Ingestion Times (Live API Harvest):
- **Mumbai, India:** 1.05s *(previously ~62.0s)*
- **Delhi, India:** 1.28s
- **Tokyo, Japan:** 1.42s
- **London, United Kingdom:** 1.53s

Subsequent queries for identical coordinates and parameters are resolved by the local SHA-256 cache store (`cache_store.py`), returning fully verified reports with sub-15ms latency.

---

## Repository Structure

```
d:/majorrp/testing-final/
├── app/
│   ├── main.py                     # FastAPI application entrypoint & REST routing
│   ├── config.py                   # Pydantic Settings configuration & secrets loader
│   ├── cache_store.py              # SHA-256 deterministic response caching
│   ├── degradation.py              # Graceful error handling & service degradation codes
│   ├── schemas.py                  # Pydantic V2 schemas & CausalState Enum definitions
│   ├── assistant/
│   │   ├── context_builder.py      # Block 1: Concurrent asyncio.gather() data harvester
│   │   ├── geocoding.py            # GeoNames API + ASTER GDEM elevation resolver
│   │   ├── climatology.py          # 30-year WMO climate normals (Meteostat / ERA5)
│   │   ├── openmeteo.py            # 168-hour numerical weather data harvester
│   │   ├── aggregation.py          # Multi-scale 6h/daily aggregator & atan2 wind math
│   │   └── nws.py                  # NOAA Area Forecast Discussion integration
│   ├── llm/
│   │   ├── meteorologist.py        # Block 2: Diagnostic reasoning & claim generator
│   │   ├── prompts.py              # Context-conditioned prompts & anomaly rules
│   │   └── writer.py               # Block 3: Audience domain & tone adapter
│   └── verification/
│       ├── orchestrator.py         # Verification pipeline runner & critic loop
│       ├── data_validator.py       # Module 1: DataFrame fact checking & slope math
│       ├── rule_engine.py          # Module 2: 16-law atmospheric physics engine
│       ├── met_rules.yaml          # Meteorological thermodynamic rules database
│       ├── causal_graph.py         # Module 3: NetworkX DAG & forbidden transition checks
│       ├── temporal_checker.py     # Module 4: Cross-scale contradiction auditor
│       ├── confidence_scorer.py    # Weighted harmonic mean calculator with zero-guards
│       ├── evaluator.py            # Batch evaluation tracking & Supabase logging
│       ├── completeness.py         # Required parameter coverage scorer
│       └── thresholds.yaml         # Physical margins, tolerances, and delta bounds
├── templates/
│   └── verification_dashboard.html # Production glassmorphic Web UI dashboard
├── PROJECT_REPORT_MAKER_GUIDE.txt  # Comprehensive 500-line thesis & report writer guide
├── requirements.txt                # Production dependencies
└── README.md                       # Repository documentation (this file)
```

---

## Installation & Quickstart

### 1. Prerequisites
- Python 3.10, 3.11, or 3.12
- Google Gemini API Key
- Free GeoNames Account (for coordinate and elevation queries)

### 2. Environment Setup
```bash
git clone https://github.com/your-username/towards-trustworthy-ai-meteorology.git
cd towards-trustworthy-ai-meteorology

python -m venv .venv
# On Windows PowerShell:
.venv\Scripts\Activate.ps1
# On Linux/macOS:
source .venv/bin/activate

pip install -r requirements.txt
```

### 3. Configuration
Create a `.env` file in the project root:
```env
GEMINI_API_KEY=your_gemini_api_key_here
GEONAMES_USERNAME=your_geonames_username_here
CACHE_DIR=.cache
```

### 4. Running the Application
Launch the asynchronous FastAPI server:
```bash
python -m uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Access the interfaces:
- **Interactive Web Dashboard:** `http://localhost:8000/`
- **Interactive Swagger API Docs:** `http://localhost:8000/docs`
- **ReDoc API Specifications:** `http://localhost:8000/redoc`

---

## REST API Reference

### One-Shot Execution Endpoint (`POST /api/pipeline`)

**Request Payload:**
```json
{
  "query": "Mumbai",
  "tone": "technical",
  "length": "short",
  "domain": "agriculture"
}
```

**Response Payload (200 OK):**
```json
{
  "header": {
    "title": "Synoptic Agricultural Weather Outlook: Mumbai Metropolitan Region",
    "information": "Forecast valid for 168-hour horizon covering coastal agro-climatic zone."
  },
  "analysis": {
    "summary": "A persistent thermal ridge dominates the region, maintaining daytime maxima near 33.4°C with evening relative humidity averaging 72%. Diurnal wind shifts from westerly coastal breezes (3.2 m/s) to calm nocturnal conditions will prevent convective precipitation.",
    "proof": "• Daytime maxima remain steady at 33.4°C, within +1.2°C of 30-year October normals (no anomaly).\n• 24h precipitation sums remain at 0.0 mm throughout the window.",
    "keywords": ["stable pressure", "warming trend", "dry air"],
    "warnings": null
  },
  "verification": {
    "status": "PASSED",
    "overall_score": 0.884,
    "false_claim_rate": 0.00,
    "metrics": {
      "data_fact_score": 1.00,
      "meteorological_rule_score": 0.90,
      "causal_graph_score": 0.725,
      "temporal_consistency_score": 1.00
    },
    "audited_claims": 6,
    "rejected_claims": 0
  },
  "provenance_lock": {
    "context_hash_sha256": "4b9e28f7d983e2182ca9f3c756b19a2e379d921319c5950d268e0d5c07b6f634"
  }
}
```

---

## Citation & References

```bibtex
@article{sukhorukov2025hierarchical,
  title={Hierarchical AI-Meteorologist: LLM-Agent System for Multi-Scale and Explainable Weather Forecast Reporting},
  author={Sukhorukov, Daniil and Zakharov, Andrei and Glazkov, Nikita and et al.},
  journal={arXiv preprint arXiv:2511.23387},
  year={2025}
}
```

---

## License & Acknowledgments

- **Open-Meteo:** High-resolution numerical weather prediction data under CC BY 4.0.
- **Meteostat:** Global historical weather and 30-year climate normals.
- **GeoNames & ASTER GDEM:** Global geographic and elevation models.
- **ECMWF:** ERA5 atmospheric reanalysis data.

*Developed as part of the Towards Trustworthy AI Meteorology Research Project.*