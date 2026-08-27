# RealLight

RealLight is a reinforcement learning framework for traffic signal control under
observations limited to what an intersection surveillance camera can provide.

Each intersection is controlled by an independent agent. The agent observes
lane-level vehicle densities within a 100 m range together with aggregate flow
indicators for neighboring intersections, selects a joint phase and green
duration, and is trained with independent Proximal Policy Optimization (IPPO).
The state and the reward are computed from aggregate counts inside a prescribed
observation range rather than from exact trajectories, unrestricted queues, or
network-wide state supplied by the simulator.

Because each agent has its own observation and action dimensions, the
formulation supports three-way, four-way, and multi-leg intersections with
different lane counts and intersection-specific phase sets, without padding or
parameter sharing.

<p align="center">
  <img src="https://github.com/user-attachments/assets/f06d5228-2fed-45e1-9036-66b8b40106b2" width=500px height=300px>
  <img src="figure/seo_gu.gif" width=300px height=300px>
</p>

## Getting started

### Requirements

- Python 3.8+
- [CityFlow](https://github.com/cityflow-project/CityFlow)
- PyTorch 2.1.0+
- numpy, pyyaml, pandas

### Run

```bash
git clone https://github.com/camuslab/RealLight.git
cd RealLight
python run.py
```

Hyperparameters and the scenario to run are set in `conf.yaml`.

## Repository layout

| Path | Contents |
| --- | --- |
| `run.py` | Training and evaluation entry point |
| `env.py` | CityFlow environment wrapper |
| `conf.yaml` | Hyperparameters and scenario selection |
| `algos/reallight.py` | RealLight (IPPO) agent |
| `algos/fixed_time.py` | Fixed-time baseline |
| `data/synthetic/` | 1x3, 2x2, 3x3, and 4x4 grid scenarios |
| `data/real/` | Jinan, Hangzhou, New York, and Daejeon Seo-gu scenarios |

The Daejeon Seo-gu network is newly constructed for this work. Its geometry is
built from OpenStreetMap data, and its signal phases follow existing
time-of-day operational plans. It contains three-way, four-way, and multi-leg
intersections, one to five lanes per approach, and shared straight-left and
straight-right lanes.

## Results

Average travel time in seconds. `RealLight-100` uses the 100 m observation
range; `RealLight-max` removes the range limit.

| | Method | 1x3 | 2x2 | 3x3 | 4x4 | Jinan | Hangzhou | New York | Seo-gu |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Non-RL | Fixed-Time | 384.47 | 454.01 | 508.87 | 565.99 | 405.91 | 488.51 | - | 220.56 |
| | SOTL | 247.07 | 331.64 | 424.66 | 474.32 | 410.65 | 505.53 | - | - |
| RL | GRL | 208.21 | 239.13 | 431.43 | 523.01 | 562.91 | 598.17 | - | - |
| | CoLight | 210.01 | 312.29 | 328.70 | 397.07 | 327.62 | 337.45 | 1459.28 | - |
| | PressLight | 98.74 | 123.90 | 166.28 | 215.32 | 285.65 | 341.99 | - | - |
| | IPDALight | 88.01 | 109.66 | 146.92 | 184.54 | 255.35 | 298.99 | - | - |
| Ours | **RealLight-100** | **85.71** | **107.08** | **142.83** | **181.39** | **253.39** | **298.19** | **887.82** | **124.36** |
| | RealLight-max | 89.43 | 113.45 | 151.63 | 193.63 | 271.34 | 319.57 | 931.52 | 131.22 |

Values are means over ten evaluation runs with distinct random seeds.

Two caveats apply when reading this table. The Fixed-Time, SOTL, GRL, CoLight,
PressLight, and IPDALight values are adopted from the IPDALight benchmark and
were not rerun here, so implementation and stochastic-training differences
cannot be ruled out. Dispersion statistics and significance tests are not
reported, so the small differences in Jinan and Hangzhou should not be read as
statistically significant.

All results are produced in simulation with exact vehicle counts. Sensing error,
controller latency, and field operation are not evaluated.

## Citation

The accompanying paper is under review at IEEE Access:

> T. Eom, M. Park, S. Kim, and J. Yeo, "RealLight: Decentralized Networked
> Traffic Signal Control under Partial Observability."

## License

Released under the MIT License. See [LICENSE](LICENSE).
