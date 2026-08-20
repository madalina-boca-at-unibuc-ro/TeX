# Numerical Formulation: First-Order Transition Amplitude for One Absorber

This document provides the mathematical formulas, physical parameters, and numerical implementation details for calculating the first-order transition amplitude of a system consisting of a free alpha particle and a single atom (modeled as a 1D harmonic oscillator).

---

## 1. Physical Model and Hamiltonian

The system consists of:
1. A free alpha particle of mass $M$ with position $X$ and momentum $P_X$.
2. A single atom of mass $m$ modeled as a 1D harmonic oscillator of frequency $\omega_0$, centered at position $a_1$, with coordinate $x$ and momentum $P_x$.
3. A Gaussian interaction potential between the alpha particle and the atom.

The total Hamiltonian is:
$$H = H_0 + H_I$$

Where the unperturbed Hamiltonian is:
$$H_0 = \frac{P_X^2}{2M} + \frac{P_x^2}{2m} + \frac{1}{2}m\omega_0^2(x-a_1)^2$$

And the interaction perturbation is:
$$H_I = V_0 e^{-\frac{(x-X)^2}{2\sigma_V^2}}$$
*(Note: $\sigma_V$ represents the range/width of the interaction potential).*

---

## 2. Key Formulas

### 2.1 Initial Wave Packet in Momentum Space
The initial state of the alpha particle in position space is a bimodal wave packet:
$$\psi_\alpha(X) = \frac{1}{\sqrt{\sigma_\alpha\sqrt{\pi}}}\sqrt{\frac{2}{1+e^{-\frac{P_0^2\sigma_\alpha^2}{\hbar^2}}}}e^{-\frac{X^2}{2\sigma_\alpha^2}}\cos\left(\frac{P_0 X}{\hbar}\right)$$

Its Fourier transform (expansion coefficient in the momentum basis), $C(P) = \langle P | \psi_\alpha \rangle$, is:
$$C(P) = \sqrt{\frac{\sigma_\alpha}{2\hbar\sqrt{\pi}\left(1+e^{-\frac{P_0^2\sigma_\alpha^2}{\hbar^2}}\right)}}\left[ e^{-\frac{(P-P_0)^2\sigma_\alpha^2}{2\hbar^2}} + e^{-\frac{(P+P_0)^2\sigma_\alpha^2}{2\hbar^2}} \right]$$

### 2.2 Matrix Element of the Interaction Potential
The transition matrix element from the initial state where the oscillator is in the ground state ($n=0$) and the alpha particle has momentum $P$ to a final state with oscillator state $n_f$ and alpha particle momentum $P_f$ is:
$$\langle P_f, n_f | H_I | P, 0 \rangle = \frac{V_0 \sigma_V}{\sqrt{2\pi}\hbar \color{red}{\sqrt{n_f!}}} e^{-\frac{(P_f-P)^2}{2\hbar^2}\left(\sigma_V^2 + \frac{\alpha^2}{2}\right)} e^{-\frac{i}{\hbar}a_1(P_f-P)} \left( -\frac{i (P_f-P) \alpha}{\sqrt{2}\hbar} \right)^{n_f}$$
where:
* $\alpha = \sqrt{\frac{\hbar}{m\omega_0}}$ is the characteristic length scale of the harmonic oscillator.
* The associated Laguerre polynomial $L_0^{(n_f)}(z)$ has been replaced by $1$.
* <span style="color:red">**Correction:** The factor $\sqrt{n_f!}$ has been moved to the denominator to match the corrected LaTeX derivation.</span>

### 2.3 First-Order Transition Amplitude
For a given final state $(P_f, n_f)$, the transition amplitude after an interaction time $\Delta t = t_f - t_i$ is computed by integrating over the momentum $P$ of the initial wave packet:
$$\mathcal{A}_{fi}^{(1)}(P_f, n_f) = \int_{-\infty}^{\infty} dP \, C(P) \, \langle P_f, n_f | H_I | P, 0 \rangle \, f(E_d)$$

where the energy difference $E_d$ is:
$$E_d(P) = n_f \hbar \omega_0 + \frac{P_f^2 - P^2}{2M}$$

and the time-dependent transition factor $f(E_d)$ is defined as:
$$f(E_d) = \frac{1 - e^{\frac{i}{\hbar} E_d \Delta t}}{E_d}$$

---

## 3. Parameter Reference

| Symbol          | Description                                           | Physical Component  |
| :-------------- | :---------------------------------------------------- | :------------------ |
| $M$             | Mass of the alpha particle                            | Free particle       |
| $m$             | Mass of the harmonic oscillator                       | Absorbing atom      |
| $\omega_0$      | Angular frequency of the oscillator                   | Absorbing atom      |
| $\alpha$        | Oscillator length scale $\sqrt{\hbar / (m \omega_0)}$ | Absorbing atom      |
| $a_1$           | Position of the absorber center                       | Absorbing atom      |
| $V_0$           | Strength of the interaction potential                 | Interaction         |
| $\sigma_V$      | Range/width of the interaction potential              | Interaction         |
| $P_0$           | Mean initial momentum of the alpha particle           | Initial Wave Packet |
| $\sigma_\alpha$ | Spatial width of the alpha wave packet                | Initial Wave Packet |
| $\Delta t$      | Interaction time duration $t_f - t_i$                 | Time evolution      |
| $P_f$           | Final momentum of the alpha particle                  | Final state         |
| $n_f$           | Final quantum state of the oscillator                 | Final state         |

---

## 4. Numerical Implementation Guidelines for an AI Agent

### 4.1 Handling the Removable Singularity
The factor $f(E_d) = \frac{1 - e^{\frac{i}{\hbar} E_d \Delta t}}{E_d}$ has a removable singularity at $E_d = 0$. Naive evaluation will cause division-by-zero errors.
To handle this stably:
* Use a Taylor expansion or the complex sinc representation when $|E_d|$ is very small.
* Since $1 - e^{ix} = -2i e^{ix/2} \sin(x/2)$, we can write:
  $$f(E_d) = -\frac{i \Delta t}{\hbar} e^{\frac{i E_d \Delta t}{2\hbar}} \text{sinc}\left( \frac{E_d \Delta t}{2\hbar} \right)$$
  where $\text{sinc}(x) = \frac{\sin x}{x}$ (and $\text{sinc}(0) = 1$).

### 4.2 Integration Limits and Grid
The integrand is modulated by $C(P)$, which consists of two Gaussians centered at $P = P_0$ and $P = -P_0$, each with standard deviation $\sigma_P = \frac{\hbar}{\sigma_\alpha}$.
* The significant support of the integrand is restricted to $P \in [-P_0 - 5\sigma_P, -P_0 + 5\sigma_P] \cup [P_0 - 5\sigma_P, P_0 + 5\sigma_P]$.
* For numerical integration (e.g., using `scipy.integrate.quad`), specify these intervals or set the integration bounds around the peaks to avoid precision loss or missing the peaks entirely.

### 4.3 Python Template (Scipy implementation)

```python
import numpy as np
import scipy.integrate as integrate

# Physical constants (atomic units or SI: must be self-consistent)
hbar = 1.0

def compute_amplitude(Pf, nf, params):
    """
    Computes the first-order transition amplitude A_fi^(1)(Pf, nf).
    
    params is a dictionary containing:
    M, m, omega0, a1, V0, sigma_V, P0, sigma_alpha, delta_t
    """
    M = params['M']
    m = params['m']
    omega0 = params['omega0']
    a1 = params['a1']
    V0 = params['V0']
    sigma_V = params['sigma_V']
    P0 = params['P0']
    sigma_alpha = params['sigma_alpha']
    delta_t = params['delta_t']
    
    # Derived parameters
    alpha = np.sqrt(hbar / (m * omega0))
    sigma_P = hbar / sigma_alpha
    
    # Normalization factor for C(P)
    C_norm = np.sqrt(sigma_alpha / (2.0 * hbar * np.sqrt(np.pi) * (1.0 + np.exp(-(P0 * sigma_alpha / hbar)**2))))
    
    def C(P):
        return C_norm * (np.exp(-((P - P0) * sigma_alpha)**2 / (2.0 * hbar**2)) + 
                         np.exp(-((P + P0) * sigma_alpha)**2 / (2.0 * hbar**2)))
    
    def matrix_element(P):
        pref = (V0 * sigma_V) / (np.sqrt(2.0 * np.pi) * hbar * np.sqrt(np.math.factorial(nf)))  # Corrected: factor in denominator
        gauss = np.exp(-((Pf - P)**2) / (2.0 * hbar**2) * (sigma_V**2 + 0.5 * alpha**2))
        phase = np.exp(-1j * a1 * (Pf - P) / hbar)
        poly_term = (-1j * (Pf - P) * alpha / (np.sqrt(2.0) * hbar))**nf
        return pref * gauss * phase * poly_term
    
    def f_Ed(P):
        Ed = nf * hbar * omega0 + (Pf**2 - P**2) / (2.0 * M)
        x = Ed * delta_t / (2.0 * hbar)
        # Safe sinc evaluation
        sinc_val = np.sinc(x / np.pi) # np.sinc defines sinc(x) as sin(pi*x)/(pi*x)
        return (-1j * delta_t / hbar) * np.exp(1j * x) * sinc_val

    # Full integrand (complex valued)
    def integrand(P):
        return C(P) * matrix_element(P) * f_Ed(P)

    # Real and imaginary parts for quad integration
    def integrand_real(P): return np.real(integrand(P))
    def integrand_imag(P): return np.imag(integrand(P))
    
    # We integrate over regions of interest (peaks around P0 and -P0)
    # to ensure quad doesn't miss the narrow Gaussians
    w = 5.0 * sigma_P
    regions = [
        (-P0 - w, -P0 + w),
        (P0 - w, P0 + w)
    ]
    
    real_part = 0.0
    imag_part = 0.0
    
    for left, right in regions:
        r_val, _ = integrate.quad(integrand_real, left, right, limit=100)
        i_val, _ = integrate.quad(integrand_imag, left, right, limit=100)
        real_part += r_val
        imag_part += i_val
        
    return real_part + 1j * imag_part
```
