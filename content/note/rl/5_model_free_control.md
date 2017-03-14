+++
title = "5 Model Free Control"
date = "2017-03-14T15:05:04+08:00"
series = ["reinforcement learning"]
tags = ["ml","rl"]
math = true
viz = true
+++

# Key Points
# Lecture Notes

## Introcution
Model-free control can solve these problems   

- MDP model is unknown, but experience can be sampled           
- MDP model is known, but is too big to use, except by samples          
 
On/Off policy learning              

- On-policy learning              
    - “Learn on the job”                
    - Learn about policy 𝛑 from experience sampled from 𝛑     
- Off-policy learning         
    - “Look over someone’s shoulder”                
    - Learn about policy 𝛑 from experience sampled from μ                       

## On-Policy MC Control

### Generalised Policy Iteration with Action-Value Function

Greedy policy improvement over V(s) requires model of MDP           
`$ \pi'(s) = \operatorname*{arg\,max}\limits_{a \in \mathcal{A}} \mathcal{R}_s^a + \mathcal{P}_{ss'}^aV(s')    $`

Greedy policy improvement over Q(s,a) is model-free                 
`$ \pi'(s) = \operatorname*{arg\,max}\limits_{a \in \mathcal{A}} \mathcal{Q}(s,a))    $`

Policy evaluation Monte-Carlo policy evaluation, `$ Q = q_\pi$`             
Policy improvement Greedy policy improvement? (haha ,that is 𝝴-Greddy)


### Exploration
**recall greedy**   

`$ 
\pi_*(a|s) = 
\begin{cases}
    1, & \text{if }  a= \operatorname*{arg\,max}\limits_{a \in \mathcal{A}} q_*(s,a))    \\
    0, & \text{otherwise}
\end{cases}
$` 

**𝝴-Greddy Exploration**

- Simplest idea for ensuring continual exploration          
- All m actions are tried with non-zero probability             
- With probability 1-𝝴  choose the greedy action           
- With probability 𝝴 choose an action at random                
`$ 
\pi(a|s) = 
\begin{cases}
    \epsilon/m +1-\epsilon , & \text{if }  a^*= \operatorname*{arg\,max}\limits_{a \in \mathcal{A}} Q(s,a))    \\
    \epsilon/m, & \text{otherwise}
\end{cases}
$` 

**𝝴-Greedy Policy Improvement**          

Theorem         
For any 𝝴-Greedy Policy 𝛑 , the 𝝴-Greedy policy 𝛑' with respect to q𝛑 is an improvement `$ v_{\pi'}(s) \geqslant v_\pi(s)  $`
`$ 
\begin{align*}
q_\pi(s, \pi'(s)) &= \sum_{a}\pi'(a|s)q_\pi(s,a) \\
                  &= \epsilon/m \sum_{a} q_\pi(s,a) + (1-\epsilon) \max_a q_\pi(s,a) \\
                  &\geqslant  \epsilon/m \sum_{a} q_\pi(s,a) + + (1-\epsilon) \sum_{a} \frac{\pi(a|s) - \epsilon/m}{1-\epsilon} q_\pi(s,a) \\
                  &= \sum_a \pi(a|s) q_\pi(s,a) \\
                  &= v_\pi(s)
\end{align*}
$` 

Therefore from policy improvement theorem,  `$ v_{\pi'}(s) \geqslant v_\pi(s)  $`

### GLIE 
**Definition**          
_Greedy in the Limit with Infinite Exploration_(GLIE)   

- All state-action pairs are explored infinitely many times,               
`$ \lim\limits_{k\rightarrow \infty} N_k(s,a)=\infty $`             

- The policy converges on a greedy policy,                 
`$ \lim\limits_{k\rightarrow \infty} \pi_k(a|s)=1(a=\operatorname*{arg\,max}\limits_{a'} Q_k(s,a')) $`          

For example, 𝝴-greedy is GLIE if 𝝴 reduces to zero at `$ \epsilon_k = \frac{1}{k}$`

**GLIE Monte-Carlo Control**        

- Sample kth episode using 𝛑: {S1, A1, R2, ..., ST } ∼ 𝛑          
- For each state St and action At in the episode        
recall _Incremental Mean_          

`$
\begin{align*}
    N(S_t, A_t) &\leftarrow N(S_t, A_t) + 1 \\
    Q(S_t, A_t) &\leftarrow Q(S_t, A_t) + \frac{1}{N(S_t, A_t)} (G_t - Q(S_t, A_t))   
\end{align*}
$`      
- Improve policy based on new action-value function             
`$
\begin{align*}
    \epsilon    &\leftarrow 1/k \\
    \pi         &\leftarrow \epsilon-greedy(Q)
\end{align*}
$`  

**Theroem**     
GLIE Monte-Carlo control converges to the optimal action-value function, `$ Q(s,a) \rightarrow q_*(s,a) $`

## On-Policy TD Learning

## Off-Policy Learning

## Summary

# Excises