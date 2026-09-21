# Review of Part IV: Fourier-transformed Faraday tensor and the far-zone limit

This note reviews the direct, simplified, and finite-interval Fourier-transform derivations in Part IV. It records the far-zone reduction and the finite-distance issues found in the review; it does not modify the LaTeX derivations.

Relevant sources:

- `../shared_src/elm_el_sc/frequency-domain/FT-direct.tex`
- `../shared_src/elm_el_sc/frequency-domain/FT-simplified.tex`
- `../shared_src/elm_el_sc/frequency-domain/FT_simplified-numeric.tex`
- `../shared_src/elm_el_sc/time-domain/c1.tex`

## Findings

The leading far-distance term reduces to the standard expression containing
$\mathbf n\times(\mathbf n\times\boldsymbol\beta)/R$ after a longitudinal total derivative is integrated and its endpoint contribution is handled.

There is, however, an error in the simplified finite-distance derivation: the derivative in Jackson's formula holds the observation event fixed, whereas the derivative used for integration by parts follows changing retarded observation times. Interchanging these derivatives loses terms of order $1/R^2$. The finite-interval numerical formulas inherit this error.

Separately, the direct formulas labelled “exact” place $1/R(\tau)$ and $1/R(\tau)^2$ outside their integrals. These factors must remain inside unless an explicit far-distance approximation makes them constant.

## Conventions

The document uses

$$
\widetilde F^{\alpha\beta}(\omega,\mathbf x)
=\frac{1}{2\pi}\int_{-\infty}^{\infty}dt\,
e^{i\omega t}F^{\alpha\beta}(t,\mathbf x),
\qquad \widetilde E_i=c\widetilde F^{i0}.
$$

Write $t'$ for the particle's laboratory time and $\tau$ for its proper time. Then

$$
u^0\,d\tau=c\,dt',\qquad
\mathbf u\,d\tau=c\boldsymbol\beta\,dt',\qquad
\boldsymbol\beta=\frac{1}{c}\frac{d\mathbf r}{dt'}.
$$

Here and below $u^0$ is the time component of the four-velocity. Define

$$
R(t')=|\mathbf x-\mathbf r(t')|,\qquad
\mathbf n(t')=\frac{\mathbf x-\mathbf r(t')}{R(t')},\qquad
k=\frac{\omega}{c}.
$$

The observation time is $t=t'+R(t')/c$. The charge $e$ is signed, as in the document.

## Reduction to the standard far-zone electric field

The simplified leading term, using $\widetilde E_i=c\widetilde F^{i0}$, becomes

$$
\widetilde{\mathbf E}_{l}
=-\frac{ie\omega}{8\pi^2\epsilon_0c}
\int dt'\,\frac{\mathbf n-\boldsymbol\beta}{R}
e^{i\omega(t'+R/c)}.
$$

In the far zone, use $R\simeq R_{\mathrm{obs}}$ in the amplitude and

$$
R\simeq R_{\mathrm{obs}}-\mathbf n\cdot\mathbf r(t')
$$

in the phase, with constant $\mathbf n$. This assumes the relevant trajectory segment is small compared with the observation distance; neglecting the next phase term also requires the usual Fraunhofer condition at the frequency considered.

The key vector identity is

$$
\mathbf n-\boldsymbol\beta
=\mathbf n\times(\mathbf n\times\boldsymbol\beta)
+\mathbf n(1-\mathbf n\cdot\boldsymbol\beta).
$$

For $\psi(t')=\omega[t'-\mathbf n\cdot\mathbf r(t')/c]$,

$$
(1-\mathbf n\cdot\boldsymbol\beta)e^{i\psi}
=\frac{1}{i\omega}\frac{d}{dt'}e^{i\psi}.
$$

The longitudinal term is therefore an endpoint contribution. When that contribution vanishes, or is handled with the appropriate convergence prescription, the result is

$$
\boxed{
\widetilde{\mathbf E}_{\mathrm{rad}}
=-\frac{ie\omega e^{ikR_{\mathrm{obs}}}}
{8\pi^2\epsilon_0cR_{\mathrm{obs}}}
\int dt'\,
\mathbf n\times(\mathbf n\times\boldsymbol\beta)
e^{i\omega[t'-\mathbf n\cdot\mathbf r(t')/c]}.
}
$$

This is the standard velocity representation with the document's Fourier normalization. The numerator replacement is valid under the integral after accounting for endpoints; it is not a pointwise equality.

For a finite interval $[t'_a,t'_b]$, the longitudinal contribution from the leading integral alone is explicitly

$$
-\frac{e e^{ikR_{\mathrm{obs}}}}
{8\pi^2\epsilon_0cR_{\mathrm{obs}}}
\mathbf n\,[e^{i\psi}]_{t'_a}^{t'_b}.
$$

It must be combined with the boundary terms of the chosen finite-interval representation. It cannot simply be discarded at nonzero frequency.

For comparison, the standard integration-by-parts reduction appears in [Likharev, *Essential Graduate Physics: Classical Electrodynamics*, Section 10.3, Eqs. (10.59)–(10.61)](https://phys.libretexts.org/Bookshelves/Electricity_and_Magnetism/Essential_Graduate_Physics_-_Classical_Electrodynamics_(Likharev)/10%3A_Radiation_by_Relativistic_Charges/10.03%3A_Synchrotron_Radiation).

## The derivative error in the simplified derivation

Jackson's representation is

$$
F^{\alpha\beta}(x)
=\left.\frac{e}{4\pi\epsilon_0c}\frac{1}{u\cdot R}
\left(\frac{d}{d\tau}\right)_{x}
\left[\frac{R^\alpha u^\beta-R^\beta u^\alpha}{R\cdot u}\right]
\right|_{\tau=\tau_r}.
$$

During this derivative the observation event $x^\mu$ is fixed:

$$
R^\mu=x^\mu-r^\mu(\tau),\qquad
\left(\frac{dR^\mu}{d\tau}\right)_x=-u^\mu.
$$

Only after differentiation is the retarded condition imposed. In particular, $R^0$ must not be replaced by $|\mathbf R|$ before performing this fixed-event derivative.

After changing the Fourier integration variable to $\tau$, the spatial observation point is fixed but its observation time varies. Along this integration path,

$$
R^\mu(\tau)=(R(\tau),\mathbf x-\mathbf r(\tau)),\qquad
\frac{dR^\mu}{d\tau}
=(-\mathbf n\cdot\mathbf u,-\mathbf u),
$$

which differs from $-u^\mu=(-u^0,-\mathbf u)$.

The problematic step is the replacement inside the derivative in `FT-simplified.tex`, at the equation labelled `eq:ft-simplified-total-integral`, followed by ordinary integration by parts. The change-of-variable Jacobian itself,

$$
\frac{dt}{d\tau}=\frac{u^0-\mathbf n\cdot\mathbf u}{c},
$$

is correct. The issue is what is held fixed during the other derivative.

A simple diagnostic is a stationary charge. Along the retarded integration path, $(n^\alpha u^\beta-n^\beta u^\alpha)/(n\cdot u)$ is constant, so its ordinary path derivative is zero. Jackson's fixed-event derivative nevertheless gives the nonzero Coulomb field. For an infinite-time Fourier transform that static field lies at zero frequency, but the time-domain test directly exposes the derivative mismatch.

## Independent check from the potentials

For the full time transform, changing variables in the Liénard–Wiechert potentials gives

$$
\widetilde\Phi
=\frac{e}{8\pi^2\epsilon_0}\int dt'\,
\frac{e^{i\omega(t'+R/c)}}{R},\qquad
\widetilde{\mathbf A}
=\frac{e}{8\pi^2\epsilon_0c}\int dt'\,
\frac{\boldsymbol\beta}{R}e^{i\omega(t'+R/c)}.
$$

Using $\widetilde{\mathbf E}=-\boldsymbol\nabla\widetilde\Phi+i\omega\widetilde{\mathbf A}$ and

$$
\boldsymbol\nabla\frac{e^{ikR}}{R}
=\mathbf n e^{ikR}\left(\frac{ik}{R}-\frac{1}{R^2}\right),
$$

one obtains

$$
\boxed{
\widetilde{\mathbf E}(\omega,\mathbf x)
=\frac{e}{8\pi^2\epsilon_0}
\int dt'\,e^{i\omega(t'+R/c)}
\left[\frac{\mathbf n}{R^2}
-\frac{i\omega}{cR}(\mathbf n-\boldsymbol\beta)\right].
}
$$

This expression assumes vanishing time-boundary contributions, or a compatible regularized full transform. It confirms the leading term while giving a different $1/R^2$ term from the current simplified derivation. A time-windowed transform requires explicit endpoint bookkeeping and is not obtained by silently truncating this full-transform formula.

## Implications for the document

1. In the direct derivation, keep all trajectory-dependent distance factors inside the integrals before making a far-zone approximation.
2. In the simplified derivation, distinguish the fixed-event derivative from the derivative along the retarded integration path and restore the resulting finite-distance terms.
3. Re-derive the finite-interval numerical formula with that distinction. Its existing boundary term alone does not repair the missing bulk terms.
4. State endpoint or convergence assumptions explicitly. The claim that boundary terms contribute only at zero frequency is not generally valid for a finite interval.
5. Add the far-zone reduction above to explain how the double-cross-product form emerges.

The earlier review of the OAM/SAM sections checked those sections' formulas; it did not establish the correctness of the Fourier-transform chapter that supplies their fields.
