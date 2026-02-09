# Latent State Inference Benchmark — Pilot (free demo)

This pack contains **synthetic time-series with known ground-truth latent state** for evaluating inference algorithms (filters, particle methods, neural estimators) under partial observability, noise, delay, and regime changes.

- **Sequences:** 10
- **Steps per sequence:** 200
- **Product:** pilot

## What this demo shows

- **Ground truth matters:** Each sequence includes hidden state and regimes so you can measure inference error and regime detection.
- **Baselines can fail:** The plots show event-relevant failures—(1) **model mismatch** (wrong-model KF drifts while PF tracks), (2) **shock-event miss** (shock-blind model misses jumps/spikes), and (3) **overconfident uncertainty** (KF bands can miss truth).
- **Noise and difficulty:** Performance degrades as observation noise increases; the full benchmark spans easy to hard for systematic evaluation.
- **Reproducibility:** Every run has seeds and config in metadata; the full pack can be regenerated at scale for papers and product validation.

## Contents

| Path | Description |
|------|-------------|
| `data/` | One CSV per sequence: `seq_XXXXXX_data.csv` |
| `metadata/` | One JSON per sequence: full config and seeds for reproducibility |
| `metrics_summary.csv` | One row per sequence: baseline metrics (KF, EKF, PF, NN) and conditions |
| `PACK_MANIFEST.json` | Pack-level command, schema, and generated timestamp |
| `plots/` | Illustrative PNGs (trajectory, noise escalation, failure modes) |

## Data CSV columns

Each `data/seq_XXXXXX_data.csv` has:

- **time** — Simulation time (starts at 0, step = `dt` in metadata).
- **obs_0**, **obs_1** (if present) — Noisy observations; may contain `nan` where missing or delayed.
- **x_0**, **x_1**, … — **Ground-truth latent state** (for evaluation only; treat as hidden when benchmarking).
- **regime** — Regime label per step: `0` = STABLE, `1` = GRADUAL_DRIFT, `2` = ABRUPT_JUMP, `3` = TRANSIENT_SHOCK.

Row count = num_timesteps + 1.

## Metrics (metrics_summary.csv)

Each row is one sequence. Key columns:

- **dynamics_type** — LinearSDE, DampedOscillatorODE, or RegimeAwareSDE.
- **observation_noise_std**, **delay_steps**, **missing_prob** — Observation conditions.
- **rmse_kf**, **rmse_ekf**, **rmse_pf**, **rmse_nn** — Latent state RMSE for Kalman filter, extended KF, particle filter, simple NN.
- **false_conf_kf**, **calibration_kf** — Uncertainty quality (KF); empty for non-linear dynamics.
- **regime_delay_mean** — Regime change detection delay (reference).

Use these to compare your method against the baselines or to slice by difficulty (noise, missing, delay).

## Reproducibility

- **metadata/seq_XXXXXX_metadata.json** — Seed, dynamics type, dimensions, noise, missing, delay. Use with the same generator to reproduce the sequence.
- **PACK_MANIFEST.json** — Exact command and schema for the whole pack.

## Plots

- `example_trajectory_latent_vs_observed.png` — Example latent state vs observation.
- `noise_escalation_curves.png` — Performance degrades with noise; full benchmark spans easy to hard for systematic evaluation.
- `failure_mode_false_confidence_kf.png` — **Failure mode:** KF uncertainty bands (±1σ) can miss the true state (overconfidence); the benchmark exposes this.
- `failure_mode_model_mismatch_kf_vs_pf.png` — **Failure mode:** Wrong-model KF drifts; PF (correct model) tracks. Benchmark exposes robustness to model mismatch.
- `failure_mode_event_shock_miss_kf.png` — **Failure mode:** Shock-blind model misses abrupt jumps and volatility spikes (market-style event risk).

## Why the full benchmark?

This pilot shows **what** the benchmark measures (latent recovery, uncertainty quality, noise/delay/missing effects) and **that** standard baselines can fail (e.g. overconfident KF). To stress-test your method and fill the market gap—no other benchmark offers ground-truth latent state across this range—you need coverage at scale:

| | Pilot (this pack) | Full benchmark (paid) |
|--|-------------------|------------------------|
| **Sequences** | 10 | 1000+ |
| **Steps/sequence** | 200 | 1000–2000 |
| **State dimension** | 2 | 2–5 |
| **Dynamics** | LinearSDE, Oscillator | + Regime-aware SDE, full sweep |
| **Noise / missing / delay** | Limited range | 0.01–0.5 noise, 0–20% missing, 0–5 step delay |
| **Use case** | Try the format, see metrics | Rigorous evaluation, papers, product validation |

The **full benchmark** is available as a paid product. Contact the maintainers for access.

## Citation / license

If you use this benchmark in research or products, please cite the repository and/or the benchmark specification. See the project repository for license terms.
