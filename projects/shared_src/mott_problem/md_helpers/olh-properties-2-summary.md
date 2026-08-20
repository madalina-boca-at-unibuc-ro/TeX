# Summary for Python implementation: Gaussian matrix elements in displaced harmonic-oscillator basis

This document summarizes the results in [olh-properties-2.tex](olh-properties-2.tex) for coding.

## Goal
Compute matrix elements

$$
V_{nm}(X,\sigma)=\langle u_n|V_0\exp\!\left[-\frac{(x-X)^2}{2\sigma^2}\right]|u_m\rangle
$$

in the 1D harmonic-oscillator (HO) basis.

## Definitions
- HO scale: \(\alpha=\sqrt{M\omega/\hbar}\)
- Width helper:

$$
D=\frac{1}{\alpha^2}+2\sigma^2
$$

- Coefficients used in generating-function and recurrences:

$$
p=\frac{2X}{\alpha D},\qquad q=-\frac{1}{\alpha^2D},\qquad r=\frac{4\sigma^2}{D}
$$

- Common prefactor:

$$
C=\sqrt{\frac{2\sigma^2}{D}}\exp\!\left(-\frac{X^2}{D}\right)
$$

## Generating-function result

$$
\mathcal G(s,t)=C\exp\!\left[p(s+t)+q(s^2+t^2)+rst\right]
$$

and

$$
V_{nm}=V_0\frac{1}{\sqrt{2^{n+m}n!m!}}\left.\partial_s^n\partial_t^m\mathcal G(s,t)\right|_{s=t=0}
$$

## Practical recurrence (recommended for code)
Use directly on normalized matrix elements:

$$
\sqrt{n+1}\,V_{n+1,m}
=
\frac{\sqrt{2}X}{\alpha D}V_{n,m}
-\frac{1}{\alpha^2D}\sqrt{n}\,V_{n-1,m}
+\frac{2\sigma^2}{D}\sqrt{m}\,V_{n,m-1}
$$

Equivalent recurrence in second index:

$$
\sqrt{m+1}\,V_{n,m+1}
=
\frac{\sqrt{2}X}{\alpha D}V_{n,m}
-\frac{1}{\alpha^2D}\sqrt{m}\,V_{n,m-1}
+\frac{2\sigma^2}{D}\sqrt{n}\,V_{n-1,m}
$$

Symmetry:

$$
V_{nm}=V_{mn}
$$

## Initialization
- Base value:

$$
V_{00}=V_0\sqrt{\frac{2\sigma^2}{D}}\exp\!\left(-\frac{X^2}{D}\right)
$$

Equivalent form (from simple checks):

$$
V_{00}=V_0\sqrt{\frac{2\sigma^2\alpha^2}{1+2\sigma^2\alpha^2}}
\exp\!\left(-\frac{\alpha^2X^2}{1+2\sigma^2\alpha^2}\right)
$$

- First column seed:

$$
V_{10}=\frac{\sqrt{2}X}{\alpha D}V_{00}
$$

- First-column recurrence (set \(m=0\)):

$$
V_{n+1,0}=\frac{1}{\sqrt{n+1}}
\left[
\frac{\sqrt{2}X}{\alpha D}V_{n,0}
-\frac{1}{\alpha^2D}\sqrt{n}\,V_{n-1,0}
\right]
$$

- First row from symmetry: \(V_{0m}=V_{m0}\)

- Full matrix recurrence (for \(n\ge 0, m\ge 1\)):

$$
V_{n+1,m}=\frac{1}{\sqrt{n+1}}
\left[
\frac{\sqrt{2}X}{\alpha D}V_{n,m}
-\frac{1}{\alpha^2D}\sqrt{n}\,V_{n-1,m}
+\frac{2\sigma^2}{D}\sqrt{m}\,V_{n,m-1}
\right]
$$

## Suggested algorithm (NxN matrix)
Input: `N, alpha, X, sigma, V0`
Output: `V` shape `(N, N)`.

1. Compute `D`, `a1 = sqrt(2)*X/(alpha*D)`, `a2 = 1/(alpha**2*D)`, `a3 = 2*sigma**2/D`.
2. Initialize `V = zeros((N,N), dtype=float)`.
3. Set `V[0,0]` from formula.
4. If `N > 1`, set `V[1,0] = a1*V[0,0]`.
5. Fill first column for `n = 1..N-2`:
   - `V[n+1,0] = (a1*V[n,0] - a2*sqrt(n)*V[n-1,0]) / sqrt(n+1)`.
6. Mirror first row: `V[0,m] = V[m,0]`.
7. Fill interior by rows (or columns), e.g. for `m = 1..N-1`, `n = 0..N-2`:
   - `term_n = a1*V[n,m]`
   - `term_nm1 = a3*sqrt(m)*V[n,m-1]`
   - `term_nm = a2*sqrt(n)*V[n-1,m]` if `n > 0`, else `0`
   - `V[n+1,m] = (term_n - term_nm + term_nm1)/sqrt(n+1)`.
8. Optionally enforce symmetry numerically:
   - `V = 0.5*(V + V.T)`.

## Optional direct finite-sum formula (for validation)

$$
V_{nm}=V_0\sqrt{\frac{2\sigma^2}{D}}e^{-X^2/D}\frac{\sqrt{n!m!}}{2^{(n+m)/2}}
\sum_{\substack{i,j,k\ge0\\2i+k\le n\\2j+k\le m}}
\frac{q^{i+j}r^k p^{n+m-2i-2j-2k}}{i!\,j!\,k!\,(n-2i-k)!\,(m-2j-k)!}
$$

Use this only for low indices due to cost.

## Validation checks to include in Python tests
1. Symmetry: `V[n,m] == V[m,n]` within tolerance.
2. Parity for centered Gaussian (`X=0`): `V[n,m] = 0` when `n+m` is odd.
3. Broad Gaussian limit (`sigma -> inf`):

$$
V_{nm}\to V_0\delta_{nm}
$$

4. Low-order formulas:

$$
V_{10}=\frac{\sqrt{2}X}{\alpha D}V_{00}
$$

$$
V_{11}=\left[\frac{2X^2}{\alpha^2D^2}+\frac{2\sigma^2}{D}\right]V_{00}
$$

## Numerical notes
- Use `float64`.
- For large `N`, recurrence is much faster than finite-sum evaluation.
- Prefer tolerance-based tests (`rtol`, `atol`) rather than exact equality.
- If parameters are extreme, consider comparing scaled quantities to avoid underflow in `exp(-X^2/D)`.
