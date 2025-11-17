# PDEsProject - Shallow Water Equations Solver

A numerical simulation framework for solving the 1D shallow water equations using finite difference methods.

## Summary of learnings
From [Cotter's exchange](https://mailing-lists.imperial.ac.uk/archives/list/firedrake@imperial.ac.uk/thread/PXCPCQLBXYCFHQEEFBCCHZOO5UMD7DJ2/?sort=date) we are able to add stochastic noise to Firedrake by doing the following:
```{python}
from numpy import random

F = Function(V)
F.dat.data[:] = random.randn(N)
```

[Ephrati et al.](https://agupubs.onlinelibrary.wiley.com/doi/epdf/10.1029/2022MS003268) have Firedrake code that they wrote to model the stochastic 2D Euler equations and perform Data Assimilation on it.
- They write down the SPDE for the evolution equation as
  $$ d\omega = L(\omega)dt + \sum_i^m G^i(\omega, \xi_i) \circ dW_t^i,$$
  where $L$ and $G$ are operators that define the evolution of the vorticity, $\omega$.
- For us to implement Firedrake code that also implements the stochastic thermal shallow water equations we need to write down a similar spde. From [Holm et al. 2021](https://doi.org/10.1063/5.0040026) we can write
  $$ d\bm u + (dX_t \cdot \nabla ) \bm u + \sum_i (\nabla \xi_i) \cdot \bm u \circ dW_t^i = -\frac{1}{\text{Ro}}f \hat{\bm z}\times dX_t\\ - \frac{1}{\text{Ro}}\sum_i\nabla (\xi_i \cdot R) \circ dW^i_t+  \\\mathcal{F}_1(b, \zeta)dt + \mathcal{F}_2(b, h, \zeta)dt$$
- With some pruning we can hopefully write this as
 $$ d\bm u = \mathcal{F}(\bm u, \zeta, h, b)dt + \sum_i^m \mathcal{G}_i(\bm u, \xi_i) \circ dW^i_t, $$
where the $\mathcal{F}$ term contains the deterministic mechanics and the sum over the eigenvector noise vectors $\xi_i$ is the stochastic process driving force.
- Once written in this appropriate form, we then need to ask if it is a good idea to solve in vorticity space. Because we do not need to solve the Poisson equation, it might not be relevant?
- Again, inspired by Ephrati et al. we can implemented this FEM with a "a third-order strong stability preserving Runge-Kutta
(SSPRK3)".
- This becomes relatively easy in code to write once we have built the driving time-step code.
- Idea would be to create a python class that represents particular solver. It can be stepped forward in time.
- We would then save these outputs and create visualisations for that.
## To do list
1. Write the STRSW in the form as described by Ephrati et al. That is compress down into a deterministics component and a stochastic component.
2. Understand how they implement the Galerkin method in Firedrake to solve this problem.
3. Compare to the Cotter et al. 2019 approach to check that this is appropriate for SALT style problems (although it should be).
4. Implement our own method based on this.
5. Narrow down what we want to do with that.
6. TESTING