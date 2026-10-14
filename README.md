<!-- solve-problem:cbda1c49-a30f-425b-8cea-8a381b4b7fba -->
# Capital-constrained time to profitability

A [Tangle Solve problem](https://solve.tangle.tools/problems/cbda1c49-a30f-425b-8cea-8a381b4b7fba).

## What is the problem?

In Nakamoto-style proof-of-work blockchains, a selfish miner with hash fraction q withholds blocks to force honest miners to waste work on chains that get orphaned. The classical question is whether the attack is *profitable in the long run* (i.e., whether the per-block revenue rate exceeds the fair share q). This problem asks something sharper: is selfish mining profitable *for an attacker with finite capital*?

Formalize as follows. Let the attacker's bankroll be a stochastic process B_t = initial capital C plus cumulative revenue R_t minus cumulative cost (electricity/hash-rate rental, or financing cost r·t if capital is borrowed). The attacker is *ruined* the first time B_t ≤ 0 (cannot cover the next unit of operating cost). Classical analyses (Markov chains over the withholding state, recasting revenue as a positive-drift random walk) show eventual profitability for q above some threshold (≈ 0.233 for the standard model per Grunspan–Pérez-Marco, or even lower with optimal strategies). But the revenue process has high variance and long negative excursions: during a withholding episode the attacker mines at cost while collecting nothing until the crisis of the fork resolves.

The open problem: **(a)** compute/optimize the distribution (or expectation) of the break-even time T* = inf{t : B_t ≥ C} for a given bankroll C and hash fraction q, under ruin constraints; **(b)** characterize the *efficient frontier* of triples (expected profit rate, time to profitability, ruin probability) over the space of withholding strategies (when to withhold, when to release, when to abort); **(c)** find the optimal risk-constrained strategy, e.g., maximizing expected discounted profit subject to P(ruin) ≤ ε. The model assumptions to state explicitly: network propagation parameters (γ), difficulty adjustment rule, cost model per unit hash rate, and whether capital is equity or debt (financing cost).

## Why does it matter?

Rational-attack security analyses (and thus the honest-majority assumption underlying Bitcoin's security) currently rely on asymptotic profitability, which can be misleading: an attack with positive drift but requiring transient losses exceeding any realistic budget is not a credible threat. A break-even-time–risk frontier would let protocol designers and insurers quantify *actual* attack incentives rather than asymptotic ones, inform fee/subsidy policy during difficulty-adjustment epochs, and connect blockchain security to classical ruin theory with genuine predictive content. It also matters to attackers/financiers literally: it answers "how much capital does a selfish miner need to survive until profit?"

## What's already known?

- **Grunspan & Pérez-Marco (2018)** compute exact closed-form Markov-chain revenue rates for selfish mining, correcting earlier simulations, and show the attack is eventually profitable above q ≈ 0.233 (γ = 0) with profit-lag effects acknowledged: profitability is asymptotic, and they explicitly note the profit appears only "in the long run" — the *interim* loss process was not quantified.
- **Finkbeiner, Shaw & Almashaqbeh (2026)** (verify details) apparently distinguish profit-maximizing from time-minimizing withholding strategies and address temporal composability — i.e., how attack decisions compose over time and when short-horizon selfishness pays off. This supersedes pure steady-state analysis but still does not handle bankroll/ruin constraints.
- Classical ruin theory (Lundberg's inequality, Cramér asymptotics for ruin probabilities) and risk-sensitive control (average/variance-constrained MDPs, percentile criteria) provide ready machinery, but nobody has connected them to the selfish-mining Markov chain in a published result.
- No published strategy is known to be optimal under a hard ruin constraint; existing strategies (selfish mining, publish-immediately, hybrid withhold-k-then-release) are heuristics optimized for asymptotic revenue rate.

## What would count as solving it?

Any of the following, in increasing strength:
1. **Break-even time bounds:** For the standard selfish-mining Markov chain with cost rate c per unit time, prove nontrivial bounds (or exact asymptotics, e.g., via large deviations on the negative excursions of the revenue process) on E[T*] and P(T* > t) as functions of (q, γ, c, C). A referee would want a theorem with explicit constants.
2. **Ruin characterization:** A closed-form or provably tight bound on P(ruin before profitability) — e.g., an exponential-in-C bound via Lundberg-type adjustment coefficients for the withholding random walk.
3. **Frontier construction:** A parametric family of strategies and a proof that they trace the (profit rate, break-even time, ruin probability) Pareto frontier, or at least dominate all previously proposed strategies.
4. **Optimal control:** Formulate as a constrained MDP over the fork-state × bankroll state and prove structural properties of the optimal policy (e.g., threshold form in bankroll), or give a convergent numerical scheme with performance guarantees.
5. **Empirical:** A large-scale simulation study validating the analytic frontier across realistic difficulty-adjustment dynamics — weaker, but acceptable for a workshop-tier result.

## How to start

1. Read Grunspan–Pérez-Marco (arXiv:1805.08281) §§ on the Markov chain and exact revenue; extract the transition structure of the withholding process. Then read the Finkbeiner et al. 2026 paper for the temporal-composability framing (verify availability).
2. Model the attacker's cumulative revenue as a random walk with positive drift whose increments are governed by the selfish-mining chain; the key observation is that break-even time is a first-passage time of this walk, and ruin is a two-sided first-passage problem — classical Cramér–Lundberg territory. Start with γ = 0 and fixed difficulty (no readjustment) for tractability.
3. Main obstacle: the revenue increments are *heavy on the left* (long zero-revenue withholding stretches) and not i.i.d., so classical Lundberg exponents need adaptation to the regenerative structure; also difficulty readjustment couples the walk to a slow feedback loop.
4. Natural second step: MDP formulation with state (fork state, bankroll) and constraint on ruin probability; exploit that bankroll enters only through a one-dimensional level to guess threshold policies and prove optimality by induction or convexity.

## Key references

- Grunspan, C. and Pérez-Marco, L. (2018), "On profitability of selfish mining," arXiv:1805.08281. Computes exact closed-form expected revenue rates for selfish mining via Markov chains and establishes the correct profitability threshold, replacing earlier simulation estimates. https://arxiv.org/abs/1805.08281
- Finkbener, T., Shaw, R., and Almashaqbeh, A. (2026), "When Does Being Selfish Pay Off? Temporal Composability and Profitability in Selfish Mining," LIPIcs/AFT, DOI:10.4230/LIPIcs.AFT.2026.29. Analyzes how selfish-mining decisions compose over time, distinguishing strategies that maximize profit from those that minimize time to profitability. (verify — citation from census record; not independently confirmed)
- Eyal, I. and Sirer, E.G. (2014), "Majority is not Enough: Bitcoin Mining is Vulnerable," arXiv:1311.0243. The original selfish-mining paper defining the attack and the q-threshold with propagation parameter γ; the baseline model this problem builds on. https://arxiv.org/abs/1311.0243
- Asmussen, S. and Albrecher, H., *Ruin Probabilities* (2nd ed., World Scientific, 2010). Standard reference for Cramér–Lundberg theory and first-passage analysis of risk processes; the mathematical toolbox for the ruin-constrained analysis.

## Warnings

- The exact modeling choices (cost per hash vs. per block, fixed vs. difficulty-adjusting regime, financing cost, whether ruin means bankroll ≤ 0 or inability to fund one epoch) materially change the answer; state your conventions up front and check sensitivity.
- Do not treat revenue increments as i.i.d. or light-tailed — the withholding mechanism creates correlated, left-skewed increments; naive Lundberg bounds can be wrong.
- Difficulty adjustment is often ignored and is not innocuous: selfish mining suppresses honest block production, perturbing difficulty and thus revenue rates over epochs.
- The 2026 Finkbeiner et al. citation comes from a census record and could not be independently verified; confirm it exists before citing it.
- Common dead end: optimizing only the asymptotic revenue rate and reporting that as "the strategy" — the whole point is the transient/interim loss behavior.

---
*`BCGT-000228` · flagged for review*

## Acceptance guidance

Any of the following, in increasing strength:
1. **Break-even time bounds:** For the standard selfish-mining Markov chain with cost rate c per unit time, prove nontrivial bounds (or exact asymptotics, e.g., via large deviations on the negative excursions of the revenue process) on E[T*] and P(T* > t) as functions of (q, γ, c, C). A referee would want a theorem with explicit constants.
2. **Ruin characterization:** A closed-form or provably tight bound on P(ruin before profitability) — e.g., an exponential-in-C bound via Lundberg-type adjustment coefficients for the withholding random walk.
3. **Frontier construction:** A parametric family of strategies and a proof that they trace the (profit rate, break-even time, ruin probability) Pareto frontier, or at least dominate all previously proposed strategies.
4. **Optimal control:** Formulate as a constrained MDP over the fork-state × bankroll state and prove structural properties of the optimal policy (e.g., threshold form in bankroll), or give a convergent numerical scheme with performance guarantees.
5. **Empirical:** A large-scale simulation study validating the analytic frontier across realistic difficulty-adjustment dynamics — weaker, but acceptable for a workshop-tier result.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/cbda1c49-a30f-425b-8cea-8a381b4b7fba?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000228.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000228)
