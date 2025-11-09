+++
date = '2025-11-03T21:11:10+08:00'
draft = true
title = 'RL Note 2: Multi-Armed Bandits'
tags = ['Course Notes', 'Reinforcement Learning']
categories = ['Learning']
+++

# Prologue
In the [last post](../r1/index.md), we introduced the basics of RL—action, reward, state, value, policy, model, etc.—so you should now have a rough picture of the field. In this post, we go deeper and discuss a classic yet still active topic: Multi-Armed Bandits (MAB). There will be more math ahead; hope you can enjoy it.

# Problem Formulation 
Multi-armed bandits are popular gambling games. There are slot machines, each called a bandit, with an unknown reward distribution that governs how much you get when pulling its arm. Your goal is to maximize the total winnings within a fixed number of pulls. You may try the game to get an intuition at https://su-my.github.io/Test-page/

Let's abstract the gambling game to a formal problem definition:
```callout {title="Definition: Stochastic Multi-Armed Bandit(MAB) Problem"}
Let $\mathcal{A} = \{ a_1, a_2, \dots, a_K \}$ be the set of arms. Pulling arm $a_k$ at round $t$ yields a reward $R_{t,k} \sim \mathcal{D}_k$, where each $\mathcal{D}_k$ is an unknown distribution with mean $\mu_k$. Over a finite horizon $T$, a policy chooses arms $(a_{i_1}, \dots, a_{i_T})$ and observes rewards $(R_{1,i_1}, \dots, R_{T,i_T})$. The objective is to maximize the expected cumulative reward $\mathbb{E}_\pi\left[\sum_{t=1}^{T} R_{t,i_t}\right]$.
```
```callout {.tip title="Notation for Rewards"}
the $R_i$ notations are random variabes for rewards. We will always use the upper cases to refer to the random variables and lower cases for scalars.
```

Furthur discussions:

## Regret
### Definitions
Regret is the primary metric for evaluating a MAB algorithm. Informally, it is the gap between the reward you would obtain by always pulling the best arm and the reward actually obtained by the algorithm. A smaller regret indicates a better algorithm.

Throughout this section we assume a stochastic environment with arm-wise reward means $\{\mu_k\}_{k=1}^K$, and we write $\mu_* = \max_{1\le k\le K} \mu_k$ for the optimal mean.

```callout {title="Definition: Realized Pseudo-Regret"}
Fix a policy $\pi$ and a sample path $\omega\in\Omega$.  
Let $A_t:\Omega\to\mathcal{A}$ denote the random arm chosen at round $t$, and write $A_t(\omega)=a_{i_t}$ for its realization along $\omega$.  
The realized pseudo-regret at horizon $T$ is
$$
\mathbf R_\pi(T,\omega)
= \sum_{t=1}^T \bigl(\mu_* - \mu(A_t(\omega))\bigr)
= T\mu_* - \sum_{t=1}^T \mu(A_t(\omega)).
$$
```

```callout {title="Definition: Random Pseudo-Regret"}
Under policy $\pi$, the (trajectory-dependent) pseudo-regret is the random variable
$$
\mathbf R_\pi(T)
= \sum_{t=1}^T \bigl(\mu_* - \mu(A_t)\bigr),
\qquad A_t:\Omega\to\mathcal{A}.
$$
It represents the regret as a random variable before taking expectation.
```

```callout {title="Definition: Expected Pseudo-Regret"}
The expected pseudo-regret of policy $\pi$ is
$$
\mathbb{E}_\pi[\mathbf R_\pi(T)]
= \int_\Omega \mathbf R_\pi(T,\omega)\,d\mathbb{P}_\pi(\omega),
$$
which measures the algorithm’s average performance under its induced randomness.
```

```callout {.tip title="Clarifying the regret definitions"}
The three regret notions above may look verbose, but they separate measurables cleanly and avoid mixing pathwise quantities with expectations[^1]. Two remarks:

1) Why **pseudo**-regret?  
Pseudo‑regret replaces realized rewards by their means. It isolates the algorithm’s decision quality from observation noise. A corresponding “real” (pathwise) regret can be defined by using realized rewards. Let $a_*\in\arg\max_k \mu_k$ and let $r_t(a)$ denote the realized reward at round $t$ if arm $a$ were pulled. Then the realized regret along $\omega$ is
$$
\mathbf R^{\mathrm{real}}_\pi(T,\omega)
= \sum_{t=1}^T \bigl(r_t(a_*) - r_t(A_t(\omega))\bigr).
$$
Taking expectation over the reward noise (with $r_t(a)\sim\mathcal D_a$) recovers the pseudo‑regret:
$$
\mathbb E\bigl[\mathbf R^{\mathrm{real}}_\pi(T,\omega)\mid A_1(\omega),\dots,A_T(\omega)\bigr]
= \mathbf R_\pi(T,\omega).
$$
In analysis we usually work with pseudo‑regret, and when context is clear we simply say “regret”.

2) Why is $A_t$ random?  
Because both the rewards and the policy may be stochastic. Rewards influence the history observed by the policy, and the policy may randomize given that history; therefore $A_t:\Omega\to\mathcal A$ is a random variable.

[^1]: Many expositions (e.g., [Introduction to Multi-Armed Bandits](https://arxiv.org/pdf/1904.07272), pp. 5–6) present regret directly under expectation and sometimes blur the distinction between pathwise and expected quantities. The split into realized, random, and expected pseudo‑regret avoids that ambiguity.
```
### Lower Bounds
