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

# 2. k-NN: la desigualdad con $\min\{\eta,1-\eta\}$ (caso $1-\eta(\bx)$)

````{admonition} Enunciado
:class: note

Para todo $\eta\in[0,1]$,

$$
2\eta(1-\eta)\ \le\ 2\min\{\eta,1-\eta\}.
$$ (eq-desig-eta)

Las notas prueban el caso $\min\{\eta,1-\eta\}=\eta(\bx)$ y dejan pendiente el caso $\min\{\eta,1-\eta\}=1-\eta(\bx)$.
Se pide demostrar el caso pendiente.
````

```{admonition} Para qué sirve
:class: important

En la demostración del Lema 2.1 se acota $\E_{\bx}[2\eta(1-\eta)]\le 2\,\E_{\bx}[\min\{\eta,1-\eta\}]=2L_\Dist(h^\star)$;
la última igualdad es el inciso (d) del capítulo 1.
```

```{admonition} Idea
:class: tip

El producto $\eta(1-\eta)$ es el producto del número menor por el mayor, y el mayor nunca supera 1.
```

## Demostraciones

````{admonition} Demostración: Caso $\min\{\eta,1-\eta\}=1-\eta$
:class: demo
Supongamos $\min\{\eta,1-\eta\}=1-\eta$, es decir, $1-\eta\le\eta$ (equivalentemente $\eta\ge1/2$). Como $\eta\le1$ y $1-\eta\ge0$,
al multiplicar la desigualdad $\eta\le1$ por $1-\eta\ge0$ se obtiene

$$
\eta(1-\eta)\ \le\ 1\cdot(1-\eta)\quad\Longrightarrow\quad 2\eta(1-\eta)\ \le\ 2(1-\eta)=2\min\{\eta,1-\eta\}.
$$
````

````{admonition} Demostración: Caso $\min\{\eta,1-\eta\}=\eta$ (el de las notas, corregido)
:class: demo
Si $\eta\le1-\eta$, como $1-\eta\le1$ y $\eta\ge0$, al multiplicar $1-\eta\le1$ por $\eta\ge0$:

$$
\eta(1-\eta)\le\eta\cdot1\quad\Longrightarrow\quad 2\eta(1-\eta)\le 2\eta=2\min\{\eta,1-\eta\}.
$$
````

````{admonition} Prueba unificada (sin separar casos)
:class: demo
Para cualquier $\eta\in[0,1]$, los números $\eta$ y $1-\eta$ son
$\min\{\eta,1-\eta\}$ y $\max\{\eta,1-\eta\}$ en algún orden, de modo que

$$
\eta(1-\eta)=\min\{\eta,1-\eta\}\cdot\max\{\eta,1-\eta\}\ \le\ \min\{\eta,1-\eta\}\cdot1,
$$

porque $\max\{\eta,1-\eta\}\le1$ y $\min\{\eta,1-\eta\}\ge0$. Multiplicando por 2 se obtiene {eq}`eq-desig-eta`. La igualdad
ocurre si y solo si $\min=0$ o $\max=1$, es decir, $\eta\in\{0,1\}$.
````

````{admonition} Error en la cadena intermedia de las notas
:class: danger

Para el caso $\min=\eta$, las notas escriben

$$
2\eta(1-\eta)\ \le\ 2(1-\eta)^2\ \le\ 2\min\{\eta,1-\eta\}.
$$

La primera desigualdad es válida (usa $\eta\le1-\eta$), pero la segunda no lo es en general: como
aquí $\min=\eta$, equivale a $(1-\eta)^2\le\eta$, es decir, $\eta^2-3\eta+1\le0$, que solo se cumple para
$\eta\ge\frac{3-\sqrt5}{2}\approx0.382$. Por ejemplo, con $\eta=0.1$: $2(0.9)^2=1.62$ *no* es $\le 0.20=2\eta$.

La «cadena espejo» natural para el caso $1-\eta$, $\;2\eta(1-\eta)\le2\eta^2\le2(1-\eta)$, falla de la
misma manera (solo vale para $\eta\le\frac{\sqrt5-1}{2}\approx0.618$; con $\eta=0.9$ da $1.62\not\le0.20$). Por eso la
prueba anterior acota el factor *mayor* por 1 en lugar de reemplazarlo por el factor menor.
La desigualdad {eq}`eq-desig-eta` y, con ella, el Lema 2.1 **no** se ven afectados.
````

| $\eta$ | $2\eta(1-\eta)$ | $2(1-\eta)^2$ | $2\min\{\eta,1-\eta\}$ | ¿$2(1-\eta)^2\le 2\min$? |
|:---:|:---:|:---:|:---:|:---:|
| 0.1 | 0.18 | 1.62 | 0.20 | <span style="color:#c0392b">No</span> |
| 0.3 | 0.42 | 0.98 | 0.60 | <span style="color:#c0392b">No</span> |
| 0.4 | 0.48 | 0.72 | 0.80 | Sí |

```{code-cell} ipython3
:tags: [hide-input]

import numpy as np
import matplotlib.pyplot as plt

eta = np.linspace(0, 1, 100_001)
izq = 2 * eta * (1 - eta)
der = 2 * np.minimum(eta, 1 - eta)
mitad = eta <= 0.5
paso = 2 * (1 - eta[mitad]) ** 2

fig, ax = plt.subplots(figsize=(7, 4.2))
ax.fill_between(eta[mitad], der[mitad], paso, where=paso > der[mitad],
                color="0.85", alpha=0.6)
ax.plot(eta, izq, color="#1f4e79", lw=2.2, label=r"$2\eta(1-\eta)$")
ax.plot(eta, der, color="#c0392b", lw=2, label=r"$2\min\{\eta,1-\eta\}$")
ax.plot(eta[mitad], paso, "--", color="0.45", lw=1.8,
        label=r"$2(1-\eta)^2$ (paso intermedio de las notas, caso $\eta\leq 1/2$)")
ax.annotate(r"$\eta=0.1$:  $1.62>0.20$", xy=(0.1, 1.62), xytext=(0.3, 1.75),
            arrowprops=dict(arrowstyle="->", color="0.4"), color="0.4")
ax.set_xlim(0, 1); ax.set_ylim(0, 2.02)
ax.set_xlabel(r"$\eta(x)$")
ax.grid(alpha=0.3)
ax.spines[["top", "right"]].set_visible(False)
ax.legend(loc="upper center", bbox_to_anchor=(0.5, -0.15), frameon=False)
plt.tight_layout()
plt.show()

# comprobación numérica
print("max[2η(1-η) - 2min{η,1-η}] =", (izq - der).max())
print("la cadena de las notas falla para η <", round(eta[mitad][paso > der[mitad]].max(), 3))
```

**Figura 1.** La desigualdad {eq}`eq-desig-eta` se cumple siempre (curva azul $\le$ curva roja). El paso intermedio
$2(1-\eta)^2$ (gris) supera a $2\min\{\eta,1-\eta\}$ para $\eta<0.382$.
