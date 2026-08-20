# Numerical Formulation: Two-Absorber TDSE

This note summarizes the implementation-relevant results from [two_absorbers_numerical.tex](two_absorbers_numerical.tex) for a Python solver.

## 1. Model

We solve the time-dependent Schr"odinger equation for an alpha particle coordinate `X` coupled to two 1D harmonic-oscillator absorbers with coordinates `x1` and `x2`:

$$
i\hbar\frac{\partial}{\partial t}\psi(X,x_1,x_2,t)=H\psi(X,x_1,x_2,t)
$$

with Hamiltonian

$$
H=-\frac{\hbar^2}{2M}\frac{\partial^2}{\partial X^2}-\sum_{i=1}^2\frac{\hbar^2}{2m}\frac{\partial^2}{\partial x_i^2}+\sum_{i=1}^2\frac12 m\omega_0^2(x_i-a_i)^2+\sum_{i=1}^2V_0\exp\!\left[-\frac{(x_i-X)^2}{2\sigma_V^2}\right]
$$

Parameters:
- `M`: alpha-particle mass
- `m`: absorber mass
- `omega0`: oscillator frequency
- `a1, a2`: absorber equilibrium positions
- `V0`: Gaussian interaction strength
- `sigma_V`: Gaussian interaction width

## 2. Basis expansion

Expand the full wave function in the tensor product of oscillator Fock states:

$$
\psi(X,x_1,x_2,t)=\sum_{n_1\ge 0}\sum_{n_2\ge 0}\Psi_{n_1 n_2}(X,t)\,|n_1(a_1)\rangle|n_2(a_2)\rangle
$$

After projection, the TDSE becomes a coupled system for the coefficient functions `Psi[n1,n2](X,t)`:

$$
i\hbar\frac{\partial\Psi_{n_1n_2}(X,t)}{\partial t}
=-\frac{\hbar^2}{2M}\frac{\partial^2}{\partial X^2}\Psi_{n_1n_2}(X,t)
+\hbar\omega(n_1+n_2+1)\Psi_{n_1n_2}(X,t)
+\sum_{m_1,m_2\ge 0}V_{n_1n_2,m_1m_2}(X)\Psi_{m_1m_2}(X,t)
$$

where `omega` is the oscillator frequency used in the source text.

## 3. Coupling matrix

The two-absorber coupling matrix is the sum of two one-absorber couplings:

$$
V_{n_1n_2,m_1m_2}(X)
=\langle n_1(a_1)|V_0 e^{-\frac{(x_1-X)^2}{2\sigma_V^2}}|m_1(a_1)\rangle\,\delta_{n_2m_2}
+\langle n_2(a_2)|V_0 e^{-\frac{(x_2-X)^2}{2\sigma_V^2}}|m_2(a_2)\rangle\,\delta_{n_1m_1}
$$

Implementation note:
- Reuse the one-absorber matrix element routine for each absorber.
- For equal absorber parameters, the two terms have the same functional form, only the center `a_i` changes.

## 4. Initial condition

Both absorbers start in their ground states, and the alpha-particle spatial wave packet is the same as in the one-absorber problem:

$$
\Psi_{n_1n_2}(X,0)=|0(a_1)\rangle|0(a_2)\rangle|\psi_0(X,0)\rangle\,\delta_{n_1,0}\delta_{n_2,0}
$$

So, in code:
- `Psi[0,0](X,0) = psi0(X,0)`
- all other channels are zero at `t = 0`

## 5. Channel size and indexing

If `N` Fock states are kept per absorber, the channel space has size `N^2`.

Use a flattening map such as

$$
k = n_1 N + n_2
$$

for the numerical state vector / matrix representation.

Recommended inverse map:
- `n1 = k // N`
- `n2 = k % N`

This is needed for assembling the coupled PDE system and for building the potential matrix.

## 6. Numerical method

The source text uses a split-operator method based on a Trotter-Suzuki factorization:

$$
U(\Delta t)=e^{-\frac{i}{\hbar}H\Delta t}\approx e^{-\frac{i}{\hbar}V\Delta t/2}e^{-\frac{i}{\hbar}T\Delta t}e^{-\frac{i}{\hbar}V\Delta t/2}
$$

with
- `T`: kinetic operator
- `V`: potential operator

### Kinetic step
- Diagonal in momentum space.
- Apply with FFT in the `X` coordinate.

### Potential step
- Diagonal in position space only after converting the channel coupling matrix at each `X` grid point.
- For each `X`, build the `N^2 x N^2` potential matrix in the oscillator basis, diagonalize it, apply the diagonal phase, then transform back.

## 7. Minimal implementation outline

1. Choose `N`, spatial grid in `X`, time step `dt`, and all physical parameters.
2. Build the initial channel vector with only `(n1,n2)=(0,0)` populated.
3. For each time step:
   - apply half potential step in channel space at each `X`
   - FFT in `X`
   - apply kinetic phase
   - inverse FFT
   - apply the second half potential step
4. After propagation, reconstruct observables from the channel amplitudes.

## 8. Practical notes

- The channel count grows as `N^2`, so memory and runtime scale quickly.
- Use `float64` / `complex128`.
- Keep the channel flattening consistent everywhere: wave-function storage, matrix assembly, and diagnostics.
- The one-absorber matrix element formula from the companion note should be treated as a reusable primitive.
