+++
date = "2017-03-09T11:26:55+08:00"
title = "2_Markov_Decision_Processes"
menu = ""
tags = ["ml", "rl"]
categories = ["learning"]
Description = ""
math = true

+++

## Markov Processes

### Definition  
A state `$S_t$` is Markov if and only if   
`$ \mathbb{P}[S_{t+1} | s_t] = \mathbb{P}[S_{t+1}|S_1, ... , S_t] $`  

### State Trasition Matrix
`$ \mathcal{P} = \mathbb{P}[S_{t+1} = s' | S_t = s] $`

### Markov Process
A Markov Process (or Markov Chain) is a tuple `$ <\mathcal{S}, \mathcal{P}> $`   
- `$\mathcal{S}$` is a (finite) set of states  
- `$\mathcal{P}$` is a state transition probability matrix      
  `$ \mathcal{P} = \mathbb{P}[S_{t+1} = s' | S_t = s] $`


## Markov Reward Processes
A Markov reward process is a Markov chain with values.

### Definition
A Markov Reward Process is a tuple  `$ <\mathcal{S}, \mathcal{P}, \mathcal{R}, \gamma> $`   
- `$ \mathcal{S} $` is a finite set of states   
- `$ \mathcal{P} $` is a state transition probability matrix,       
    `$ \mathcal{P} = \mathbb{P}[S_{t+1} = s' | S_t = s] $`  
- `$ \mathcal{R} $` is a reward function,       
    `$ \mathcal{R}_s =  \mathbb{E}[R_{t+1} | S_t=s]$`   
- `$ \gamma $` is a discount factor,  `$ \gamma \in [0,1]$`     

## Markov Decision Processes

## Extensions to MDPs
