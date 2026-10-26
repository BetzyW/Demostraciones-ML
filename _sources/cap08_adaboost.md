---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# 8. AdaBoost: origen del algoritmo y error de entrenamiento exponencialmente decreciente

````{admonition} Enunciado
:class: note

Sean $(y_n,\bx_n)$, $n=1,\dots,N$, con $y_n\in\{-1,1\}$, clasificadores base $\phi(\cdot;\theta)\in
\{-1,1\}$, $F_i=\sum_{k\le i}a_k\phi_k$ con $\phi_k=\phi(\cdot;\theta_k)$ y $f=\sgn(F_K)$ (con $\sgn(0)=+1$). Con
$P_i=\sum_n\omega_n^{(i)}\Ind_{[y_n\phi_i(\bx_n)<0]}$ (error ponderado), demostrar:

**(A) Origen del Algoritmo 5.1.** Los pasos $\omega_n^{(i)}$, $\theta_i$, $a_i=\frac12\ln\frac{1-P_i}{P_i}$ y $Z_i$ resultan de
minimizar de forma voraz la pérdida exponencial $\sum_ne^{-y_nF_i(\bx_n)}$.

**(B) Convergencia exponencial.** El error de entrenamiento $L_S(f)=\frac1N\sum_n\Ind_{[f(\bx_n)\neq y_n]}$ cumple

$$
L_S(f)\ \le\ \prod_{i=1}^KZ_i=\prod_{i=1}^K2\sqrt{P_i(1-P_i)}=\prod_{i=1}^K\sqrt{1-4\gamma_i^2}\ \le\ \exp\Big(-2\sum_{i=1}^K\gamma_i^2\Big),\qquad\gamma_i=\tfrac12-P_i.
$$ (eq-ada-cota)

En particular, si $P_i\le\frac12-\gamma$ para todo $i$, $L_S(f)\le e^{-2\gamma^2K}$.
````

```{admonition} Nota de interpretación
:class: important

La casilla muestra el Algoritmo 5.1 y solo dice «Demostrar». Las
notas dicen que la deducción de $a_i$ y de los pesos «se demuestra fácilmente» (parte A) y,
en los ejercicios de la Sec. 5.6, piden probar que el error de entrenamiento tiende a cero
exponencialmente rápido (parte B). Se incluyen ambas.
```

```{admonition} Idea
:class: tip

(A) Con la pérdida exponencial, el problema voraz se factoriza en un peso por muestra
y un único parámetro $a$. (B) La pérdida 0-1 queda acotada por la exponencial, cuya suma
es exactamente el producto de las constantes de normalización $Z_i$.
```

## Parte A: derivación del algoritmo

````{prf:lemma} Pesos
:label: lem-pesos

Para todo $i\ge1$: $\omega_n^{(i)}=e^{-y_nF_{i-1}(\bx_n)}/W_{i-1}$, con $W_{i-1}=\sum_me^{-y_mF_{i-1}(\bx_m)}$ y
$F_0\equiv0$.
````

````{admonition} Demostración
:class: demo
Inducción. Para $i=1$, $\omega_n^{(1)}=1/N=e^0/N$. Si vale para $i$, el algoritmo hace
$\omega_n^{(i+1)}=\omega_n^{(i)}e^{-y_na_i\phi_i(\bx_n)}/Z_i$ (líneas 5–8), luego
$\omega_n^{(i+1)}\propto e^{-y_nF_{i-1}(\bx_n)}e^{-y_na_i\phi_i(\bx_n)}=e^{-y_nF_i(\bx_n)}$ y,
como los pesos suman 1, $\omega_n^{(i+1)}=e^{-y_nF_i(\bx_n)}/W_i$.
````

````{admonition} Demostración: Elección de $\theta_i$ (Ecs. 5.5–5.6)
:class: demo
Con $F_i=F_{i-1}+a\phi$ y el {prf:ref}`lem-pesos`,

$$
J(a,\theta)=\sum_ne^{-y_n(F_{i-1}(\bx_n)+a\phi(\bx_n;\theta))}=W_{i-1}\sum_n\omega_n^{(i)}e^{-ay_n\phi(\bx_n;\theta)}.
$$

Como $y_n\phi\in\{-1,1\}$, $e^{-ay_n\phi}=e^{-a}+(e^a-e^{-a})\Ind_{[y_n\phi<0]}$. Con $\sum_n\omega_n^{(i)}=1$:

$$
J(a,\theta)=W_{i-1}\big[e^{-a}+(e^a-e^{-a})P(\theta)\big],\qquad P(\theta)=\sum_n\omega_n^{(i)}\Ind_{[y_n\phi(\bx_n;\theta)<0]}.
$$

Para $a>0$, $e^a-e^{-a}>0$; así, minimizar en $\theta$ equivale a minimizar el error ponderado $P(\theta)$, sin
importar $a$: $\theta_i=\arg\min_\theta P(\theta)$ y $P_i=P(\theta_i)$.
````

````{admonition} Demostración: Elección de $a_i$
:class: demo
Con $\theta_i$ fijo, $J/W_{i-1}=g(a):=(1-P_i)e^{-a}+P_ie^a$. Entonces $g'(a)=-(1-P_i)e^{-a}+
P_ie^a$ y $g''(a)>0$, luego $g$ es estrictamente convexa y su mínimo cumple $e^{2a}=\frac{1-P_i}{P_i}$, es decir

$$
a_i=\tfrac12\ln\frac{1-P_i}{P_i},\qquad a_i>0\iff P_i<\tfrac12.
$$

(Si $P_i>\frac12$ basta cambiar $\phi_i$ por $-\phi_i$, cuyo error es $1-P_i<\frac12$.)
````

````{prf:lemma} Valor de $Z_i$
:label: lem-Z

$Z_i=\sum_n\omega_n^{(i)}e^{-y_na_i\phi_i(\bx_n)}=g(a_i)=2\sqrt{P_i(1-P_i)}$.
````

````{admonition} Demostración
:class: demo
Los puntos bien clasificados pesan $1-P_i$ y se multiplican por $e^{-a_i}=\sqrt{P_i/(1-P_i)}$;
los mal clasificados pesan $P_i$ y se multiplican por $e^{a_i}=\sqrt{(1-P_i)/P_i}$. Luego
$Z_i=(1-P_i)\sqrt{\frac{P_i}{1-P_i}}+P_i\sqrt{\frac{1-P_i}{P_i}}=2\sqrt{P_i(1-P_i)}$.
````

Esto justifica todas las líneas del algoritmo: los pesos del {prf:ref}`lem-pesos`, el $\theta_i$ que minimiza $P_i$, $a_i$,
y $Z_i$ como constante que renormaliza los pesos. Además, con los nuevos pesos el error de $\phi_i$ es
$P_ie^{a_i}/Z_i=\frac12$, de modo que $\phi_i$ ya no aporta información en la siguiente ronda.

## Parte B: el error de entrenamiento

````{admonition} Demostración de (B)
:class: demo
**Paso 1 (suma de la pérdida exponencial).** Desenrollando la recursión de pesos desde $\omega_n^{(1)}=1/N$,

$$
\omega_n^{(K+1)}=\frac1N\frac{\prod_{i=1}^Ke^{-y_na_i\phi_i(\bx_n)}}{\prod_{i=1}^KZ_i}=\frac{e^{-y_nF_K(\bx_n)}}{N\prod_iZ_i}.
$$

Sumando en $n$ (los pesos suman 1): $\ \frac1N\sum_{n=1}^Ne^{-y_nF_K(\bx_n)}=\prod_{i=1}^KZ_i$.

**Paso 2 (0-1 $\le$ exponencial).** Si $f(\bx_n)\neq y_n$ entonces $y_nF_K(\bx_n)\le0$ (con $\sgn(0)=+1$), así que
$e^{-y_nF_K(\bx_n)}\ge1=\Ind_{[f(\bx_n)\neq y_n]}$; si $f(\bx_n)=y_n$, $0\le e^{-y_nF_K(\bx_n)}$. Promediando y usando el Paso 1,

$$
L_S(f)\le\frac1N\sum_ne^{-y_nF_K(\bx_n)}=\prod_{i=1}^KZ_i.
$$

**Paso 3.** Por el {prf:ref}`lem-Z` y $P_i=\frac12-\gamma_i$: $4P_i(1-P_i)=4(\frac12-\gamma_i)(\frac12+\gamma_i)=1-4\gamma_i^2$, luego
$Z_i=\sqrt{1-4\gamma_i^2}$.

**Paso 4.** Como $1-x\le e^{-x}$ para todo $x$ (la recta tangente a $e^{-x}$ en 0 queda por debajo de la
curva convexa), $\sqrt{1-4\gamma_i^2}\le\sqrt{e^{-4\gamma_i^2}}=e^{-2\gamma_i^2}$. Multiplicando sobre $i$: $\prod_iZ_i\le\exp(-2\sum_i\gamma_i^2)$.
````

```{admonition} Corolario: convergencia exponencial
:class: seealso

Si cada clasificador base es ligeramente mejor que
el azar, $P_i\le\frac12-\gamma$, entonces $L_S(f)\le e^{-2\gamma^2K}$. Como $L_S(f)\in\{0,\frac1N,\frac2N,\dots\}$, el error es
*exactamente* 0 en cuanto $e^{-2\gamma^2K}<\frac1N$, es decir $K>\dfrac{\ln N}{2\gamma^2}$. Ejemplo: $N=200$, $\gamma=0.1$ da
$K\ge265$. La cota es sobre el error de *entrenamiento*; no dice nada del error de prueba.
```

## Comprobación numérica

El siguiente código implementa el Algoritmo 5.1 con tocones de decisión sobre datos ruidosos
($N=200$) y verifica la cadena {eq}`eq-ada-cota` en cada iteración.

```{code-cell} ipython3
:tags: [hide-input]

import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(1)
N, K = 200, 40
X = rng.uniform(-1, 1, size=(N, 2))
y = np.where(X[:, 0] ** 2 + X[:, 1] ** 2 < 0.5, 1, -1)
y[rng.random(N) < 0.08] *= -1                         # ruido en las etiquetas

def mejor_tocon(X, y, w):
    mejor = (np.inf, None)
    for j in range(X.shape[1]):
        for t in np.unique(X[:, j]):
            for s in (1, -1):
                pred = np.where(X[:, j] < t, s, -s)
                P = w[pred != y].sum()
                if P < mejor[0]:
                    mejor = (P, (j, t, s))
    return mejor

def tocon(X, theta):
    j, t, s = theta
    return np.where(X[:, j] < t, s, -s)

w = np.full(N, 1 / N)
F = np.zeros(N)
P_hist, err, prodZ, cota = [], [], [], []
logZ = sum_g2 = 0.0
for i in range(K):
    P, theta = mejor_tocon(X, y, w)
    phi = tocon(X, theta)
    a = 0.5 * np.log((1 - P) / P)
    pesos = w * np.exp(-y * a * phi)
    Z = pesos.sum()
    w = pesos / Z
    F += a * phi
    f = np.where(F >= 0, 1, -1)
    logZ += np.log(Z); sum_g2 += (0.5 - P) ** 2
    assert np.isclose(Z, 2 * np.sqrt(P * (1 - P)))      # Lema Z_i
    P_hist.append(P); err.append(np.mean(f != y))
    prodZ.append(np.exp(logZ)); cota.append(np.exp(-2 * sum_g2))

err, prodZ, cota = map(np.array, (err, prodZ, cota))
assert np.all(err <= prodZ + 1e-12) and np.all(prodZ <= cota + 1e-12)

print(f"{'K':>3} {'P_K':>7} {'error entr.':>12} {'∏Z_i':>8} {'exp(-2Σγ²)':>11}")
for k in (1, 5, 10, 20, 30, 40):
    print(f"{k:>3} {P_hist[k-1]:7.3f} {err[k-1]:12.3f} {prodZ[k-1]:8.3f} {cota[k-1]:11.3f}")

ks = np.arange(1, K + 1)
fig, ax = plt.subplots(figsize=(7, 4.3))
ax.semilogy(ks, err, "-o", ms=3.5, color="#1f4e79", lw=2,
            label=r"error de entrenamiento de $f=\mathrm{sgn}(F_K)$")
ax.semilogy(ks, prodZ, color="#c0392b", lw=1.8, label=r"$\prod_{i\leq K} Z_i$")
ax.semilogy(ks, cota, "--", color="#2e8b57", lw=1.8, label=r"$\exp(-2\sum_{i\leq K}\gamma_i^2)$")
ax.set_xlabel("iteración $K$")
ax.set_title(rf"AdaBoost con tocones de decisión ($N={N}$)")
ax.grid(alpha=0.3, which="both"); ax.legend()
ax.spines[["top", "right"]].set_visible(False)
plt.tight_layout(); plt.show()
```

**Figura 5.** AdaBoost con tocones de decisión sobre datos ruidosos ($N=200$). La cota $\prod Z_i\le e^{-2\sum\gamma_i^2}$ se
cumple en cada iteración; aquí es holgada porque $\gamma_i$ es pequeño.
