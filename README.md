# Safe Reinforcement Learning: Lunar Lander Experiment

This repository contains the experimental implementation for a study on safe reinforcement learning using the Lunar Lander environment with constraint-based safety mechanisms.

> **Note**: This README was generated with AI assistance (Claude by Anthropic).

## Overview

This experiment compares unconstrained baseline PPO (Proximal Policy Optimization) with constrained PPO using Lagrangian penalty methods to enforce safety constraints during lunar landing maneuvers.

## Environment

**SafeLunarLander** - A modified version of OpenAI Gymnasium's LunarLander-v3 environment with safety constraints:
- **Velocity constraint**: Maximum safe velocity of 1.00 m/s
- **Angle constraint**: Maximum safe angle of 0.40 radians
- **Cost limit**: 8.0 (optimized threshold)

The environment outputs a cost signal when safety thresholds are violated, enabling constraint-aware learning.

## Experimental Setup

### Configuration
- **Total timesteps**: 1,000,000
- **Evaluation interval**: 50,000 steps
- **Random seeds**: [0, 1, 2, 3, 4]
- **Algorithm**: PPO with Lagrangian penalty (constrained version)
- **Lambda learning rate**: 0.005
- **Entropy coefficient**: 0.01
- **n_steps**: 1024

### Training Approaches

1. **Baseline PPO (Unconstrained)**
   - Standard PPO without safety constraints
   - Optimizes only for reward maximization

2. **Constrained PPO (Lagrangian Penalty)**
   - Incorporates safety costs via Lagrangian multiplier
   - Balances reward maximization with constraint satisfaction

## Results

### Main Comparison (Mean ± Std over 5 seeds)

| Metric | Baseline | Constrained |
|--------|----------|-------------|
| Mean Return | 148.8 ± 137.9 | 164.8 ± 80.7 |
| Mean Cost | 8.5 ± 7.4 | 5.9 ± 4.8 |
| Violation Rate (%) | 30.4 ± 27.8 | 20.4 ± 15.3 |
| Success Rate (%) | 58.4 ± 35.8 | 59.2 ± 29.7 |

**Key Finding**: The constrained approach achieved similar success rates while reducing safety violations by 33% and improving stability (lower standard deviation).

## Extensions

### Extension A: Sparse Rewards
Investigation of exploration strategies with sparse reward signals:
- **Default entropy coefficient** (0.01): Mean success rate = 61.6%
- **High entropy coefficient** (0.02): Mean success rate = 43.6%

Results indicate that increased exploration does not necessarily improve performance in this sparse reward setting.

### Extension B: Explainability (Gradient Saliency)
Gradient-based saliency analysis reveals feature importance for policy decisions:

**Most Important Features**:
- **Baseline**: Angle (19.6), Angular velocity (14.9), Vertical velocity (9.3)
- **Constrained**: Vertical velocity (14.8), Angular velocity (11.5), Vertical position (6.2)

The constrained model shows increased sensitivity to vertical velocity, aligning with the safety constraints.

### Extension C: Domain Randomization
Robustness evaluation under environmental variations:

| Condition | Standard Model Success | Randomized Model Success |
|-----------|----------------------|-------------------------|
| Standard | 90% | 84% |
| Heavy Gravity | 54% | 86% |
| Light Gravity | 76% | 74% |
| Strong Wind | 90% | 84% |
| Turbulent | 90% | 84% |

Domain randomization significantly improves robustness to heavy gravity conditions (+32% success rate).

## Repository Structure

```
.
├── safe_rl_lunar_lander.ipynb    # Main experiment notebook
├── safe_rl_outputs/
│   ├── results/                  # CSV files with training logs
│   │   ├── baseline_logs.csv
│   │   ├── constrained_logs.csv
│   │   ├── final_comparison.csv
│   │   ├── robustness_results_optimized.csv
│   │   ├── sparse_default_logs.csv
│   │   └── sparse_explore_logs.csv
│   └── plots/                    # Visualization outputs
│       ├── baseline_vs_constrained.png
│       ├── extension_a_sparse_rewards.png
│       ├── extension_b_explainability.png
│       └── extension_c_robustness.png
└── README.md
```

## Dependencies

- Python 3.x
- NumPy 2.2.0
- Pandas 2.2.3
- PyTorch 2.7.0+cpu
- Gymnasium 1.1.1
- Stable-Baselines3 2.7.1
- Matplotlib

## Running the Experiment

Execute the Jupyter notebook `safe_rl_lunar_lander.ipynb` sequentially. The notebook includes:
1. Dependency installation and setup
2. Environment definition and testing
3. Baseline training (5 seeds)
4. Constrained training (5 seeds)
5. Results aggregation and visualization
6. Extension experiments (A, B, C)

All results are automatically saved to `~/safe_rl_outputs/`.

## Citation

If you reference this experiment, please cite the associated paper and include the repository information.

## Observations

The observation space consists of 8 features:
- `x`: Horizontal position
- `y`: Vertical position
- `vx`: Horizontal velocity
- `vy`: Vertical velocity
- `angle`: Lander orientation
- `ang_vel`: Angular velocity
- `leg1`: Left leg ground contact (0 or 1)
- `leg2`: Right leg ground contact (0 or 1)

## Action Space

Discrete(4):
- 0: Do nothing
- 1: Fire left engine
- 2: Fire main engine
- 3: Fire right engine
