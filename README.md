# SRP-1-semester
This code plots a discrete filterless system with a sinusoidal phase detector characteristic, which is directly considered in the paper.

What does the code do?

The dpll_first_order_map function calculates the discrete PLL mapping.

The plot_dpll_map function plots a phase portrait with:

*The plot of the φ_{k+1} = φ_k - K₁ sin(φ_k)

*The identity line (45°)

*The trajectory using the "Lamerey staircase" method

*Fixed points (stable and unstable)
