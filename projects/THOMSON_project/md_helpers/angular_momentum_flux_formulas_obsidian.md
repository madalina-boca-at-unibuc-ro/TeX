# Angular-momentum flux formulas for the scattered field

This note collects a practical set of formulas for computing the **total**, **extrinsic**, **intrinsic**, **spin**, and **intrinsic orbital** angular-momentum fluxes through a planar detector.

The formulas below are written in **Gaussian units** and for a monochromatic Fourier component with complex amplitudes

$$
\mathbf E(\mathbf r,t)=\Re\!\left[\mathbf E(\mathbf r,\omega)e^{-i\omega t}\right],
\qquad
\mathbf B(\mathbf r,t)=\Re\!\left[\mathbf B(\mathbf r,\omega)e^{-i\omega t}\right].
$$

They are therefore directly suited to a calculation in which the Liénard–Wiechert fields are Fourier transformed first.

> **Important:** if a different Fourier normalization or complex-amplitude convention is used, the common numerical prefactors may change. The geometrical structure of all formulas is unchanged.

---

## 1. Geometry

Take the detector to be the plane

$$
z=Z,
$$

with transverse coordinates

$$
\mathbf r_\perp=(x,y).
$$

The desired angular-momentum component is the one about the laser axis,

$$
J_z.
$$

The surface element is

$$
dA=dx\,dy.
$$

---

## 2. Time-averaged Poynting vector

For one Fourier component,

$$
\boxed{
\langle \mathbf S\rangle
=
\frac{c}{8\pi}
\Re\!\left(\mathbf E\times\mathbf B^*\right)
}
$$

### Meaning

$\langle \mathbf S\rangle$ is the energy-flux density through space.

The normal energy flux through the screen is

$$
\boxed{
I_z(x,y,\omega)=\langle S_z\rangle .
}
$$

Its integral gives the spectral power crossing the detector,

$$
\boxed{
\mathcal P(\omega)=
\int dA\,\langle S_z\rangle .
}
$$

---

## 3. Maxwell stress tensor

For complex monochromatic fields, the cycle-averaged Maxwell stress tensor is

$$
\boxed{
\langle T_{ij}\rangle
=
\frac{1}{8\pi}
\Re\!\left[
E_iE_j^*
+
B_iB_j^*
-
\frac12\delta_{ij}
\left(
|\mathbf E|^2+|\mathbf B|^2
\right)
\right].
}
$$

For a plane \(z=\mathrm{const.}\), the components

$$
T_{xz},\qquad T_{yz},\qquad T_{zz}
$$

are the fluxes of \(x\)-, \(y\)-, and \(z\)-momentum through the screen.

Define the integrated transverse momentum fluxes

$$
\boxed{
\dot P_x(\omega)=\int dA\,T_{xz},
\qquad
\dot P_y(\omega)=\int dA\,T_{yz}.
}
$$

These are the quantities relevant for testing the origin dependence of \(J_z\).

---

## 4. Total \(z\)-angular-momentum flux density

The exact flux density of \(z\)-angular momentum through the plane is

$$
\boxed{
\mathcal M_z^{\rm tot}(x,y,\omega)
=
x\,T_{yz}
-
y\,T_{xz}.
}
$$

### Meaning

At each screen point this is the flux, per unit area, of angular momentum about the laboratory \(z\)-axis.

The integrated total flux is

$$
\boxed{
\dot J_z^{\rm tot}(\omega)
=
\int dA\,
\mathcal M_z^{\rm tot}
=
\int dA\,
\left(xT_{yz}-yT_{xz}\right).
}
$$

No spatial differentiation of the field is required.

---

## 5. Radiation centroid on the detector

Use the normal energy flux as the weight:

$$
\boxed{
X_c(\omega)
=
\frac{
\int dA\,x\,S_z
}{
\int dA\,S_z
},
\qquad
Y_c(\omega)
=
\frac{
\int dA\,y\,S_z
}{
\int dA\,S_z
}.
}
$$

### Meaning

$(X_c,Y_c)$ is the centroid of the radiation crossing the screen at frequency $\omega$.

For a broadband pulse one may either construct a frequency-dependent centroid or form a frequency-integrated centroid, depending on the observable of interest.

---

## 6. Extrinsic \(z\)-angular-momentum flux

The extrinsic contribution associated with the centroid trajectory is

$$
\boxed{
\dot J_z^{\rm ext}
=
X_c\,\dot P_y
-
Y_c\,\dot P_x.
}
$$

Equivalently, a local surface representation is

$$
\boxed{
\mathcal M_z^{\rm ext}(x,y)
=
X_c\,T_{yz}(x,y)
-
Y_c\,T_{xz}(x,y).
}
$$

Its integral is exactly

$$
\int dA\,\mathcal M_z^{\rm ext}
=
X_c\dot P_y-Y_c\dot P_x.
$$

### Important test

If

$$
\boxed{
\dot P_x\simeq 0,
\qquad
\dot P_y\simeq 0,
}
$$

then

$$
\boxed{
\dot J_z^{\rm ext}\simeq0.
}
$$

Thus a nonzero $J_z$ cannot then be attributed to the ordinary centroid-type extrinsic angular momentum.

Note that $\dot P_z$ is of course nonzero for a forward beam; this does not affect the origin dependence of $J_z$.

---

## 7. Total intrinsic angular-momentum flux

Subtract the centroid contribution:

$$
\boxed{
\dot J_z^{\rm int}
=
\dot J_z^{\rm tot}
-
\dot J_z^{\rm ext}.
}
$$

Equivalently, directly about the radiation centroid,

$$
\boxed{
\mathcal M_z^{\rm int}(x,y)
=
(x-X_c)T_{yz}
-
(y-Y_c)T_{xz}.
}
$$

and

$$
\boxed{
\dot J_z^{\rm int}
=
\int dA\,\mathcal M_z^{\rm int}.
}
$$

This is the total angular momentum about the beam centroid and is therefore translation-invariant with respect to the usual extrinsic contribution.

---

# 8. Spin angular-momentum flux

For monochromatic free-space fields, the \(z\)-spin flux through a plane normal to \(z\) can be written without transverse spatial derivatives.

A convenient exact flux-tensor expression gives

$$
\boxed{
\mathcal M_z^{\rm spin}
=
\frac{c}{8\pi\omega}
\operatorname{Im}
\left[
\mathbf B^*\!\cdot\mathbf E
-
2B_z^*E_z
\right].
}
$$

More generally, the spin angular-momentum flux tensor can be written

$$
\boxed{
\Sigma_{ij}
=
\frac{c}{8\pi\omega}
\operatorname{Im}
\left[
\delta_{ij}\,\mathbf B^*\!\cdot\mathbf E
-
B_i^*E_j
-
B_j^*E_i
\right].
}
$$

For \(z\)-angular momentum flowing through the \(z=\mathrm{const.}\) detector,

$$
\mathcal M_z^{\rm spin}=\Sigma_{zz}.
$$

The integrated spin flux is therefore

$$
\boxed{
\dot J_z^{\rm spin}(\omega)
=
\int dA\,
\mathcal M_z^{\rm spin}(x,y,\omega).
}
$$

### Paraxial limit

If

$$
E_z\simeq0,
\qquad
B_z\simeq0,
$$

then

$$
\boxed{
\mathcal M_z^{\rm spin}
\simeq
\frac{c}{8\pi\omega}
\operatorname{Im}
\left(
\mathbf B^*\!\cdot\mathbf E
\right).
}
$$

For a circularly polarized plane wave this gives the familiar result that the spin-angular-momentum flux divided by the energy flux is

$$
\frac{\dot J_z^{\rm spin}}{\mathcal P}
=
\pm\frac{1}{\omega}.
$$

For linearly polarized radiation the spin contribution vanishes for a pure plane wave.

---

# 9. Intrinsic orbital angular-momentum flux — derivative-free implementation

Once the total intrinsic flux and spin flux are known,

$$
\boxed{
\dot J_z^{\rm orb,int}
=
\dot J_z^{\rm int}
-
\dot J_z^{\rm spin}.
}
$$

Therefore,

$$
\boxed{
\dot J_z^{\rm orb,int}
=
\dot J_z^{\rm tot}
-
\dot J_z^{\rm ext}
-
\dot J_z^{\rm spin}.
}
$$

This is the recommended formula for the present numerical calculation.

It requires only the complex fields

$$
\mathbf E(x,y,\omega),\qquad
\mathbf B(x,y,\omega),
$$

on the detector.

**No numerical differentiation of the coarse screen field is required.**

A useful residual surface map is

$$
\boxed{
\mathcal M_z^{\rm orb,res}(x,y)
=
\mathcal M_z^{\rm int}(x,y)
-
\mathcal M_z^{\rm spin}(x,y).
}
$$

Its integral gives

$$
\boxed{
\int dA\,\mathcal M_z^{\rm orb,res}
=
\dot J_z^{\rm orb,int}.
}
$$

---

## 10. Interpretation of the local residual map

The integrated identity

$$
\dot J_z^{\rm orb,int}
=
\dot J_z^{\rm int}
-
\dot J_z^{\rm spin}
$$

is the quantity of primary physical interest.

At the **local** level, however, one should be slightly careful in terminology.

The canonical orbital-AM flux density is not uniquely identical point by point to the residual

$$
\mathcal M_z^{\rm orb,res}.
$$

Different spin/orbital decompositions can redistribute angular momentum locally by divergence terms while leaving the integrated flux unchanged.

Therefore, for plots it is safest to call

$$
\boxed{
\mathcal M_z^{\rm orb,res}
}
$$

the **intrinsic orbital residual flux density**.

Its integrated value is the intrinsic orbital angular-momentum flux.

---

# 11. Independent canonical-OAM benchmark

If a finer detector grid is available for selected test cases, the orbital result can be checked independently using the canonical derivative expression.

For the \(z\)-component,

$$
x\partial_y-y\partial_x=\partial_\phi.
$$

The monochromatic canonical orbital density/flux expression therefore contains

$$
\boxed{
\operatorname{Im}
\sum_k
E_k^*
\partial_\phi E_k.
}
$$

For an azimuthal mode

$$
E_k\propto e^{im\phi},
$$

one has

$$
-i\partial_\phi E_k=mE_k.
$$

Thus the azimuthal Fourier index \(m\) directly measures the OAM mode content.

This derivative-based calculation is best regarded as an **independent benchmark**, not the primary production method.

---

# 12. Recommended numerical workflow

For every frequency \(\omega\):

1. Read the complex Fourier-domain fields
   $$
   \mathbf E(x,y,\omega),\qquad
   \mathbf B(x,y,\omega).
   $$

2. Compute the cycle-averaged Maxwell stress tensor
   $$
   T_{ij}(x,y,\omega).
   $$

3. Compute
   $$
   \dot P_x=\int T_{xz}\,dA,
   \qquad
   \dot P_y=\int T_{yz}\,dA.
   $$

4. Compute the radiation centroid
   $$
   X_c,\;Y_c
   $$
   from \(S_z\).

5. Compute the total AM flux
   $$
   \dot J_z^{\rm tot}
   =
   \int
   \left(xT_{yz}-yT_{xz}\right)dA.
   $$

6. Compute the extrinsic part
   $$
   \dot J_z^{\rm ext}
   =
   X_c\dot P_y-Y_c\dot P_x.
   $$

7. Compute the intrinsic total
   $$
   \dot J_z^{\rm int}
   =
   \dot J_z^{\rm tot}
   -
   \dot J_z^{\rm ext}.
   $$

8. Compute the spin flux density
   $$
   \mathcal M_z^{\rm spin}
   =
   \frac{c}{8\pi\omega}
   \operatorname{Im}
   \left[
   \mathbf B^*\!\cdot\mathbf E
   -
   2B_z^*E_z
   \right]
   $$
   and integrate it:
   $$
   \dot J_z^{\rm spin}
   =
   \int \mathcal M_z^{\rm spin}dA.
   $$

9. Obtain the intrinsic orbital part by subtraction:
   $$
   \boxed{
   \dot J_z^{\rm orb,int}
   =
   \dot J_z^{\rm tot}
   -
   \dot J_z^{\rm ext}
   -
   \dot J_z^{\rm spin}.
   }
   $$

10. Store 2D maps of
    $$
    \mathcal M_z^{\rm tot},
    \qquad
    \mathcal M_z^{\rm int},
    \qquad
    \mathcal M_z^{\rm spin},
    \qquad
    \mathcal M_z^{\rm orb,res}.
    $$

---

# 13. Particularly useful diagnostics for LG scattering

### A. Extrinsic-AM test

Check

$$
\frac{|\dot P_\perp|}{|\dot P_z|}
=
\frac{
\sqrt{\dot P_x^2+\dot P_y^2}
}{
|\dot P_z|
}.
$$

If this is numerically negligible, then

$$
\dot J_z^{\rm ext}\simeq0.
$$

---

### B. Spin test

For an incident linearly polarized LG beam, check whether

$$
\dot J_z^{\rm spin}\simeq0.
$$

If both

$$
\dot P_\perp\simeq0
$$

and

$$
\dot J_z^{\rm spin}\simeq0,
$$

then

$$
\boxed{
\dot J_z^{\rm tot}
\simeq
\dot J_z^{\rm orb,int}.
}
$$

This would provide a particularly clean signature of orbital AM in the scattered radiation.

---

### C. Topological-charge reversal

Repeat the calculation for

$$
\ell,\qquad -\ell,\qquad 0.
$$

Define

$$
\boxed{
J_z^{\rm odd}(\ell)
=
\frac{
J_z(+\ell)-J_z(-\ell)
}{2}
}
$$

and

$$
\boxed{
J_z^{\rm even}(\ell)
=
\frac{
J_z(+\ell)+J_z(-\ell)
}{2}.
}
$$

A contribution tied to LG handedness should primarily occur in the odd part.

---

### D. Screen-distance convergence

Repeat the calculation for increasing detector distance \($Z$).

The integrated radiated angular-momentum flux should approach a distance-independent far-field value.

This is an important check because the local non-radial components of the Poynting vector become progressively smaller even though the integrated angular-momentum flux can remain finite.

---

# 14. Frequency-resolved versus pulse-integrated quantities

The formulas above apply to one Fourier component \(\omega\).

Thus one naturally obtains

$$
\frac{dJ_z}{d\omega},
\qquad
\frac{dJ_z^{\rm spin}}{d\omega},
\qquad
\frac{dJ_z^{\rm orb}}{d\omega},
$$

up to the normalization implied by the Fourier-transform convention.

For a pulse, the total transported angular momentum is obtained by integrating the spectral quantity,

$$
\boxed{
J_z
=
\int_0^\infty
d\omega\,
\frac{dJ_z}{d\omega}.
}
$$

The exact factor multiplying the spectral quadratic forms must be matched to the Fourier convention used in the code.

---

# 15. Core formulas to implement

The minimum implementation set is therefore

$$
\boxed{
T_{ij}
=
\frac{1}{8\pi}
\Re\left[
E_iE_j^*
+
B_iB_j^*
-
\frac12\delta_{ij}
\left(|E|^2+|B|^2\right)
\right]
}
$$

$$
\boxed{
\mathcal M_z^{\rm tot}
=
xT_{yz}-yT_{xz}
}
$$

$$
\boxed{
X_c=
\frac{\int xS_z\,dA}{\int S_z\,dA},
\qquad
Y_c=
\frac{\int yS_z\,dA}{\int S_z\,dA}
}
$$

$$
\boxed{
\dot J_z^{\rm ext}
=
X_c\int T_{yz}dA
-
Y_c\int T_{xz}dA
}
$$

$$
\boxed{
\mathcal M_z^{\rm int}
=
(x-X_c)T_{yz}
-
(y-Y_c)T_{xz}
}
$$

$$
\boxed{
\mathcal M_z^{\rm spin}
=
\frac{c}{8\pi\omega}
\operatorname{Im}
\left[
\mathbf B^*\!\cdot\mathbf E
-
2B_z^*E_z
\right]
}
$$

and finally

$$
\boxed{
\dot J_z^{\rm orb,int}
=
\int dA\,
\left[
\mathcal M_z^{\rm int}
-
\mathcal M_z^{\rm spin}
\right].
}
$$

This last equation is the main derivative-free formula for the intrinsic orbital angular-momentum flux.

---

## References

- S. M. Barnett, **“Optical angular-momentum flux”**, *Journal of Optics B: Quantum and Semiclassical Optics* **4**, S7–S16 (2002), DOI: 10.1088/1464-4266/4/2/361.
- K. Y. Bliokh *et al.*, **“Conservation of the spin and orbital angular momenta in electromagnetism”**, *New Journal of Physics* **16**, 093037 (2014), DOI: 10.1088/1367-2630/16/9/093037.

