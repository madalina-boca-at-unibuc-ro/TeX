# Numerically Stable Form of the Second-Order Expression

Consider the expression

$$\mathcal A= \frac{1}{E_{\gamma_1}-E_\gamma} \left[
\frac{e^{-\frac{i}{\hbar}E_\gamma(t_f-t_i)}
      -e^{-\frac{i}{\hbar}E_{\gamma_f}(t_f-t_i)}}
     {E_{\gamma_f}-E_\gamma}
-
\frac{e^{-\frac{i}{\hbar}E_{\gamma_1}(t_f-t_i)}
      -e^{-\frac{i}{\hbar}E_{\gamma_f}(t_f-t_i)}}
     {E_{\gamma_f}-E_{\gamma_1}}
\right]. $$

Although this expression is finite when two or more of the energies
coincide, its direct numerical evaluation is problematic because the
individual terms contain removable singularities and differences of
nearly equal quantities.

We derive forms based on the sinc function that are better suited for
numerical evaluation.

## 1. Notation

Define

$$\Delta t=t_f-t_i, \qquad
\tau=\frac{\Delta t}{\hbar}, $$

and abbreviate

$$ E_0=E_\gamma, \qquad E_1=E_{\gamma_1},
\qquad E_f=E_{\gamma_f}. $$

Then

$$\mathcal A= \frac{1}{E_1-E_0} \left[
\frac{e^{-i\tau E_0}-e^{-i\tau E_f}}{E_f-E_0}
-
\frac{e^{-i\tau E_1}-e^{-i\tau E_f}}{E_f-E_1}
\right]. $$

Throughout, the mathematical sinc function is defined by

$$\operatorname{sinc}z=\frac{\sin z}{z}, \qquad
\operatorname{sinc}(0)=1. $$

## 2. First sinc transformation

Use the identity

$$ e^{-ix}-e^{-iy} = -i(x-y)e\^{-i(x+y)/2}
\operatorname{sinc}\left(\frac{x-y}{2}\right).
$$

It follows that

$$\frac{e^{-i\tau E_a}-e^{-i\tau E_b}}{E_b-E_a} = i\tau
e\^{-i\tau(E_a+E_b)/2} \operatorname{sinc} \left[
\frac{\tau(E_a-E_b)}{2}
\right]. $$

Therefore,

$$\boxed{
\mathcal A
=
\frac{i\tau}{E_1-E_0}
\left[
e^{-i\tau(E_0+E_f)/2}
\operatorname{sinc}
\left(\frac{\tau(E_0-E_f)}{2}\right)
-
e^{-i\tau(E_1+E_f)/2}
\operatorname{sinc}
\left(\frac{\tau(E_1-E_f)}{2}\right)
\right].
} $$

This form explicitly removes the apparent singularities at

$$ E_f\to E_0 \qquad\text{and}\qquad
E_f\to E_1. $$

However, it still contains an apparent difference quotient when
$E_1\to E_0$.

## 3. Limit $E_1\to E_0$

Define

$$ g(E)= e^{-i\tau(E+E_f)/2} \operatorname{sinc}
\left[
\frac{\tau(E-E_f)}{2}
\right]. $$

Then

$$\mathcal A = i\tau
\frac{g(E_0)-g(E_1)}{E_1-E_0}. $$

When $E_1\to E_0=E$,

$$\frac{g(E_0)-g(E_1)}{E_1-E_0} \longrightarrow -g'(E).
$$

Let

$$ z=\frac{\tau(E-E_f)}{2}. $$

Differentiating $g(E)$ gives

$$ g'(E) = \frac{\tau}{2} e^{-i\tau(E+E_f)/2} \left[
\operatorname{sinc}'z
-i\operatorname{sinc}z
\right]. $$

Hence

$$\boxed{
\mathcal A(E,E,E_f)
=
-\frac{\tau^2}{2}
e^{-i\tau(E+E_f)/2}
\left[
\operatorname{sinc}z
+i\,\operatorname{sinc}'z
\right].
} $$

Here

$$\operatorname{sinc}'z = \frac{z\cos z-\sin z}{z^2},
$$

with the regular limit

$$\operatorname{sinc}'(0)=0. $$

## 4. Completely degenerate case

If

$$ E_0=E_1=E_f=E, $$

then $z=0$, and therefore

$$\operatorname{sinc}(0)=1, \qquad
\operatorname{sinc}'(0)=0. $$

The expression reduces to

$$\boxed{
\mathcal A
=
-\frac{\tau^2}{2}e^{-i\tau E}
=
-\frac{(t_f-t_i)^2}{2\hbar^2}
e^{-iE(t_f-t_i)/\hbar}.
} $$

Thus the apparent singularity when all three energies coincide is
completely removable.

## 5. Fully regular integral representation

A representation with no energy denominators at all is

$$\boxed{
\begin{aligned}
\mathcal A
={}&
-\tau^2
\int_0^1 ds\,
(1-s)\,
\exp\left\{
-i\tau
\left[
\frac{1-s}{2}(E_0+E_1)+sE_f
\right]
\right\}
\\
&\qquad\times
\operatorname{sinc}
\left[
\frac{\tau(1-s)(E_1-E_0)}{2}
\right].
\end{aligned}
} $$

This identity is exact. It contains no divisions by

$$ E_1-E_0,\qquad E_f-E_0,\qquad E_f-E_1, $$

and all factors remain regular when any or all of these differences
vanish.

In particular,

$$\operatorname{sinc}(0)=1, $$

so no special treatment of a zero argument is required provided that the
numerical implementation of sinc itself is stable.

## 6. Recommended forms for numerical evaluation

For most parameter values, the compact sinc form

$$\boxed{
\mathcal A
=
\frac{i\tau}{E_1-E_0}
\left[
e^{-i\tau(E_0+E_f)/2}
\operatorname{sinc}
\left(\frac{\tau(E_0-E_f)}{2}\right)
-
e^{-i\tau(E_1+E_f)/2}
\operatorname{sinc}
\left(\frac{\tau(E_1-E_f)}{2}\right)
\right]
} $$

already avoids the two internal (0/0) structures of the original
expression.

When $E_1$ and $E_0$ are also very close, one can either use the
analytic limiting expression

$$\mathcal A(E,E,E_f) = -\frac{\tau^2}{2}
e\^{-i\tau(E+E_f)/2} \left[
\operatorname{sinc}z
+i\,\operatorname{sinc}'z
\right], $$

or use the fully regular integral representation, which contains no
small energy denominators.

## 7. NumPy convention

Care is needed because NumPy defines

$$\texttt{np.sinc}(x) = \frac{\sin(\pi x)}{\pi x}, $$

whereas the sinc used above is

$$\operatorname{sinc}(z)=\frac{\sin z}{z}. $$

Therefore, in NumPy,

 python
sinc_z = np.sinc(z / np.pi)


implements the mathematical sinc function used in these formulas.
