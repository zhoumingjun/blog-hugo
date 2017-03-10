+++
date = "2017-03-08T16:34:01+08:00"
title = "1 Introduction to Reinforcement Learning"
tags = ["ml", "rl"]
series = ["reinforcement learning"]
highlight = true
math = true

[header]
  caption = ""
  image = ""

+++

## About Reinforcement Learning  

What makes reinforcement learning different from other machine learning paradigms?   

- There is no supervisor, only a reward signal
- Feedback is delayed, not instantaneous
- Time really matters (sequential, non i.i.d data)
- Agent’s actions affect the subsequent data it receives


## The Reinforcement Learning Problem

### Rewards

**Definition (Reward Hypothesis)**   
All goals can be described by the maximisation of expected cumulative reward

### Sequential Decision Making
- Goal: select actions to maximise total future reward
- Actions may have long term consequences
- Reward may be delayed
- It may be better to sacrifice immediate reward to gain more long-term reward


## Inside An RL Agent

An RL agent may include one or more of these components:   
- Policy: agent’s behaviour function  
- Value function: how good is each state and/or action 
- Model: agent’s representation of the environment 


### policy

A policy is the agent’s behaviour   
It is a map from state to action, e.g. Deterministic policy: a = 𝜋(s)   
Stochastic policy:   `$ \pi(a|s) = \mathbb{P}[A_t=t |S_t=s] $`
 
### value function

Value function is a prediction of future reward    
Used to evaluate the goodness/badness of states    
And therefore to select between actions, e.g.
`$ v_{\pi}(s) = \mathbb{E}[R_{t+1} + \gamma R_{t+2} +\gamma^{t+2} R_{t+3} + .. | S_{t} = s] $`

### model 
A model predicts what the environment will do next P predicts the next state  
R predicts the next (immediate) reward, e.g.  
` $ P_{ss{}'}^{\alpha }=\mathbb{P}[S_{t+1}=s{}'|S_{t} =s,A_{t} =a] $`   
` $ R_{s}^{\alpha }=\mathbb{E}[R_{t+1} |S_{t} =s,A_{t} =a] $  `


## Problems within Reinforcement Learning
