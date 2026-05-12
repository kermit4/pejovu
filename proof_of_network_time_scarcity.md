**PoLW in 3 constraints:**

$$
\begin{aligned}
&\text{1. Metric: } \forall i,j,k:\; |R_{ik} - R_{jk}| \le R_{ij} \le R_{ik} + R_{jk} \\
&\text{2. Leader: } h^* = \arg\min_h H(r\,\|\,pk_h\,\|\,\widetilde{R}_h) \\
&\text{3. Sybil cost: } \mathrm{rank}(R_S)\le m \Rightarrow \text{Cost}=\Omega(\#\mathrm{ASNs})
\end{aligned}
$$

Translation: Ping times obey $\Delta$-ineq. Sybils can't fake $O(n^2)$ constraints with $O(m)$ boxes. Cost = global infra, not hash.
