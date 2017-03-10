+++
title = "3 Planning by Dynamic Programming"
date = "2017-03-10T12:03:48+08:00"
series = ["reinforcement learning"]
tags = ["ml","rl"]
math = true
viz = true
+++

# Key Points
Dynamic programming assumes full knowledge of the MDP

1. given MDP and policy `$\pi$`, compute the state value `$v_\pi$`  
    just follow the ballmen equation , and compute value of each state iteratively   
`$
\begin{align*}
v_{k+1}(s)  &= \sum_{a \in \mathcal{A}} \pi(a|s)  (\mathcal{R}_s^a + \gamma \sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_k(s') ) \\
v_{k+1} &= \mathcal{R}^\pi + \gamma \mathcal{P}^\pi v_k
\end{align*}
$`   

2. given MDP, compute the optimal policy `$\pi_*$`           
2.1 Policy iteration       
Policy iteration try to find the optimal policy `$\pi_*$` by improving the current policy `$\pi$` step-by-step

```javascript
while(is_stable)
    evaluate the policy      
    improve the policy   
    /*
    greedy can be used here. 
    in fact, any improment method can be used, it just affect the converage speed      
    */   
```

2.2 Value iteration             
Value iteration try to find the optimal policy `$\pi_*$` by the known solutions to the sub problems     
It is different to the policy iteration, and there is no explicit policy

```javascript
while(is_stable)
    compute v(s) from v(s') // s can be reachable from s'

then the policy is found //because we know the action between s'->s
```

# Lecture
## Introduction
Dynamic Programming is a very general solution method forproblems which have two properties:

- Optimal substructure
    - Principle of optimality applies
    - Optimal solution can be decomposed into subproblems
- Overlapping subproblems
    - Subproblems recur many times
    - Solutions can be cached and reused
- Markov decision processes satisfy both properties
    - Bellman equation gives recursive decomposition
    - Value function stores and reuses solutions

## Policy Evaluation

### definition

- Problem:  evaluate a given policy       
- Solution:  iterative application of Bellman expectation backup        
`$ v_1 -> v_2 -> v_3 $`             
- Using synchronous backups,          
    - At each iterationk+ 1
    - For all statess `$ s \in \mathcal{S} $`
    - Update `$ v_{k+1}(s) $` from   `$ v_{k}(s')$` wheres `$s'$` is a sucdessor state of s

### equation
```viz-dot
digraph g { 
   node[shape="circle" , label="", width=0.2, height=0.2]
   l1[xlabel="Vk+1\(s\)"]
   l21[xlabel="a", width=0.1, height=0.1 , style=filled]
   l22[width=0.1, height=0.1, style=filled]
   l31[xlabel="Vk\(s'\)"]

   l1 -> l21
   l1 -> l22
   l21 -> l31 [xlabel="r"]
   l21 -> l32
   l22 -> l33
   l22 -> l34
}
```
`$
\begin{align*}
v_{k+1}(s)  &= \sum_{a \in \mathcal{A}} \pi(a|s)  (\mathcal{R}_s^a + \gamma \sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_k(s') ) \\
v_{k+1} &= \mathcal{R}^\pi + \gamma \mathcal{P}^\pi v_k
\end{align*}
$`         

## Policy Iteration
### definition
- Given a policy `$ \pi $`
    - Evaluate the policy `$ \pi $` 
    `$ v_\pi(s) = \mathcal{E}[R_{t+1} + \gamma$_{t+2} + ... | S_t=s ]$`     
    - Improve the policy by acting greedily with respect to `$ v_\pi $`     
    `$ \pi' = greedy(v_\pi) $`

![ policy iteration ](/img/content/note/rl/policy_iteration.png) 

### policy improvement

- Consider a deterministic policy, `$ a=\pi(s)$`      
- We can improve the poilcy by acting greedily        
`$ \pi'(s) = \operatorname*{arg\,max}\limits_{a \in \mathcal{A}} q_\pi(s,a))$`      
- This improves the value from any state s over one step      
`$ q_\pi(s, \pi'(s)) = \max\limits_{a \in \mathcal{A}} q_\pi (s,a)   \geqslant q_\pi(s, \pi(s)) = v_\pi(s) $`       
- It therefore improves the value function, `$ v_\pi'(s) \geqslant v_\pi(s) $`      
`$
\begin{align*}
v_\pi(s)& \leqslant q_\pi(s,\pi'(s))   \\
        & \leqslant \mathbb{E}_{\pi'}[R_{t+1} + \gamma v_\pi(S_{t+1}) | S_t = s]    \\
        & \leqslant \mathbb{E}_{\pi'}[R_{t+1} + \gamma q_\pi(S_{t+1}, \pi'(S_{t+1})) | S_t = s]    \\
        & \leqslant \mathbb{E}_{\pi'}[R_{t+1} + \gamma R_{t+2} + \gamma^2 q_\pi(S_{t+2}, \pi'(S_{t+2})) | S_t = s]    \\
        & \leqslant \mathbb{E}_{\pi'}[R_{t+1} + \gamma R_{t+2} + \dots| S_t = s]    \\
        &= v_{\pi'}(s)
\end{align*}
$`
- if improvements stop,     
`$ q_\pi(s, \pi'(s)) = \max\limits_{a \in \mathcal{A}} q_\pi (s,a) = q_\pi(s, \pi(s)) = v_\pi(s) $`         
- Then the Bellman optimality equation has been satisfied           
`$ v_\pi(s) \max\limits_{a \in \mathbb{A}} q_\pi(s,a) $`  
- Therefore `$ v_\pi(s) v_*(s)  \text{   for all} s \in \mathbb(S) $`       
- so `$\pi$` is an optimal policy

### extensions to policy iteration

![ generalised policy iteration ](/img/content/note/rl/generalised_policy_iteration.png) 


## Value Iteration

### definition
- Problem:  find optimal policy `$\pi$`     
- Solution:  iterative application of Bellman optimality backup        
`$ v_1 -> v_2 -> \dots -> v_* $`             
- Using synchronous backups,          
    - At each iteration k+1
    - For all statess `$ s \in \mathcal{S} $`
    - Update `$ v_{k+1}(s) $` from   `$ v_{k}(s')$` 
- Convergence to `$v_*$`  
- **Unlike policy iteration, there is no explicit policy**
- Intermediate value functions may not correspond to any policy

### Principle of Optimality
Any optimal policy can be subdivided into two components

- An optimal first action `$A_*$`
- Followed by an optimal policy from successor state `$\mathbb{S}'$`        

**Theorem**     
A policy `$ \pi(a|s)$` achieves the optimal value from state s, `$v_\pi(s) = v_*(s)$`, if and only if            

- For any state `$s'$` reachable from s     
- `$\pi$` achieves the optimal value from state `$s'$`, `$ v_\pi(s') = v_*(s') $`       

so if we know the subproblem's solution `$v_*(s')$`,`$v_*(s)$` can be found by one-step lookahead       
`$ v_*(s) <- \max\limits_{a \in \mathbb{A}} R_s^a + \gamma\sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_*(s') $`

Here is the final algorithm:        
```$
\begin{align*}
v_{k+1}(s) &= \max\limits_{a \in \mathcal{A}} (\mathcal{R}_s^a + \gamma\sum_{s' \in \mathcal{S}} \mathcal{P}_{ss'}^a v_*(s')  ) \\ 
v_{k+1}    &= \max\limits_{a \in \mathcal{A}} (\mathcal{R}^a + \gamma\mathcal{P}^av_k)
\end{align*}
$```


## Extensions to Dynamic Programming
## Contraction Mapping