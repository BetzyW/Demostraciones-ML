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

# 6. Lasso: obtención de los estimadores

````{admonition} Enunciado
:class: note

Con datos estandarizados como en el capítulo 5 ($\sum_i x_{ij}^2=n$, $\by$ centrado), hallar los
estimadores que minimizan

$$
S_\lambda(\bbeta)=(\by-X\bbeta)^\T(\by-X\bbeta)+\lambda\sum_{j=1}^p|\beta_j|,\qquad\lambda>0,
$$

“tal como se realizó en el caso de ridge”, y describir el algoritmo LARS sugerido en las notas.
````

```{admonition} Idea
:class: tip

$|\beta_j|$ no es derivable en 0, así que no se puede igualar el gradiente a cero como en ridge.
Se usa el *subdiferencial*: en 0 el «derivable» se reemplaza por el intervalo $[-1,1]$. Eso produce
condiciones (KKT) que generan coeficientes exactamente nulos.
```

## 6.1 Intercepto, existencia y unicidad

El término de penalización no incluye a $\beta_0^\star$, así que la derivada respecto a él es la del capítulo 4:
$\hat\beta_0^\star=\bar y=0$ (por el capítulo 5). Queda minimizar $S_\lambda(\bbeta)$ sin intercepto.

$S_\lambda=f+\lambda\lVert\bbeta\rVert_1$ con $f(\bbeta)=\lVert\by-X\bbeta\rVert^2$ convexa (su hessiano $2X^\T X\succeq0$) y $\lVert\cdot\rVert_1$ convexa
(desigualdad triangular y homogeneidad). Luego $S_\lambda$ es convexa, continua y *coerciva* ($S_\lambda\ge
\lambda\lVert\bbeta\rVert_1\to\infty$), por lo que tiene un mínimo global. Si $\rg X=p$, $f$ es estrictamente convexa y
el mínimo es único.

## 6.2 Condiciones de optimalidad (KKT)

```{prf:theorem} KKT del lasso
:label: thm-kkt

$\hat\bbeta$ es mínimo si y solo si, con $c_j:=\bx_j^\T(\by-X\hat\bbeta)$,

$$
\begin{cases}2c_j=\lambda\,\sgn(\hat\beta_j), & \hat\beta_j\neq0,\\ |2c_j|\le\lambda, & \hat\beta_j=0.\end{cases}
$$
```

```{admonition} Demostración
:class: demo
Para una función convexa, $\hat\bbeta$ es mínimo si y solo si $\cero\in\partial S_\lambda(\hat\bbeta)$. La parte suave
aporta $-2X^\T(\by-X\bbeta)$ y $\partial|\beta_j|=\{\sgn\beta_j\}$ si $\beta_j\neq0$, $=[-1,1]$ si $\beta_j=0$. Así $\cero\in\partial S_\lambda$ equivale
a $2c_j=\lambda v_j$ con $v_j=\sgn\hat\beta_j$ (si $\hat\beta_j\neq0$) o $v_j\in[-1,1]$ (si $\hat\beta_j=0$), que son las condiciones
enunciadas.
```

*Por qué hay ceros exactos:* $\hat\beta_j=0$ ocurre en todo un intervalo, $|2c_j|\le\lambda$; en ridge, en cambio,
$2c_j=2\lambda\hat\beta_j$ exige $c_j=0$ exactamente.

**Valor crítico.** $\hat\bbeta=\cero$ es óptimo si y solo si $|2\bx_j^\T\by|\le\lambda$ para todo $j$, es decir

$$
\lambda\ \ge\ \lambda_{\max}:=2\lVert X^\T\by\rVert_\infty.
$$

Como $\lVert\bx_j\rVert^2=n$, $\bx_j^\T\by$ es proporcional a la correlación de $\bx_j$ con $\by$: la primera variable en entrar
es la más correlacionada con $\by$.

## 6.3 Fórmula con conjunto activo conocido

Sea $A=\{j:\hat\beta_j\neq0\}$ y $\bs_A=\sgn(\hat\bbeta_A)$. Las condiciones de $j\in A$ dicen $X_A^\T(\by-X_A\hat\bbeta_A)=\frac\lambda2\bs_A$;
luego, si $X_A^\T X_A$ es invertible,

$$
\hat\bbeta_A=(X_A^\T X_A)^{-1}\Big(X_A^\T\by-\tfrac\lambda2\bs_A\Big),\qquad \hat\bbeta_{A^c}=\cero,
$$ (eq-activo)

válida mientras $\sgn(\hat\bbeta_A)=\bs_A$ y $|\bx_k^\T(\by-X_A\hat\bbeta_A)|\le\lambda/2$ para $k\notin A$. Es el análogo de $(X^\T X+
\lambda I)^{-1}X^\T\by$ de ridge. En cada tramo, $\dfrac{d\hat\bbeta_A}{d\lambda}=-\frac12(X_A^\T X_A)^{-1}\bs_A$ es constante: **la trayectoria
$\lambda\mapsto\hat\bbeta(\lambda)$ es lineal por tramos**.

## 6.4 Caso ortogonal $X^\T X=nI_p$: umbral suave

Sea $b_j=\bx_j^\T\by/n$ (el estimador MCO). Como $\by^\T X\bbeta=n\sum_j b_j\beta_j$,

$$
S_\lambda(\bbeta)=\by^\T\by-2n\sum_j b_j\beta_j+n\sum_j\beta_j^2+\lambda\sum_j|\beta_j|=\text{cte}+\sum_{j=1}^p\big[n(\beta_j-b_j)^2+\lambda|\beta_j|\big],
$$

que es separable. Para cada $j$ (KKT): si $\hat\beta_j>0$, $2n(\hat\beta_j-b_j)+\lambda=0\Rightarrow\hat\beta_j=b_j-\frac\lambda{2n}$, consistente
solo si $b_j>\frac\lambda{2n}$; si $\hat\beta_j<0$, $\hat\beta_j=b_j+\frac\lambda{2n}$, consistente si $b_j<-\frac\lambda{2n}$; y $\hat\beta_j=0$ si $|b_j|\le\frac\lambda{2n}$. Resumiendo:

$$
\hat\beta_j^{\text{lasso}}=\sgn(b_j)\Big(|b_j|-\frac\lambda{2n}\Big)_+,\qquad \hat\beta_j^{\text{ridge}}=\frac n{n+\lambda}\,b_j.
$$ (eq-umbral-suave)

(Para ridge: $(nI+\lambda I)^{-1}X^\T\by=\frac n{n+\lambda}\bb$.) Lasso *resta* una constante y trunca en 0; ridge *multiplica*
por un factor menor que 1.

```{code-cell} ipython3
:tags: [hide-input]

import numpy as np
import matplotlib.pyplot as plt

n, lam = 10, 10
b = np.linspace(-3, 3, 601)
lasso = np.sign(b) * np.maximum(np.abs(b) - lam / (2 * n), 0)
ridge = n / (n + lam) * b

fig, ax = plt.subplots(figsize=(6.5, 4.5))
ax.axvspan(-lam / (2 * n), lam / (2 * n), color="#c0392b", alpha=0.12)
ax.plot(b, b, ":", color="0.4", lw=1.8, label=r"MCO: $\hat\beta_j=b_j$")
ax.plot(b, ridge, color="#1f4e79", lw=2, label=r"Ridge: $\frac{n}{n+\lambda}b_j$")
ax.plot(b, lasso, color="#c0392b", lw=2.2,
        label=r"Lasso: $\mathrm{sgn}(b_j)(|b_j|-\frac{\lambda}{2n})_+$")
ax.text(0, -2.2, "zona donde lasso\nda exactamente 0", color="#c0392b", ha="center")
ax.set_xlim(-3, 3); ax.set_ylim(-3, 3)
ax.set_xlabel(r"$b_j=x_j^T y/n$ (estimador MCO)")
ax.set_ylabel(r"estimador $\hat\beta_j$")
ax.set_title(rf"Caso ortogonal $X^TX=nI_p$ ($n={n},\ \lambda={lam}$)")
ax.grid(alpha=0.3); ax.legend(loc="upper left")
ax.spines[["top", "right"]].set_visible(False)
plt.tight_layout(); plt.show()
```

**Figura 2.** MCO, ridge y lasso en el caso ortogonal.

## 6.5 Caso general: descenso por coordenadas

Fijando los demás coeficientes y con $\br^{(j)}=\by-\sum_{k\neq j}\bx_k\beta_k$,

$$
S_\lambda=n\beta_j^2-2\beta_j\bx_j^\T\br^{(j)}+\lambda|\beta_j|+\text{cte}=n\Big(\beta_j-\frac{\bx_j^\T\br^{(j)}}n\Big)^2+\lambda|\beta_j|+\text{cte},
$$

así que la actualización óptima es el mismo umbral suave: $\beta_j\leftarrow\sgn(z_j)(|z_j|-\frac\lambda{2n})_+$ con $z_j=
\bx_j^\T\br^{(j)}/n$. Iterar sobre $j$ converge al mínimo global porque la parte no suave es separable (Tseng,
2001). Esto es lo que usa `Lasso` de scikit-learn, cuyo `alpha` cumple $\alpha=\lambda/(2n)$.

## 6.6 Algoritmo LARS (Efron et al., 2004)

Se trabaja con columnas de norma 1 ($\tilde\bx_j=\bx_j/\sqrt n$, $\tilde\beta_j=\sqrt n\beta_j$, $\tilde\lambda=\lambda/\sqrt n$; el orden de entrada
no cambia). Sea $\br=\by-\hat\bmu$, $c_j=\bx_j^\T\br$, $C=\max_j|c_j|$.

1. **Inicio:** $\hat\bmu=\cero$, $\hat\bbeta=\cero$, $A=\{\arg\max_j|c_j|\}$. Aquí $\lambda=2C=\lambda_{\max}$.
2. **Dirección equiangular.** Con $s_j=\sgn(c_j)$, $X_A=(s_j\bx_j)_{j\in A}$, $G_A=X_A^\T X_A$, $A_A=
   (\uno^\T G_A^{-1}\uno)^{-1/2}$, $\bw_A=A_AG_A^{-1}\uno$ y $\bu_A=X_A\bw_A$: $\lVert\bu_A\rVert=1$ y $X_A^\T\bu_A=A_A\uno$, o sea, $\bu_A$
   forma el *mismo ángulo* con todos los predictores activos.
3. **Avance** $\hat\bmu\leftarrow\hat\bmu+\gamma\bu_A$. Las correlaciones activas bajan juntas, $C(\gamma)=C-\gamma A_A$; las inactivas
   son $c_k(\gamma)=c_k-\gamma a_k$ con $a_k=\bx_k^\T\bu_A$. Entra la variable $k$ en
   $\gamma_{\text{ent}}=\min^+_{k\notin A}\Big\{\frac{C-c_k}{A_A-a_k},\frac{C+c_k}{A_A+a_k}\Big\}$.
4. **Modificación lasso.** Los coeficientes activos cambian como $\beta_j(\gamma)=\beta_j+\gamma s_jw_j$. Si alguno
   cruza 0 antes de $\gamma_{\text{ent}}$ (en $\gamma_{\text{cero}}=\min^+_j\{-\beta_j/(s_jw_j)\}$), se avanza hasta ahí y esa variable
   *sale* de $A$.
5. Repetir hasta llegar a $\lambda=0$.

Con la modificación, los nodos de LARS son exactamente los de la trayectoria lasso, con $\lambda=2C$
(pues $|c_j|=\lambda/2$ para $j\in A$, por KKT), y en cada tramo vale {eq}`eq-activo`.

```{code-cell} ipython3
:tags: [hide-input]

from sklearn.linear_model import lars_path

rng = np.random.default_rng(0)
n, p = 60, 6
X = rng.normal(size=(n, p))
X[:, 3] += 0.6 * X[:, 0]                       # algo de correlación
beta_true = np.array([3.0, -2.0, 0.0, 0.3, 0.8, 0.3])
y = X @ beta_true + rng.normal(scale=1.5, size=n)

# estandarización del capítulo 5: columnas centradas con suma de cuadrados n, y centrado
X = (X - X.mean(0)) / X.std(0)
y = y - y.mean()

alphas, active, coefs = lars_path(X, y, method="lasso")
lams = 2 * n * alphas                          # λ = 2nα

colores = ["#1f4e79", "#c0392b", "0.5", "#d68910", "#2e8b57", "#8e44ad"]
fig, ax = plt.subplots(figsize=(6.5, 4.3))
for j in range(p):
    ax.plot(lams, coefs[j], "-o", ms=3.5, color=colores[j], label=rf"$\hat\beta_{j+1}$")
for l in lams:
    ax.axvline(l, color="0.85", lw=0.8, zorder=0)
ax.invert_xaxis()
ax.set_xlabel(r"$\lambda$"); ax.set_ylabel("coeficiente")
ax.set_title(r"Camino lasso (LARS): lineal por tramos en $\lambda$")
ax.legend(ncol=3, loc="upper left", fontsize=9)
ax.spines[["top", "right"]].set_visible(False)
plt.tight_layout(); plt.show()

print(f"λ_max = 2‖Xᵀy‖∞ = {2*np.abs(X.T @ y).max():.4f};  primer nodo de LARS: {lams[0]:.4f}")
```

**Figura 3.** Camino lasso calculado con LARS ($n=60$, $p=6$): lineal por tramos; los coeficientes entran
uno a uno y algunos permanecen en 0.

```{admonition} Resumen: parámetros de estimación lasso
:class: seealso

$\hat\beta_0^\star=\bar y$; $\hat\bbeta$ es el único punto que cumple KKT;
con conjunto activo $A$ viene dado por {eq}`eq-activo`; si $X^\T X=nI$, por {eq}`eq-umbral-suave`; en general se calcula
por descenso por coordenadas o con LARS.
```
