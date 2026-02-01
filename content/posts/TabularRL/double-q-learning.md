---
title: "Double Q-Learning"
description: "Learn Double Q-Learning to eliminate maximization bias in value estimation"
date: 2026-02-01T10:06:00+00:00
draft: false
slug: "double-q-learning"
categories:
  - Reinforcement Learning
  - Tutorial
tags:
  - RL
  - Temporal Difference
  - Double Q-Learning
  - Off-Policy
  - Model-Free
---

Double Q-Learning addresses a fundamental problem in Q-Learning: **maximization bias**. By using two Q-tables to decouple action selection from evaluation, it produces more accurate value estimates and more stable learning.

---

## The Maximization Bias Problem

Standard Q-Learning uses the same Q-values to both select and evaluate actions:

$$Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right]$$

The problem: $\max_{a'} Q(s',a')$ **overestimates** the true value because noise in our estimates is asymmetric:

$$\mathbb{E}[\max_a Q(s,a)] \geq \max_a \mathbb{E}[Q(s,a)]$$

Consider an example: if Q-values for three actions are estimated as [5, 5, 5] when the true values are [5, 5, 5], but noise makes our estimates [4.8, 5.5, 4.7], we select action 2 with estimated value 5.5 when the true value is only 5. This **positive bias** accumulates through bootstrapping.

---

## The Double Q-Learning Solution

The key insight: use two independent Q-functions to **separate selection from evaluation**:

1. $Q_1$ **selects** the best action: $a^* = \arg\max_{a'} Q_1(s', a')$
2. $Q_2$ **evaluates** that action: $Q_2(s', a^*)$

Since $Q_1$ and $Q_2$ have independent noise, the bias is eliminated (or even slightly reversed toward underestimation).

The update alternates randomly between updating $Q_1$ or $Q_2$:

$$Q_1(s,a) \leftarrow Q_1(s,a) + \alpha \left[ r + \gamma Q_2(s', \arg\max_{a'} Q_1(s', a')) - Q_1(s,a) \right]$$

---

## Pseudocode

```
Algorithm: Double Q-Learning

Input:
    γ          - discount factor ∈ [0, 1]
    α          - learning rate ∈ (0, 1]
    ε          - exploration rate ∈ (0, 1]
    num_episodes

Output:
    Q1, Q2     - two action-value function estimates
    π          - optimal policy

Initialize:
    Q1(s,a) ← arbitrary for all s ∈ S, a ∈ A
    Q2(s,a) ← arbitrary for all s ∈ S, a ∈ A

Repeat for each episode:
    s ← initial state

    While s is not terminal:
        a ← ε-greedy action from (Q1(s,·) + Q2(s,·))
        s', r ← take action a, observe next state and reward

        With probability 0.5:
            # Update Q1 using Q2 for evaluation
            a* ← argmax_a Q1(s', a)           # Q1 selects
            Q1(s,a) ← Q1(s,a) + α · [r + γ · Q2(s', a*) - Q1(s,a)]   # Q2 evaluates
        Else:
            # Update Q2 using Q1 for evaluation
            a* ← argmax_a Q2(s', a)           # Q2 selects
            Q2(s,a) ← Q2(s,a) + α · [r + γ · Q1(s', a*) - Q2(s,a)]   # Q1 evaluates

Until num_episodes reached

π(s) ← argmax_a [Q1(s,a) + Q2(s,a)]

Return Q1, Q2, π
```

---

## Python Implementation

```python
import numpy as np
import random

def epsilon_greedy(q_values, epsilon, num_actions):
    """Select action using epsilon-greedy policy."""
    if random.random() < epsilon:
        return random.randint(0, num_actions - 1)
    else:
        return np.argmax(q_values)

def double_q_learning(env, num_episodes, gamma=0.99, alpha=0.1, epsilon=0.1):
    """
    Double Q-Learning: Off-Policy TD Control with reduced bias.

    Args:
        env: Environment with reset() and step(action) methods
        num_episodes: Number of episodes to run
        gamma: Discount factor
        alpha: Learning rate
        epsilon: Exploration rate

    Returns:
        Q1, Q2: Two action-value functions
        pi: Optimal policy
    """
    num_states = env.observation_space.n
    num_actions = env.action_space.n

    Q1 = np.zeros((num_states, num_actions))
    Q2 = np.zeros((num_states, num_actions))

    for episode in range(num_episodes):
        state = env.reset()
        done = False

        while not done:
            # Action selection uses sum of both Q-tables
            combined_q = Q1[state] + Q2[state]
            action = epsilon_greedy(combined_q, epsilon, num_actions)

            next_state, reward, done, _ = env.step(action)

            if done:
                td_target_1 = reward
                td_target_2 = reward
            else:
                # With 50% probability, update Q1 or Q2
                if random.random() < 0.5:
                    # Q1 selects, Q2 evaluates
                    best_action = np.argmax(Q1[next_state])
                    td_target = reward + gamma * Q2[next_state, best_action]
                    Q1[state, action] += alpha * (td_target - Q1[state, action])
                else:
                    # Q2 selects, Q1 evaluates
                    best_action = np.argmax(Q2[next_state])
                    td_target = reward + gamma * Q1[next_state, best_action]
                    Q2[state, action] += alpha * (td_target - Q2[state, action])

            state = next_state

    # Extract policy from combined Q-values
    combined_Q = Q1 + Q2
    pi = np.argmax(combined_Q, axis=1)

    return Q1, Q2, pi
```

---

## Key Points

### 1. Decoupling Selection and Evaluation

The core insight: by using independent estimators for selection and evaluation, we break the correlation that causes systematic overestimation.

| Step | Q-Learning | Double Q-Learning |
|------|------------|-------------------|
| Select | $\arg\max_a Q(s', a)$ | $\arg\max_a Q_1(s', a)$ |
| Evaluate | $Q(s', a^*)$ (same Q) | $Q_2(s', a^*)$ (different Q) |

### 2. Bias Comparison

| Aspect | Q-Learning | Double Q-Learning |
|--------|------------|-------------------|
| Target | $r + \gamma \max_{a'} Q(s', a')$ | $r + \gamma Q_2(s', \arg\max_{a'} Q_1(s', a'))$ |
| Bias | Overestimates | Unbiased (or slight underestimate) |
| Memory | 1 Q-table | 2 Q-tables |
| Convergence | Faster initially | More stable long-term |

### 3. Action Selection for Behavior

The behavior policy typically uses the **sum or average** of both Q-tables:

```python
combined_q = Q1[state] + Q2[state]
action = epsilon_greedy(combined_q, epsilon)
```

This provides better exploration than using either table alone.

### 4. Terminal State Handling

When $s'$ is terminal, the target is just $r$ (no bootstrap term needed).

### 5. Why Two Tables?

You might wonder: why not just use two separate experiences to estimate one table? The key is that the **same** transition $(s, a, r, s')$ is used, but the selection and evaluation are decoupled. This is more sample-efficient than collecting twice as much data.

---

## When Maximization Bias Matters

Maximization bias is most problematic when:

1. **High stochasticity:** More noise in estimates means larger bias
2. **Many actions:** More actions means more opportunities for one to be overestimated
3. **Early learning:** Estimates are noisier, bias is worse
4. **Deep networks:** Bias compounds through bootstrapping chains

This is why Double DQN (the deep RL extension) is often preferred over vanilla DQN.

---

## When to Use Double Q-Learning

**Strengths:**
- Eliminates maximization bias
- More stable value estimates
- Better long-term convergence

**Limitations:**
- Doubles memory requirements (2 Q-tables)
- Slightly slower per-update computation
- May underestimate slightly (usually not a problem)

**Best for:** Stochastic environments, problems with many actions, or when you observe Q-Learning's estimates diverging or oscillating.

---

## Connection to Deep RL

Double Q-Learning directly inspired **Double DQN** (van Hasselt et al., 2016):

- **DQN:** Uses target network for stability
- **Double DQN:** Uses online network to select, target network to evaluate

The same principle—decouple selection from evaluation—eliminates the overestimation that plagues vanilla DQN.

---

*This concludes the Tabular RL series. For function approximation and deep RL methods, see the [Deep RL](/posts/DeepRL/) series.*
