---
title: "Deep Learning Optimizers Explained: SGD vs Adam vs AdamW"
description: "A practical, intuition-first guide to deep learning optimizers—SGD, Adam, and AdamW—with comparisons and a decision flowchart."
date: 2025-01-18
categories:
  - Deep Learning
  - Machine Learning
tags:
  - optimization
  - sgd
  - adam
  - adamw
  - deep learning
  - neural networks
draft: false
---

Optimization algorithms are the engines of deep learning. Their job is simple in principle but tricky in practice: minimize a loss function **L(θ)** by updating parameters **θ** efficiently.

The optimizer you choose strongly influences **convergence speed, training stability, and final generalization**.

---

## 1. Stochastic Gradient Descent (SGD)

Stochastic Gradient Descent updates parameters using a single sample or a mini-batch rather than the full dataset.

### Update Rule

$$\theta_{t+1} = \theta_t - \eta \nabla L(\theta_t)$$

- **η**: learning rate  
- **∇L(θₜ)**: gradient of the loss at step *t*

### Intuition

Imagine walking down a foggy mountain using only the local slope beneath your feet. Each step is cheap, noisy, and imperfect—but over time, you trend downward.

**Pros**
- Extremely simple and memory efficient  
- Gradient noise can help escape shallow local minima  

**Cons**
- Slow convergence  
- Oscillations in narrow or ill-conditioned valleys  

---

## 2. Momentum & AdaGrad

Before jumping to modern optimizers, two key ideas reshaped SGD: **inertia** and **adaptive learning rates**.

### Momentum: Adding Inertia

Momentum accelerates SGD by accumulating velocity in directions of consistent descent.

$$v_t = \beta v_{t-1} + \eta \nabla L(\theta_t)$$
$$\theta_{t+1} = \theta_t - v_t$$

🔑 **Key idea:** Momentum damps oscillations and builds speed along stable descent directions, much like a heavy ball rolling downhill.

### AdaGrad: Per-Parameter Learning Rates

AdaGrad adapts the learning rate for each parameter based on the history of squared gradients.

$$G_t = G_{t-1} + (\nabla L(\theta_t))^2$$
$$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{G_t + \epsilon}} \cdot \nabla L(\theta_t)$$

**Works well for**
- Sparse features  
- NLP and recommender systems  

**Limitation**
- Learning rates shrink monotonically and can become too small to make progress

---

## 3. RMSProp

RMSProp fixes AdaGrad's overly aggressive decay by using an exponentially weighted moving average of squared gradients.

$$s_t = \beta s_{t-1} + (1 - \beta)(\nabla L(\theta_t))^2$$
$$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{s_t + \epsilon}} \cdot \nabla L(\theta_t)$$

**Common use cases**
- Reinforcement learning  
- Recurrent neural networks  
- Non-stationary or noisy objectives  

---

## 4. Adam (Adaptive Moment Estimation)

Adam combines the best ideas of **Momentum** and **RMSProp** by tracking both first and second moments of the gradients.

$$m_t = \beta_1 m_{t-1} + (1 - \beta_1)\nabla L(\theta_t)$$
$$v_t = \beta_2 v_{t-1} + (1 - \beta_2)(\nabla L(\theta_t))^2$$

Bias correction:

$$\hat{m}_t = \frac{m_t}{1 - \beta_1^t}, \quad \hat{v}_t = \frac{v_t}{1 - \beta_2^t}$$

Update rule:

$$\theta_{t+1} = \theta_t - \eta \cdot \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon}$$

⚠️ **Practical note:** Adam converges quickly and is easy to tune, but it can sometimes lead to worse generalization compared to SGD-based methods.

---

## 5. AdamW: Decoupled Weight Decay

AdamW fixes a subtle but important issue in Adam: weight decay should not be mixed with adaptive gradient scaling.

$$\theta_{t+1} = \theta_t - \eta \cdot \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon} - \eta \lambda \theta_t$$

By decoupling weight decay from the gradient update, AdamW provides more predictable regularization.

**Best suited for**
- Transformers and LLMs  
- Vision Transformers (ViTs)  
- Large-scale foundation models  

---

## Optimizer Comparison (High-Level Insight)

Adam and AdamW typically converge the fastest and most smoothly.

SGD with momentum converges more slowly but often reaches solutions that generalize better.

Vanilla SGD exhibits high variance early in training and usually benefits from momentum or scheduling.

---

## Choosing the Right Optimizer

{{< mermaid >}}
flowchart TD
    A[Start] --> B{Very large model?<br/>Transformers / LLMs}
    B -->|Yes| C[AdamW]
    B -->|No| D{Sparse features?}
    D -->|Yes| E[AdaGrad / Adam]
    D -->|No| F{Non-stationary or RL?}
    F -->|Yes| G[RMSProp / Adam]
    F -->|No| H{Generalization priority?}
    H -->|Yes| I[SGD + Momentum]
    H -->|No| J[Adam]
{{< /mermaid >}}

---

## Quick Optimizer Cheat Sheet

- **AdamW** → Transformers, LLMs, ViTs
- **Adam** → Rapid prototyping and baseline training
- **SGD + Momentum** → Strongest generalization
- **RMSProp** → Reinforcement learning
- **AdaGrad** → Sparse or high-dimensional data

---

## Final Thoughts

Optimization is not just about speed. It shapes **training stability, robustness to noise, and how well a model generalizes**.

A practical workflow that works surprisingly often:

1. Start with **AdamW**
2. Tune learning rate and weight decay
3. Switch to **SGD + Momentum** for final training if generalization matters most

The optimizer is not just a tool—it's a bias you impose on learning.

