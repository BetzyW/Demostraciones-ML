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

# 7. Árboles: dónde está el error del Ejercicio 6 (Sec. 12)

````{admonition} Enunciado
:class: note

Con los datos de la Tabla 5.1 de las notas (variable objetivo *Gonna Be an
Astronaut*) se construye un árbol minimizando
$\text{Gini}_{\text{split}}=\frac{N_1}{N}\text{Gini}(\text{Nodo}_1)+\frac{N_2}{N}\text{Gini}(\text{Nodo}_2)$,
con $\text{Gini}=1-\sum_i(n_i/N)^2$. Localizar el error de la solución propuesta y corregirlo.
````

| Age | 24 | 30 | 36 | 36 | 42 | 44 | 46 | 47 | 47 | 51 |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **Like Dogs** | 0 | 1 | 0 | 0 | 0 | 1 | 1 | 1 | 0 | 1 |
| **Like Gravity** | 0 | 1 | 1 | 0 | 0 | 1 | 0 | 1 | 1 | 1 |
| **Astronauta** | 0 | 1 | 1 | 0 | 0 | 1 | 0 | 1 | 1 | 0 |

```{admonition} Cómo se hizo la verificación
:class: important

Todo se recalculó con fracciones exactas (ver la celda de código al final del capítulo). Los umbrales de
`Age` son puntos medios entre valores *distintos* consecutivos; “izquierda” es $\text{Age}<a$. En las
tablas, $n$ (Sí/No) indica el tamaño del grupo y cuántos son astronautas. No pudieron abrirse las
imágenes `TREEDECISION1--6` de la página; de ellas solo se vieron los umbrales 27–39 del archivo.
El resto se contrastó con los números que da el *texto* de la solución.
```

## 7.1 Raíz ($n=10$, 5 Sí / 5 No, $\text{Gini}=0.5$)

| Partición | Izq. $n$ (Sí/No) | Der. $n$ (Sí/No) | $\text{Gini}_L$ | $\text{Gini}_R$ | $\text{Gini}_{\text{split}}$ | valor |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| *Variable Age (umbral $a$)* | | | | | | |
| 27 | 1 (0/1) | 9 (5/4) | 0.0000 | 0.4938 | $\frac49$ | 0.4444 |
| 33 | 2 (1/1) | 8 (4/4) | 0.5000 | 0.5000 | $\frac12$ | 0.5000 |
| 39 | 4 (2/2) | 6 (3/3) | 0.5000 | 0.5000 | $\frac12$ | 0.5000 |
| 43 | 5 (2/3) | 5 (3/2) | 0.4800 | 0.4800 | $\frac{12}{25}$ | 0.4800 |
| 45 | 6 (3/3) | 4 (2/2) | 0.5000 | 0.5000 | $\frac12$ | 0.5000 |
| 46.5 | 7 (3/4) | 3 (2/1) | 0.4898 | 0.4444 | $\frac{10}{21}$ | 0.4762 |
| 49 | 9 (5/4) | 1 (0/1) | 0.4938 | 0.0000 | $\frac49$ | 0.4444 |
| *Variables binarias (izq. = Sí (1), der. = No (0))* | | | | | | |
| Like Dogs | 5 (3/2) | 5 (2/3) | 0.4800 | 0.4800 | $\frac{12}{25}$ | 0.4800 |
| **Like Gravity** | **6 (5/1)** | **4 (0/4)** | **0.2778** | **0.0000** | $\mathbf{\frac16}$ | **0.1667** |

La mejor partición es `Like Gravity` ($1/6\approx0.17$), como dice la solución. **El primer árbol
es correcto**: con $a=27$, $\text{Gini}(D_1)=0$, $\text{Gini}(D_2)=1-(5/9)^2-(4/9)^2=40/81\approx0.49$ y
$\text{Gini}_{\text{split}}=\frac9{10}\cdot\frac{40}{81}=\frac49\approx0.44$; igual para 33 y 39 ($=0.5$). Solo hay una observación: la solución
lista también 36 y 47, que no son puntos medios entre valores distintos y dan las mismas particiones
que 33 y 46.5 (duplicados inofensivos). Además 27 y 49 empatan en $4/9$.

## 7.2 Nodo `Like Gravity` = Sí ($n=6$; 5 Sí / 1 No; $\text{Gini}=5/18$)

Datos: $(30,1),(36,0),(44,1),(47,1),(47,0)$ con etiqueta Sí y $(51,1)$ con etiqueta No (par = Age,
Like Dogs).

| Partición | Izq. $n$ (Sí/No) | Der. $n$ (Sí/No) | $\text{Gini}_L$ | $\text{Gini}_R$ | $\text{Gini}_{\text{split}}$ | valor |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| *Variable Age* | | | | | | |
| 33 | 1 (1/0) | 5 (4/1) | 0.0000 | 0.3200 | $\frac4{15}$ | 0.2667 |
| 40 | 2 (2/0) | 4 (3/1) | 0.0000 | 0.3750 | $\frac14$ | 0.2500 |
| 45.5 | 3 (3/0) | 3 (2/1) | 0.0000 | 0.4444 | $\frac29$ | 0.2222 |
| **49** | **5 (5/0)** | **1 (0/1)** | **0.0000** | **0.0000** | $\mathbf{0}$ | **0.0000** |
| Like Dogs | 4 (3/1) | 2 (2/0) | 0.3750 | 0.0000 | $\frac14$ | 0.2500 |

```{admonition} Aquí está el error
:class: danger

1. `Like Dogs`: la solución reporta 0.17; el valor correcto es $\frac14=0.25$. (El 0.17 es el Gini de
   `Like Gravity` en la raíz; parece haberse copiado.)
2. `Age`: la solución dice que el mejor umbral es 45.5 (0.22, valor que sí es correcto). Pero
   el umbral **49** separa perfectamente ($\{30,36,44,47,47\}$ son todos Sí y $\{51\}$ es No): **Gini
   $=0$**. Se omitió o se calculó mal.
3. Por eso eligen `Like Dogs` ($0.17<0.22$) cuando lo correcto es $\text{Age}<49$ ($0<0.22<0.25$).
```

## 7.3 El árbol final de la solución también está mal

Aun aceptando `Like Dogs` en ese nodo, el árbol publicado tiene dos fallas:

- **Rama `Like Dogs` = Sí** $\{30,44,47\ (\text{Sí});\ 51\ (\text{No})\}$: se declara hoja “Sí”, pero es impura
  ($\text{Gini}=3/8$) y clasifica mal al de 51 años. Habría que seguir dividiendo (por $\text{Age}<49$,
  Gini 0):

  | Partición | Izq. $n$ (Sí/No) | Der. $n$ (Sí/No) | $\text{Gini}_L$ | $\text{Gini}_R$ | $\text{Gini}_{\text{split}}$ | valor |
  |---|:-:|:-:|:-:|:-:|:-:|:-:|
  | 37 | 1 (1/0) | 3 (2/1) | 0.0000 | 0.4444 | $\frac13$ | 0.3333 |
  | 45.5 | 2 (2/0) | 2 (1/1) | 0.0000 | 0.5000 | $\frac14$ | 0.2500 |
  | **49** | **3 (3/0)** | **1 (0/1)** | **0.0000** | **0.0000** | $\mathbf{0}$ | **0.0000** |

- **Rama `Like Dogs` = No** $\{(36,\text{Sí}),(47,\text{Sí})\}$: ya es *pura* y debía ser hoja “Sí”. La solución
  la divide con $\text{Age}<45.5$ y asigna “No” a $\text{Age}\ge45.5$, lo que contradice los datos (el de 47
  años sí es astronauta). Parece que se intercambiaron las ramas.

```{code-cell} ipython3
:tags: [hide-input]

import matplotlib.pyplot as plt
from matplotlib.patches import FancyBboxPatch

AZUL = ("#e8ecfb", "#1f2f8f"); ROJO = ("#fbe3e3", "#b03030")
VERDE = ("#e3f5e3", "#2e7d32"); NARANJA = ("#fde3cf", "#d35400")

def nodo(ax, x, y, texto, est):
    ax.text(x, y, texto, ha="center", va="center", fontsize=12,
            bbox=dict(boxstyle="round,pad=0.35", fc=est[0], ec=est[1], lw=1.4))

def arista(ax, x0, y0, x1, y1, etiqueta):
    ax.annotate("", xy=(x1, y1 + 0.32), xytext=(x0, y0 - 0.3),
                arrowprops=dict(arrowstyle="->", color="k", lw=1))
    ax.text((x0 + x1) / 2 + (0.15 if x1 > x0 else -0.15), (y0 + y1) / 2, etiqueta,
            fontsize=10, ha="center", va="center")

fig, (a1, a2) = plt.subplots(1, 2, figsize=(10, 5))
for ax, t in [(a1, "Árbol publicado"), (a2, "Árbol corregido")]:
    ax.set_xlim(-0.5, 5.5); ax.set_ylim(-0.6, 4.6); ax.axis("off")
    ax.set_title(t, fontweight="bold")

# publicado
nodo(a1, 2.5, 4, "Like Gravity?", AZUL)
arista(a1, 2.5, 4, 0.8, 3, "No");  nodo(a1, 0.8, 3, "No\n$n=4$", ROJO)
arista(a1, 2.5, 4, 3.8, 3, "Sí");  nodo(a1, 3.8, 3, "Like Dogs?", AZUL)
arista(a1, 3.8, 3, 2.6, 1.8, "Sí"); nodo(a1, 2.6, 1.8, "Sí\ncontiene 1 No (51)", NARANJA)
arista(a1, 3.8, 3, 4.6, 1.8, "No"); nodo(a1, 4.6, 1.8, "Age < 45.5?", AZUL)
arista(a1, 4.6, 1.8, 3.9, 0.4, "Sí"); nodo(a1, 3.9, 0.4, "Sí", VERDE)
arista(a1, 4.6, 1.8, 5.2, 0.4, "No"); nodo(a1, 5.2, 0.4, "No\nel de 47 es Sí", NARANJA)

# corregido
nodo(a2, 2.5, 4, "Like Gravity?", AZUL)
arista(a2, 2.5, 4, 1.2, 3, "No");  nodo(a2, 1.2, 3, "No\n$n=4$", ROJO)
arista(a2, 2.5, 4, 3.8, 3, "Sí");  nodo(a2, 3.8, 3, "Age < 49?", AZUL)
arista(a2, 3.8, 3, 3.0, 1.8, "Sí"); nodo(a2, 3.0, 1.8, "Sí\n$n=5$", VERDE)
arista(a2, 3.8, 3, 4.6, 1.8, "No"); nodo(a2, 4.6, 1.8, "No\n$n=1$", ROJO)
plt.tight_layout(); plt.show()
```

**Figura 4.** Izquierda: árbol de la solución (hojas naranjas = asignaciones contradichas por los datos).
Derecha: árbol corregido.

| (Age, Dogs, Gravity) | Real | Publicado | Corregido |
|:-:|:-:|:-:|:-:|
| (24,0,0) | No | No | No |
| (30,1,1) | Sí | Sí | Sí |
| (36,0,1) | Sí | Sí | Sí |
| (36,0,0) | No | No | No |
| (42,0,0) | No | No | No |
| (44,1,1) | Sí | Sí | Sí |
| (46,1,0) | No | No | No |
| (47,1,1) | Sí | Sí | Sí |
| **(47,0,1)** | Sí | **No** | Sí |
| **(51,1,1)** | No | **Sí** | No |
| *Exactitud en entrenamiento* | | **8/10** | **10/10** |

```{admonition} Conclusión
:class: seealso

El error *no* está en el primer árbol (umbrales $27,33,36,39,\dots$). Está en el
nodo `Like Gravity`=Sí: Gini de `Like Dogs` $=0.25$ (no 0.17), umbral $\text{Age}=49$ con Gini 0
omitido, y ramas finales mal asignadas. El árbol correcto es: `Like Gravity`=No $\Rightarrow$ No; `Like
Gravity`=Sí $\Rightarrow$ ($\text{Age}<49\Rightarrow$ Sí, en otro caso No), con 100 % de exactitud en entrenamiento.
```

## Recálculo con fracciones exactas

```{code-cell} ipython3
:tags: [hide-input]

from fractions import Fraction as F

age   = [24, 30, 36, 36, 42, 44, 46, 47, 47, 51]
dogs  = [0, 1, 0, 0, 0, 1, 1, 1, 0, 1]
grav  = [0, 1, 1, 0, 0, 1, 0, 1, 1, 1]
astro = [0, 1, 1, 0, 0, 1, 0, 1, 1, 0]

def gini(ys):
    if not ys:
        return F(0)
    p = F(sum(ys), len(ys))
    return 1 - p**2 - (1 - p)**2

def split(ys_l, ys_r):
    N = len(ys_l) + len(ys_r)
    return F(len(ys_l), N) * gini(ys_l) + F(len(ys_r), N) * gini(ys_r)

def tabla(idx, nombre):
    print(f"\n== {nombre}: n={len(idx)}, Gini={gini([astro[i] for i in idx])}")
    vals = sorted({age[i] for i in idx})
    for a in [(u + v) / 2 for u, v in zip(vals, vals[1:])]:
        L = [astro[i] for i in idx if age[i] < a]
        R = [astro[i] for i in idx if age[i] >= a]
        g = split(L, R)
        print(f"  Age < {a:<5}  izq {len(L)} ({sum(L)}/{len(L)-sum(L)})  "
              f"der {len(R)} ({sum(R)}/{len(R)-sum(R)})  Gini_split = {str(g):>5} = {float(g):.4f}")
    for var, v in [("Like Dogs", dogs), ("Like Gravity", grav)]:
        L = [astro[i] for i in idx if v[i] == 1]
        R = [astro[i] for i in idx if v[i] == 0]
        if L and R:
            g = split(L, R)
            print(f"  {var:<12}  Gini_split = {str(g):>5} = {float(g):.4f}")

todos = list(range(10))
tabla(todos, "Raíz")
nodo_g = [i for i in todos if grav[i] == 1]
tabla(nodo_g, "Like Gravity = Sí")
tabla([i for i in nodo_g if dogs[i] == 1], "Like Gravity = Sí, Like Dogs = Sí")
```
