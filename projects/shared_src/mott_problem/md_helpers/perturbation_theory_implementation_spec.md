# Python implementation specification: two-absorber perturbation theory

## Purpose

Write a tested Python implementation of the finite-time first- and second-order perturbative transition amplitudes for a one-dimensional projectile interacting with two displaced harmonic oscillators. This file is the mathematical and numerical specification. It incorporates the intended results of Chapter II.5 and corrects its obvious transcription errors.

The implementation must compute complex amplitudes and differential momentum probability densities for a requested final state

$$
|P_f,n_1,n_2\rangle
=|P_f\rangle\otimes|n_1(a_1)\rangle\otimes|n_2(a_2)\rangle.
$$

Do not hard-code physical parameters, momentum bounds, oscillator cutoffs, or quadrature grids.

## Model and conventions

The free Hamiltonian has eigenenergies

$$
E(n_1,n_2;P)=\frac{P^2}{2M}+\hbar\omega(n_1+n_2+1).
$$

The interaction is

$$
H_I=V_0\exp\!\left[-\frac{(X-x_1)^2}{2\sigma_V^2}\right]
    +V_0\exp\!\left[-\frac{(X-x_2)^2}{2\sigma_V^2}\right].
$$

The continuum states use

$$
\langle X|P\rangle=(2\pi\hbar)^{-1/2}e^{iPX/\hbar}.
$$

All formulas below assume this normalization. Consequently, $|\mathcal A(P_f)|^2$ is a differential probability density with respect to $P_f$, not a dimensionless probability at a single momentum.

## Required configurable parameters

At minimum, expose the following in a configuration object or dataclass:

- `delta_t = t_f - t_i`
- projectile mass `M`
- oscillator mass `m`
- oscillator angular frequency `omega`
- interaction width `sigma_V`
- initial packet width `sigma_alpha`
- initial momentum magnitude `P0`
- interaction strength `V0`
- absorber positions `a1`, `a2`
- `hbar` (defaulting to 1 is acceptable, but it must remain configurable)
- final oscillator indices and the requested $P_f$ value or grid
- intermediate oscillator cutoff `n_intermediate_max`
- momentum-tail tolerance, quadrature tolerances, and convergence controls

Validate that masses, widths, $\omega$, and $\hbar$ are positive, that $\Delta t$ is nonnegative, and that oscillator indices are nonnegative integers. Permit positive, zero, or negative `P0`, but use $|P_0|$ when constructing the symmetric momentum windows.

## Initial momentum wavefunction

Use the normalized symmetric bimodal wavefunction

$$
C(P)=N_C\left[
e^{-(P-P_0)^2\sigma_\alpha^2/(2\hbar^2)}
+e^{-(P+P_0)^2\sigma_\alpha^2/(2\hbar^2)}
\right],
$$

where

$$
N_C=\sqrt{\frac{\sigma_\alpha}
{2\hbar\sqrt\pi\left(1+e^{-P_0^2\sigma_\alpha^2/\hbar^2}\right)}}.
$$

Use the exact normalization even when the two peaks are far apart. Include a numerical test that $\int |C(P)|^2\,dP=1$.

## One-absorber interaction matrix element

Define

$$
\mathcal V(n,m;P_a,P_b;a)
=\langle n(a),P_a|H_I|m(a),P_b\rangle.
$$

Let $q=P_a-P_b$, $n_< =\min(n,m)$, $n_>=\max(n,m)$, and

$$
\alpha=\sqrt{\frac{m\omega}{\hbar}}.
$$

Then

$$
\begin{aligned}
\mathcal V(n,m;P_a,P_b;a)
={}&\frac{V_0}{2\pi\hbar}\sqrt{2\pi\sigma_V^2}
e^{-iaq/\hbar}
e^{-q^2\sigma_V^2/(2\hbar^2)}\\
&\times\sqrt{\frac{n_<!}{n_>!}}
\left(-\frac{iq}{\sqrt2\hbar\alpha}\right)^{n_>-n_<}
e^{-q^2/(4\hbar^2\alpha^2)}
L_{n_<}^{(n_>-n_<)}\!\left(\frac{q^2}{2\hbar^2\alpha^2}\right).
\end{aligned}
$$

Implement factorial ratios through log-gamma functions when indices can be large. Use a standard evaluated generalized Laguerre polynomial, not symbolic algebra.

At $q=0$, explicitly enforce

$$
\mathcal V(n,m;P,P;a)
=\frac{V_0}{2\pi\hbar}\sqrt{2\pi\sigma_V^2}\,\delta_{nm},
$$

so that the code never evaluates an ambiguous $0^0$.

For the frequent ground-state case,

$$
\mathcal V(n,0;P_a,P_b;a)
=\frac{V_0}{2\pi\hbar}\sqrt{2\pi\sigma_V^2}
e^{-iaq/\hbar}
e^{-q^2\sigma_V^2/(2\hbar^2)}
\frac{1}{\sqrt{n!}}
\left(-\frac{iq}{\sqrt2\hbar\alpha}\right)^n
e^{-q^2/(4\hbar^2\alpha^2)}.
$$

## Stable finite-time factors

Set

$$
\tau=\frac{\Delta t}{\hbar},\qquad
\operatorname{sinc}(z)=\frac{\sin z}{z},\quad \operatorname{sinc}(0)=1.
$$

Use the unnormalized mathematical sinc convention. Note that `numpy.sinc(x)` evaluates $\sin(\pi x)/(\pi x)$, so the correct NumPy call is `np.sinc(z / np.pi)`.

The stable first-order factor is

$$
\mathcal E(E_f,E_i)
=-i\tau e^{-i\tau(E_f+E_i)/2}
\operatorname{sinc}\!\left[\frac{\tau(E_f-E_i)}2\right].
$$

Do not evaluate it using a quotient of nearly equal exponentials.

The formal second-order factor is

$$
\mathcal F(E_f,E_1,E_i)=\frac{1}{E_1-E_i}
\left[
\frac{e^{-i\tau E_i}-e^{-i\tau E_f}}{E_f-E_i}
-\frac{e^{-i\tau E_1}-e^{-i\tau E_f}}{E_f-E_1}
\right].
$$

Do not use this raw expression near degeneracies. The most robust reference implementation is the exact denominator-free representation

$$
\mathcal F(E_f,E_1,E_i)=-\tau^2\int_0^1ds\,(1-s)
\exp\left\{-i\tau\left[\frac{1-s}{2}(E_i+E_1)+sE_f\right]\right\}
\operatorname{sinc}\!\left[\frac{\tau(1-s)(E_1-E_i)}2\right].
$$

This additional one-dimensional integral can be evaluated with a fixed, verified Gauss-Legendre rule and vectorized over energy arrays. An optimized implementation may instead use analytic divided differences plus explicit near-degenerate branches, but it must agree with the denominator-free reference within the requested tolerance. In the fully degenerate case verify

$$
\mathcal F(E,E,E)=-\frac{\tau^2}{2}e^{-i\tau E}.
$$

## First-order amplitude

For final oscillator indices $(n_1,n_2)$,

$$
E_i(P)=\frac{P^2}{2M}+\hbar\omega,
\qquad
E_f(P_f)=\frac{P_f^2}{2M}+\hbar\omega(n_1+n_2+1).
$$

Compute

$$
\begin{aligned}
\mathcal A_{fi}^{(1)}(P_f;n_1,n_2)
=\int dP\,C(P)\big[&\delta_{n_2,0}\mathcal V(n_1,0;P_f,P;a_1)\\
&+\delta_{n_1,0}\mathcal V(n_2,0;P_f,P;a_2)\big]
\mathcal E(E_f,E_i(P)).
\end{aligned}
$$

The first-order amplitude must be exactly zero when both $n_1>0$ and $n_2>0$. The first-order differential density is

$$
\mathcal P^{(1)}(P_f,n_1,n_2)=|\mathcal A_{fi}^{(1)}|^2.
$$

## Second-order amplitude

Compute the coherent sum

$$
\mathcal A_{fi}^{(2)}
=\mathcal A_{20}+\mathcal A_{02}+\mathcal A_{11}^{(1\to2)}+\mathcal A_{11}^{(2\to1)}.
$$

Terms excluded by their Kronecker delta are exactly zero and should be skipped.

### Two interactions with absorber 1

$$
\begin{aligned}
\mathcal A_{20}={}&\delta_{n_2,0}
\sum_{r=0}^{r_{\max}}\int dP\int dP_1\,C(P)
\mathcal V(r,0;P_1,P;a_1)
\mathcal V(n_1,r;P_f,P_1;a_1)\\
&\times\mathcal F(E_f,E_1,E_i),
\end{aligned}
$$

with

$$
E_f=\frac{P_f^2}{2M}+\hbar\omega(n_1+1),\quad
E_1=\frac{P_1^2}{2M}+\hbar\omega(r+1),\quad
E_i=\frac{P^2}{2M}+\hbar\omega.
$$

### Two interactions with absorber 2

$$
\begin{aligned}
\mathcal A_{02}={}&\delta_{n_1,0}
\sum_{r=0}^{r_{\max}}\int dP\int dP_1\,C(P)
\mathcal V(r,0;P_1,P;a_2)
\mathcal V(n_2,r;P_f,P_1;a_2)\\
&\times\mathcal F(E_f,E_1,E_i),
\end{aligned}
$$

with

$$
E_f=\frac{P_f^2}{2M}+\hbar\omega(n_2+1),\quad
E_1=\frac{P_1^2}{2M}+\hbar\omega(r+1),\quad
E_i=\frac{P^2}{2M}+\hbar\omega.
$$

Both matrix elements in this term use $a_2$. This explicitly corrects the residual $a_1$ typo in the TeX source.

### One interaction with each absorber: order 1 then 2

$$
\begin{aligned}
\mathcal A_{11}^{(1\to2)}={}&\int dP\int dP_1\,C(P)
\mathcal V(n_1,0;P_1,P;a_1)
\mathcal V(n_2,0;P_f,P_1;a_2)\\
&\times\mathcal F(E_f,E_1^{(1)},E_i),
\end{aligned}
$$

where

$$
E_1^{(1)}=\frac{P_1^2}{2M}+\hbar\omega(n_1+1),\qquad
E_f=\frac{P_f^2}{2M}+\hbar\omega(n_1+n_2+1).
$$

### One interaction with each absorber: order 2 then 1

$$
\begin{aligned}
\mathcal A_{11}^{(2\to1)}={}&\int dP\int dP_1\,C(P)
\mathcal V(n_2,0;P_1,P;a_2)
\mathcal V(n_1,0;P_f,P_1;a_1)\\
&\times\mathcal F(E_f,E_1^{(2)},E_i),
\end{aligned}
$$

where

$$
E_1^{(2)}=\frac{P_1^2}{2M}+\hbar\omega(n_2+1).
$$

Do not add an intermediate-state sum to either mixed term: the Kronecker deltas fix the intermediate oscillator indices. Add all amplitude contributions before taking the modulus squared:

$$
\mathcal P^{(2)}(P_f,n_1,n_2)=|\mathcal A_{fi}^{(2)}|^2.
$$

If a combined perturbative prediction through second order is desired, clearly distinguish $|A^{(1)}+A^{(2)}|^2$ from the isolated order contributions. Do not silently add probabilities.

## Momentum-domain construction

The mathematical integrals run over the real line, but the numerical domains must be generated from the configured parameters and a tail tolerance. Never use a fixed interval copied from one parameter set.

Define the initial amplitude width

$$
w_C=\frac{\hbar}{\sigma_\alpha}
$$

and, for an amplitude tail threshold $\epsilon_{\rm tail}$,

$$
R_C=w_C\sqrt{2\log(1/\epsilon_{\rm tail})}.
$$

Start with the two intervals

$$
I_-=[-|P_0|-R_C,-|P_0|+R_C],\qquad
I_+=[|P_0|-R_C,|P_0|+R_C].
$$

- If the intervals overlap or touch, merge them and integrate over one connected interval.
- If they are disjoint, preserve them as two separate quadrature intervals. Do not integrate a huge mostly empty interval from approximately $-P_0$ to $+P_0$.
- Apply this logic continuously as `P0` changes; do not create separate hard-coded “large mass” and “small mass” cases.

The Gaussian envelope of every interaction matrix element has the momentum-transfer scale

$$
w_V=\frac{\hbar}{\sqrt{\sigma_V^2+1/(2\alpha^2)}}.
$$

A basic transfer cutoff is

$$
R_V=w_V\sqrt{2\log(1/\epsilon_{\rm tail})}.
$$

The polynomial factors associated with excited oscillator states can broaden the useful range beyond this basic Gaussian cutoff. Therefore `R_V` is only an initial proposal. Enlarge it until the boundary contribution and the resulting integral satisfy the convergence tests below.

For a fixed $P_f$, construct the second-order $P_1$ domain from both interactions:

1. Expand every connected initial $P$ interval by $R_V$; this enforces $P_1\approx P$ for the first interaction.
2. Intersect those expanded intervals with $[P_f-R_V,P_f+R_V]$; this enforces $P_1\approx P_f$ for the second interaction.
3. Integrate each surviving connected intersection separately.
4. If an intersection is empty, do not automatically return zero: enlarge `R_V` according to the tail/convergence procedure before deciding the contribution is negligible.

For first order, the integrand is significant only where an initial interval also lies within roughly $R_V$ of $P_f$. It is permissible to intersect the $P$ windows with $[P_f-R_V,P_f+R_V]$, subject again to domain-expansion verification.

When evaluating an entire $P_f$ grid, choose that grid from the union of the initial peaks broadened by the required one- or two-interaction momentum-transfer range. Preserve disconnected negative- and positive-momentum output windows when they remain separated. Since energy transfer and finite-time oscillations can shift or structure the dominant region, verify and enlarge the proposed $P_f$ windows rather than assuming their centers are exactly $\pm P_0$.

## Mandatory convergence procedure

For every new parameter set, or at least for representative edge points of a parameter sweep:

1. Compute with the proposed $P$, $P_1$, and $P_f$ windows.
2. Enlarge all momentum radii by a configurable factor such as 1.25–1.5 and recompute.
3. Refine the quadrature tolerances or node counts and recompute.
4. Increase `n_intermediate_max` for same-absorber second-order terms and recompute.
5. Accept the result only when the complex amplitudes, not merely their squared moduli, change by less than configured absolute and relative tolerances.

Report convergence metadata: domains used, connected components, quadrature errors, oscillator cutoff, and changes under refinement. A code path that returns a number without this validation is incomplete.

## Suggested software structure

Use small testable functions resembling:

```text
ModelParameters
MomentumDomainSettings
initial_coefficient(P, params)
interaction_matrix_element(n, m, Pa, Pb, a, params)
energy(n1, n2, P, params)
first_order_factor(Ef, Ei, params)
second_order_factor(Ef, E1, Ei, params)
initial_momentum_intervals(params, domain_settings)
intermediate_momentum_intervals(Pf, params, domain_settings)
first_order_amplitude(Pf, n1, n2, ...)
second_order_components(Pf, n1, n2, ...)
second_order_amplitude(Pf, n1, n2, ...)
convergence_report(...)
```

Use complex128 arithmetic by default. Integrate real and imaginary parts consistently if the quadrature library does not support complex integrands. Cache quadrature nodes, Laguerre values, energy arrays, and reusable matrix elements when scanning $P_f$.

## Required tests

- Numerical normalization of $C(P)$ for overlapping peaks ($P_0\lesssim\hbar/\sigma_\alpha$), well-separated peaks, and $P_0=0$.
- $\mathcal V(n,m;P,P;a)\propto\delta_{nm}$ and no NaN at $P_a=P_b$.
- Hermiticity:
  $\mathcal V(n,m;P_a,P_b;a)=\mathcal V(m,n;P_b,P_a;a)^*$.
- Stable $\mathcal E$ at $E_f=E_i$.
- Stable $\mathcal F$ for pairwise and complete energy degeneracy, including comparison with its denominator-free representation.
- First-order amplitude exactly zero for $n_1>0$ and $n_2>0$.
- Exchange symmetry under $(a_1,n_1)\leftrightarrow(a_2,n_2)$.
- Both mixed time orderings are included coherently.
- The $a_2$-only second-order term uses $a_2$ in both matrix elements.
- Results are stable when disconnected momentum windows are replaced by a sufficiently large enclosing interval.
- Results meet the stated tolerances under domain enlargement, quadrature refinement, and intermediate-state cutoff enlargement.

## Deliverables expected from the coding agent

1. A documented Python module implementing the equations above.
2. Automated unit tests and numerical convergence tests.
3. A minimal example configuration and script producing first- and second-order differential densities over a $P_f$ grid.
4. A convergence report for that example, including the automatically selected $P$ and $P_1$ intervals.
5. Clear warnings or failures when convergence is not achieved; never silently present an unconverged result.
