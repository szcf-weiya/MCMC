# Hamiltonian Monte Carlo Sampling

## HMC

- a MCMC method that uses the derivatives of the density function being sampled to generate efficient transitions spanning the posterior
- uses an approximate Hamiltonian dynamics simulation based on numerical integration which is then correlated by performing a Metropolis acceptance step

### Target Density

a density $$p(\theta)$$ for the parameters $$\theta$$. Typically, a Bayesian posterior $$p(\theta\mid y)$$ given data $$y$$.

### Auxiliary Momentum Variable

HMC introduces auxiliary momentum variable $$\rho$$ and draws from a joint density

$$
p(\rho,\theta)=p(\rho\mid\theta)p(\theta)\,.
$$

In most applications of HMC, the auxiliary density is a multivariate normal that does not depend on the parameter $$\theta$$,

$$
\rho\sim \text{MultiNormal}(0,\Sigma)\,.
$$

The covariance matrix $$\Sigma$$ acts as a Euclidean metric to rotate and scale the target distribution.

The inverse $$\Sigma^{-1}$$ is known as the mass matrix.

### The Hamiltonian

The joint density $p(\rho,\theta)$ defines a Hamiltonian,

$$
\begin{align*}
H(\rho,\theta) &= -\log p(\rho,\theta)\\
&= -\log p(\rho,\theta) - \log p(\theta)\\
&= T(\rho\mid\theta) + V(\theta)\,,
\end{align*}
$$

where the first term is called the "kinetic energy", and the second term is called the "potential energy".

### Generating Transitions

Starting from the current value of the parameters $\theta$, a transition to a new state is generated in two stages

1. draw a value for the momentum independently of the current parameter $$\rho\sim \text{MultiNormal}(0,\Sigma)$$. **These momentum does not persist across iterations**
2. evolved via Hamilton's equations

$$
\begin{align*}
\frac{d\theta}{dt} & = +\frac{\partial H}{\partial\rho} = +\frac{\partial T}{\partial \rho}\\
\frac{d\rho}{dt} &= -\frac{\partial H}{\partial \theta} = - \frac{\partial T}{\partial \theta} - \frac{\partial V}{\partial \theta}\,.
\end{align*}
$$

### Leapfrog Integrator

- a numerical integration algorithm that's specifically adapted to provide stable results for Hamiltonian systems of equations
- take discrete steps of some small time interval $\epsilon$. 
- begin by drawing a fresh momentum term independently of the parameter values $$\theta$$ or previous momentum value
- alternates half-step updates of the momentum and full-step updates of the position

$$
\begin{align*}
\rho & \leftarrow \rho -\frac{\epsilon}{2}\frac{\partial V}{\partial\theta}\\
\theta & \leftarrow \theta + \epsilon \Sigma \rho\\
\rho & \leftarrow \rho - \frac{\epsilon}{2}\frac{\partial V}{\partial\theta}
\end{align*}
$$

- The resulting state at the end of the simulation ($$L$$ repetitions of the above three steps) will be denoted $$(\rho^*,\theta^\*)$$.

### Metropolis Accept Step

If the leapfrog integrator were perfect numerically, there would no need to do any more randomization per transition than generating a random momentum vector.

Instead, what is done in practice to account for numerical errors during integration is to apply a Metropolis acceptance step, where the probability of keeping the proposal $$(\rho^*,\theta^\*)$$ generated by transitioning from $$(\rho,\theta)$$ is 

$$
\min(1,\exp(H(\rho,\theta)-H(\rho^*,\theta^*)))\,.
$$

## HMC Algorithm Parameters

### Integration time

the actual integration time is $$L\epsilon$$, a function of number of steps

### Automatic Parameter Tuning

### Discretization-Interval Adaptation Parameters

### Step-Size Jitter

### Euclidean Metric

### NUTS and its configuration

The no-U-turn (NUTS) automatically selects an appropriate number of leapfrog steps in each iteration in order to allow the proposals to traverse the posterior without doing unnecessary work.

### Sampling without Parameters

!!! TODO

!!! Not work yet.

