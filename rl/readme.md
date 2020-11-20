# Reinforcement Learning papers
I'm a beginner when it comes to reinforcement learning, but want to learn a little bit more.

## Soft Actor-Critic Algorithms and Applications
https://arxiv.org/abs/1812.05905

A contribution of the paper in introducing "the first off-policy actor-critiv method in the maximum entropy reinfrocement learning framework".

### SAC - what is it?
off-policy actor-critic algorithm based on the maximum entropy

OK, let's break it down.

#### off-policy
What does it mean that RL algorithm is off-policy? It means, that we don't want to generate new samples every time we want to learn something. We want to learn on the history of our actions (reuse past experience). In other words, we want to learn about policy q, given the trajectories of policy p. Most RL algorithms are **on-policy**.

#### actor-critic
What is actor-critic algorithm?

Actor ~ policy approximiation, Critic ~ value function approximation

Actor methods use parametrized family of distributions: we update 'actor' parameters with gradient descent. Drawback: large variance of gradient estimators.

Critic methods rely on value fuction approximiation. How do we update a policy with this method?

Actor-critic: combination of the two. Critic learns a value function which is used to update the actor's policy parameters. Source: "Actor-Critiv Algorithms" Konda & Tsitskilis, 1999.

Question: how is Q function related to these methods?

#### maxiumum entropy
What is maximum entropy algorithm in RL?

It is an algorithm that doesn't just optimize expected sum of rewards, but rather expected sum of rewards + entropy of a policy. This assumes, that policy is stochastic. The term deciding the impact of the entropy (how stochastic the algorithm is) is called *temperature*. I guess the advantage of the algorithm lies in the stochasticity of the policy. If you are not sure what to do, just optimize for entropy. That forces the algorithm to explore more and maybe come up better actions. Sounds simple, just modify the loss function. But calculating this entropy doesn't seem straightforward, what is the distribution of our policy pi(a_t|s_t)? We assume a continuous state and ation space (e.g what angle to turn the steering wheel). This action is probably decided by a neural networ. I guess it can output the parameters of the distribution. As a first try I would try gaussian, but it has a single mode, which may be too simplistic (it doesn't allow for exploring multiple equally attractive regrions with the same probability).

### What do I need to learn?
* planning and control with continuous action space using function approximators
* policy iteration
* Why is SAC off-policy? I don't get it.
* DDPG (Deep Deterministic Policy Gradient)
* How to apply maximum-entropy framework to MCTS planning?

