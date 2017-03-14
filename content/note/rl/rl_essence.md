+++
title = "RL Essence"
date = "2017-03-14T14:37:32+08:00"
series = ["reinforcement learning"]
tags = ["ml","rl"]
math = true
viz = true
+++
# Definition
- MDP               
A Markov decision process (MDP) is a Markov reward process with decisions.  
It is an environment in which all states are Markov.    
A Markov Decision Process is a tuple  `$ <\mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma> $`    

    - `$ \mathcal{S} $` is a finite set of states   
    - `$ \mathcal{A} $` is a finite set of actions
    - `$ \mathcal{P} $` is a state transition probability matrix,       
    `$ \mathcal{P}_{ss'}^a = \mathbb{P}[S_{t+1} = s' | S_t=s, A_t = a] $`  
    - `$ \mathcal{R} $` is a reward function,       
    `$ \mathcal{R}_s^a =  \mathbb{E}[R_{t+1} | S_t=s, A_t = a] $`   
    - `$ \gamma $` is a discount factor,  `$ \gamma \in [0,1]$`  

- state action relation         
`$
\begin{align*}
    v_\pi(s) &= \sum_{a \in \mathcal{A}} \pi(a|s) q_\pi(s,a)               \\
    q_\pi(s,a) &= \mathcal{R}_s^a + \gamma \sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_\pi(s')  
\end{align*}
$`    

- state-value function          
`$ v_\pi(s)  = \sum_{a \in \mathcal{A}} \pi(a|s)  (\mathcal{R}_s^a + \gamma \sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_\pi(s') ) $` 

- action-value function          
    `$ q_\pi(s,a)  = \mathcal{R}_s^a + \gamma \sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a \sum_{a \in \mathcal{A}} \pi(a'|s') q_\pi(s',a') $`  

- return        
    `$ G_t = R_{t+1} +  R_{t+2} + ... = \sum\limits_{k=0} ^\infty \gamma^kR_{t+k+1} $`              
`$
\begin{align*}
V_\pi(s) &= \mathbb{E}_\pi \left \{ {G_t | s_t = s} \right \}                                                   &  \color{red} {MC} \\
         &= \mathbb{E}_\pi\left \{  R_{t+1} + \gamma V_\pi(s_{t+1}) | s_t = s\right \}                          &  \color{red} {TD(0)} \\
         &= \sum_a\pi(s,a)\sum_{s'}\mathbb{P}_{ss'}^a [R_{ss'}^a + \gamma V_\pi(s')]                            &  \color{red} {DP}
\end{align*}
$`

# Method
 
## DP           
`$ V(S_t) \leftarrow \mathbb{E}_\pi[ R_{t+1} + \gamma V(S_{t+1})] $`    

The way to find a policy `$ \pi_*$` over `$ \pi $`              
`$ 
\pi_*(a|s) = 
\begin{cases}
    1, & \text{if }  a= \operatorname*{arg\,max}\limits_{a \in \mathcal{A}} q_*(s,a))    \\
    0, & \text{otherwise}
\end{cases}
$`                          
`$  v_*(s) = \max\limits_a q_* (s,a)  \ \ \ \ \    q_*(s,a) = R_s^a + \gamma\sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_*(s') $`

## MC       
 `$ V(S_t) \leftarrow V(S_t) + \alpha(G_t-V(S_t))$`       

## TD           
`$ V(S_t) \leftarrow V(S_t) + \alpha(R_{t+1} + \gamma V(S_{t+1})-V(S_t))$`            
