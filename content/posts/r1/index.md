+++
date = '2025-10-28T15:23:52+08:00'
draft = true
title = 'RL Note 1: Basics'
categories = ['Course Notes', 'Reinforcement Learning']
+++

# Prologue
It’s been a while since I last updated this blog, so I’m kicking off a new series.

I’m currently taking a Reinforcement Learning (RL) course taught by Prof. [Hongning Wang](https://coai.cs.tsinghua.edu.cn/hw-ai/index.html). I’m really enjoying his lectures and carefully prepared notes, and I’ve quickly grown fond of RL.

I’ve decided to turn the course material into a series of blog posts—not only to deepen my own understanding, but also in the hope that it helps other readers. The series will largely follow the [Fall 2025 Reinforcement Learning course](https://coai.cs.tsinghua.edu.cn/Courses/RL2025/_site/index.html), but rather than simply repeating the lecture slides, I’ll add my own insights. I’ll also place extra emphasis on the mathematics—equations and proofs—which I find both challenging and especially interesting.

The major topics of this series are:
- Basics of Reinforcement Learning
- Multi-Armed Bandits
- Markov Decision Processes (MDPs)
- Dynamic Programming
- Monte Carlo Methods
- Temporal-Difference Learning
- Policy Gradient Methods
- Function Approximation
- Deep Reinforcement Learning

I’ll cover each of these in its own post.

Today’s topic introduces the basic concepts of RL, which are foundational for everything that follows. As Prof. Wang noted in class, these fundamentals capture much of what RL is about.

Let’s begin our journey into Reinforcement Learning!

# What is Reinforcement Learning?
<span id='rl-in-short'>In short, reinforcement learning is about an agent that continually updates its policy—how it selects actions—based on feedback from the environment, with the goal of maximizing future cumulative reward.</span>

The figure below illustrates this process:
![rl overview](rl_overview.png)

You now have a broad picture of RL. To go deeper, we need to clarify a few core concepts that will ground the discussions to come:
- Action vs. Reward
- State vs. Value
- Policy
- Model

# Action vs. Reward
The two concepts are easy to understand, but they are two most important components in RL. Let's first give the definition:
> Action: making Choices out of a presented options.

> Reward: a scalar feedback signal about the taken action.

The defintions seems easy but there still some points to talk about.
## How to understand 'a presented options'?
This means that the options are not decided by the agent: They are given by the environments, agents can only choose from them.

## The Goal of RL 
Nowadays when researchers training LLM with RL method, they always use the reward as a key metrics to evaluate the model's performance, which leads to a common misconception of the goal of RL that the RL is trying to maximize the reward of a single action. This wrong since the true feedback maybe delayed. Some actions seems good in this step may lead worse and worse situations but some actions seems not that good now may turns to be wisable after a few turns. Hence, [as summarized before](#rl-in-short):

> The goal of learning is to maximize **cumulative** rewards.

In other words, all goals can be described by the maximization of expected cumulative reward. But the rewards is often manually designed so this sentence holds under the hypothesis that the reward design is good enough. Reward Really Counts!

# State vs. Value
