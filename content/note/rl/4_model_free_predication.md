+++
title = "4 Model Free Predication"
date = "2017-03-13T14:23:47+08:00"
series = ["reinforcement learning"]
tags = ["ml","rl"]
math = true
viz = true
+++

# Key Points
**Return**      
`$
\begin{align*}
V_\pi(s) &= \mathbb{E}_\pi\left \{ \sum_{k=0}^\infty \gamma_k R_{t+k+1} | s_t = s\right \}                      &  \color{red}  {Definition} \\
         &= \mathbb{E}_\pi\left \{  R_{t+1} + \gamma \sum_{k=0}^\infty \gamma_k R_{t+k+2} | s_t = s\right \}    &  \color{red}  {Unfolding} \\
         &= \mathbb{E}_\pi\left \{  R_{t+1} + \gamma G_{t+1} | s_t = s\right \}                                 &  \color{red}  {Recursive \ formula} 
\end{align*}
$`

**Value function**           
`$
\begin{align*}
V_\pi(s) &= \mathbb{E}_\pi \left \{ {G_t | s_t = s} \right \}                                                   &  \color{red} {MC} \\
         &= \mathbb{E}_\pi\left \{  R_{t+1} + \gamma V_\pi(s_{t+1}) | s_t = s\right \}                          &  \color{red} {TD(0)} \\
         &= \sum_a\pi(s,a)\sum_{s'}\mathbb{P}_{ss'}^a [R_{ss'}^a + \gamma V_\pi(s')]                            &  \color{red} {DP}
\end{align*}
$`

MC Backup: `$ V(S_t) \leftarrow V(S_t) + \alpha(G_t-V(S_t))$`       
TD Backup: `$ V(S_t) \leftarrow V(S_t) + \alpha(R_{t+1} + \gamma V(S_{t+1})-V(S_t))$`            
DP Backup: `$ V(S_t) \leftarrow \mathbb{E}_\pi[ R_{t+1} + \gamma V(S_{t+1})] $`

# Lecture Notes
[some reading]https://www.tu-chemnitz.de/informatik/KI/scripts/ws0910/ml09_6.pdf
## introduction
**Model-free prediction**           
Estimate the value function of an _unknown_ MDP

## Monte-Carlo Learning
[MC method](https://en.wikipedia.org/wiki/Monte_Carlo_method) 

- MC methods learn directly from episodes of experience
- MC is model-free: no knowledge of MDP transitions / rewards 
- MC learns from complete episodes: no bootstrapping
- MC uses the simplest possible idea: value = mean return
- Caveat: can only apply MC to episodic MDPs 
    - All episodes must terminate

### Some definition
- Goal: learn `$v_\pi$` from episodes of experience under policy `$\pi$`            
    `$ S_1, A_1, R_2, \dots, S_k \sim \pi $`
- Recall that the return is the total discounted reward:            
    `$ G_t = R_{t+1} +  \gammaR_{t+2} + ... = \sum_{k=0} ^\infty \gamma^kR_{t+k+1} $`     
- Recall that the value function is the expected return:            
    `$  v(s) = \mathbb{E}[G_t | S_t =s] $`    
- Monte-Carlo policy evaluation uses **empirical mean return** instead of expected return        


### MC Policy Evaluation (algorithm)
Thare are two kinds of evaluation method:  _the first time-step_ and _every time-step_     

- The first/Every time-step  t that state s is visited in an episode,       
- Increment counter ,     `$ N(s) \leftarrow N(s) +1 $`       
- Increment total return, `$ S(s) \leftarrow S(s) +G_t $`     
- Value is estimated by mean return `$ V(s) = S(s)/N(s)$`     
- By law of large numbers, `$ V(s) \rightarrow v_\pi(s) \text{  as  } N(s) \rightarrow \infty$`       


### Incremental Mean and Incremental MC updates (practice)
**Incremental Mean**        
The Mean `$ \mu_1, \mu_2, \dots $` of a sequence `$x_1, x_2, \dots$` can be computed incrementally,                
`$
\begin{align*}
\mu_k &= \frac{1}{k}\sum_{j=1}^kx_j \\
      &= \frac{1}{k}(x_k + \sum_{j=1}^{k-1}x_j)  \\
      &= \frac{1}{k}(x_k + (k-1)\mu_{k-1})  \\
      &= \frac{1}{k}x_k  +\mu_{k-1} - \frac{1}{k}\mu_{k-1} \\
      &= \mu_{k-1} + \frac{1}{k}(x_k - \mu_{k-1}) \\
\end{align*}
$`

**Incremental MC updates**         

- Update V(s) incrementally after episode `$ S_1, A_1, R_2, \dots, S_k \sim \pi $`          
- For each state `$S_t$` with return `$G_t$`            
`$
\begin{align*}    
    N(s) & \leftarrow N(s) +1 \\
    V(S_t) & \leftarrow V(S_t) + \frac{1}{N(s)}(G_t - V(S_t))
\end{align*}
$`
- In non-stationary problems, it can be useful to track a running mean, i.e. forget old episodes.                   
`$ V(S_t)  \leftarrow V(S_t) + \alpha(G_t - V(S_t)) $`     
<span style="color:red">what is stationary/non-stationary problems ???</span>   

## Temporal-Difference Learning
- TD methods learn directly from episodes of experience
- TD is **model-free**: no knowledge of MDP transitions / rewards 
- TD learns from incomplete episodes, by bootstrapping
- TD updates a guess towards a guess

### TD Policy Evaluation
- Simplest temporal-difference learning algorithm: TD(0) 
    - Update value `$V(S_t)$` toward estimated return `$ R_{t+1} + \gamma V(S_{t+1})$`  
        `$ V(S_t) \leftarrow + \alpha(R_{t+1} + \gamma V(S_{t+1} - V(S_t))$`    
    - `$ R_{t+1} + \gamma V(S_{t+1}$` is called the TD target       
    - `$ \delta _t = R_{t+1} + \gamma V(S_{t+1} - V(S_t)$` is called the TD error     

## MC vs TD
### Bias
- TD can learn before knowing the final outcome
    - TD can learn online after every step
    - MC must wait until end of episode before return is known
- TD can learn without the final outcome 
    - TD can learn from incomplete sequences
    - MC can only learn from complete sequences
    - TD works in continuing (non-terminating) environments 
    - MC only works for episodic (terminating) environments

- The return value
    - Return `$ G_t = R_{t+1} +  R_{t+2} + ... \gamma^{T-1}R_T $`  is unbiased estimate of `$ v_\pi(S_t)$`
    - True TD target `$ R_{t+1} + \gamma v_\pi(S_{t+1})$` is is unbiased estimate of `$ v_\pi(S_t)$`
    - TD target `$ R_{t+1} + \gamma V(S_{t+1})$` is biased estimate of vπ(St)
    - TD target is much lower variance than the return:
        - Return depends on many random actions, transitions, rewards 
        - TD target depends on one random action, transition, reward

### Variance
 MC | TD
--- | ---
MC has high variance, zero bias    |        TD has low variance, some bias                                                                      
Good convergence properties        |        Usually more efficient than MC                                                 
(even with function approximation) |        TD(0) converges to `$v_\pi(s)$`                                                     
Not very sensitive to initial value|        (but not always with function approximation)                                                  
Very simple to understand and use  |        More sensitive to initial value            

### markov property
- TD exploits Markov property
    - Usually more efficient in Markov environments
- MC does not exploit Markov property
    - Usually more effective in non-Markov environments       

MC Backup: `$ V(S_t) \leftarrow V(S_t) + \alpha(G_t-V(S_t))$`       
TD Backup: `$ V(S_t) \leftarrow V(S_t) + \alpha(R_{t+1} + \gamma V(S_{t+1})-V(S_t))$`            
DP Backup: `$ V(S_t) \leftarrow \mathbb{E}_\pi[ R_{t+1} + \gamma V(S_{t+1})] $`

## Unified view of reinforcement learning
- Bootstrapping: update involves an estimate 
    - MC does not bootstrap
    - DP bootstraps
    - TD bootstraps
- Sampling: update samples an expectation 
    - MC samples
    - DP does not sample 
    - TD samples

![image](/img/content/note/rl/unified_view_of_rl.png)

## TD(λ)
### n-step return
- Consider the followingn-step returns for `$ n= 1,2,3, \dots, \infty$`         
`$
\begin{align*}
n=1          \ \ \ \ \ \      &  G_t^{(1)} =  R_{t+1} + \gamma V(S_{t+1})      \\
n=2          \ \ \ \ \ \      &  G_t^{(2)} =  R_{t+1} + \gamma R_{t+2} + \gamma^2 V(S_{t+2})      \\
\vdots       \ \ \ \ \ \      &  \vdots  \\
n=\infty     \ \ \ \ \ \      &  G_t^{(\infty)} =   R_{t+1} + \gamma R_{t+2} + \dots +\gamma^{T-1}R_T      
\end{align*}
$`
- Define the n-step return      
`$ G_t^{(n)} =  R_{t+1} + \gamma R_{t+2} + \dots + \gamma^n V(S_{t+n})   $`         
- n-step TD learning        
`$ V(S_t) \leftarrow V(S_t) + \alpha ( G_t^{(n)} - V(S_t)  )$`

### Forward view of TD(λ)  

**averaging n-step returns**            

- We can averagen-step returns over different n          
    e.g.  `$ \frac{1}{2}G^{(2)} + \frac{1}{2}G^{(4)} $`  

**λ-return**            

- The λ-return `$G_t^\lambda$` combines all n-step returns `$G_t^{(n)}$`        
- Using weight `$(1-\lambda)\lambda^{n-1}$`       
    `$ G_t^\lambda  = (1-\lambda)\sum_{n=1}^\infty \lambda^{n-1}G_t^{(n)} $`       
- Forward-view TD(λ)     
    `$ V(S_t) \leftarrow V(S_t) + \alpha (G_t^\lambda - V(S_t)) $`



Update value function towards the λ-return          
Forward-view looks into the future to compute `$G_t^\lambda$`           
Like MC, can only be computed from complete episodes            

### Backward View TD(λ)  
- Forward view provides theory
- Backward view provides mechanism
- Update online, every step, from incomplete sequences

# Excises
