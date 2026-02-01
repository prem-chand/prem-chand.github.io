---
title: "Tabular Reinforcement Learning: Classical Methods"
description: "A comprehensive guide to foundational RL algorithms including Dynamic Programming, Monte Carlo, and Temporal Difference methods"
date: 2026-02-01T10:00:00+00:00
draft: false
slug: "tabular-rl"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Dynamic Programming
  - Monte Carlo
  - Temporal Difference
  - Q-Learning
  - SARSA
  - Machine Learning
---

Tabular Reinforcement Learning represents the foundation of modern RL algorithms. These methods work with finite state and action spaces, storing value estimates in tables (hence "tabular"). Understanding these algorithms is essential before diving into function approximation and deep RL.

This series covers six fundamental algorithms, progressing from model-based to model-free approaches.

---

## The Landscape of Tabular RL

```
                    Tabular RL Methods
                           │
           ┌───────────────┴───────────────┐
           │                               │
    Model-Based                      Model-Free
  (Requires P, R)                 (Learns from experience)
           │                               │
    ┌──────┴──────┐              ┌─────────┴─────────┐
    │             │              │                   │
  Value       Policy          Monte              Temporal
 Iteration   Iteration        Carlo              Difference
                                │                    │
                           First-Visit         ┌─────┴─────┐
                           MC Control          │           │
                                            On-Policy   Off-Policy
                                            (SARSA)    (Q-Learning)
                                                            │
                                                      Double Q-Learning
```

---

## Dynamic Programming Methods

These methods require complete knowledge of the environment dynamics (transition probabilities and rewards). They solve the Bellman equations exactly.

### [Value Iteration](value-iteration)

**The Idea:** Iteratively apply the Bellman optimality operator until the value function converges, then extract the optimal policy.

| Aspect | Details |
|--------|---------|
| Type | Model-based |
| Updates | Synchronous sweeps over all states |
| Complexity | $O(\|S\|^2\|A\|)$ per iteration |
| Convergence | Guaranteed (contraction mapping) |

**When to use:** When you have the full MDP model and can afford to iterate over all states.

---

### [Policy Iteration](policy-iteration)

**The Idea:** Alternate between (1) evaluating the current policy and (2) improving it greedily. Converges to optimality in finite steps.

| Aspect | Details |
|--------|---------|
| Type | Model-based |
| Updates | Policy evaluation + improvement cycles |
| Complexity | Higher per iteration, but fewer iterations |
| Convergence | Guaranteed in at most $\|A\|^{\|S\|}$ iterations |

**When to use:** When you need exact policy evaluation or have a small state space.

---

## Monte Carlo Methods

Monte Carlo methods learn from complete episodes of experience. No model required—just sample trajectories.

### [On-Policy First-Visit Monte Carlo Control](on-policy-monte-carlo-control)

**The Idea:** Run episodes using an $\varepsilon$-greedy policy, compute returns, and update Q-values based on first visits to state-action pairs.

| Aspect | Details |
|--------|---------|
| Type | Model-free, on-policy |
| Updates | End of each episode |
| Bias | None (uses true returns) |
| Variance | High |

**When to use:** Episodic tasks where you can wait for complete trajectories. Good for problems where bootstrapping introduces too much bias.

---

## Temporal Difference Methods

TD methods combine the best of DP and MC: they learn from experience (like MC) but update estimates based on other estimates (bootstrapping, like DP).

### [SARSA](sarsa)

**The Idea:** Update Q-values using the TD error with the *actual* next action taken by the policy.

$$Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma Q(s',a') - Q(s,a) \right]$$

| Aspect | Details |
|--------|---------|
| Type | Model-free, on-policy |
| Updates | Every step |
| Learns | $Q^{\pi_\varepsilon}$ (policy being followed) |
| Name | State-Action-Reward-State-Action |

**When to use:** When you want the learned policy to account for exploration. Safer in environments with cliffs or penalties.

---

### [Q-Learning](q-learning)

**The Idea:** Update Q-values using the maximum over next actions, regardless of the action actually taken.

$$Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right]$$

| Aspect | Details |
|--------|---------|
| Type | Model-free, off-policy |
| Updates | Every step |
| Learns | $Q^*$ (optimal policy directly) |
| Issue | Maximization bias |

**When to use:** When you want to learn the optimal policy while exploring with a different behavior policy.

---

### [Double Q-Learning](double-q-learning)

**The Idea:** Use two Q-tables to decouple action selection from evaluation, eliminating maximization bias.

$$Q_1(s,a) \leftarrow Q_1(s,a) + \alpha \left[ r + \gamma Q_2(s', \arg\max_{a'} Q_1(s',a')) - Q_1(s,a) \right]$$

| Aspect | Details |
|--------|---------|
| Type | Model-free, off-policy |
| Updates | Every step (alternating Q-tables) |
| Learns | $Q^*$ (with reduced bias) |
| Advantage | Unbiased value estimates |

**When to use:** When Q-learning overestimates values (common in stochastic environments).

---

## Quick Comparison

| Algorithm | Model | Policy | Bias | Variance | Update Frequency |
|-----------|-------|--------|------|----------|-----------------|
| Value Iteration | Required | — | None | None | Sync sweeps |
| Policy Iteration | Required | — | None | None | Sync sweeps |
| MC Control | Free | On | None | High | Episode end |
| SARSA | Free | On | Bootstrap | Medium | Every step |
| Q-Learning | Free | Off | Bootstrap + Max | Medium | Every step |
| Double Q-Learning | Free | Off | Bootstrap | Medium | Every step |

---

## Learning Path

I recommend studying these algorithms in order:

1. **Value Iteration** — Understand the Bellman optimality equation
2. **Policy Iteration** — See how evaluation and improvement interact
3. **Monte Carlo Control** — Transition to model-free learning
4. **SARSA** — Learn bootstrapping and TD errors
5. **Q-Learning** — Understand off-policy learning
6. **Double Q-Learning** — Appreciate the subtleties of maximization bias

Each article includes:
- Intuitive explanation of the core idea
- Formal pseudocode
- Python implementation
- Key theoretical insights
- Comparison with related methods

---

## Prerequisites

To get the most from this series, you should be comfortable with:

- **Markov Decision Processes (MDPs):** States, actions, transitions, rewards
- **Bellman Equations:** Value functions, optimality conditions
- **Basic Probability:** Expected values, sampling

---

*This series covers tabular methods. For function approximation and deep RL, see the [Deep RL](/posts/DeepRL/) series.*
