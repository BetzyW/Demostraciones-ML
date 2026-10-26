# Demostraciones de la tarea de Machine Learning

**k-NN · Regresión Ridge y Lasso · Árboles y AdaBoost**

*Solución detallada con base en las notas de clase del Dr. Lihki Rubio — 25 de octubre de 2026*

Este libro resuelve, con el mayor detalle posible, las ocho casillas del archivo
`Demostraciones_Tarea.docx`, usando la notación y los resultados de las notas de clase
del Dr. Lihki Rubio (<https://lihkir.github.io/MachineLearning/>). Cada capítulo sigue el
mismo esquema: *enunciado*, *idea*, *demostración paso a paso* y *observaciones*. Además, todas las
fórmulas y los cálculos numéricos se comprobaron por computador (ver el capítulo
{doc}`cap09_verificaciones`).

## Panorama

| § | Tema | Qué se demuestra | Idea clave |
|---|------|------------------|------------|
| 1 | k-NN | Ec. (2.2): $L_\Dist(h)=\Prob[h(\bx)\neq y]=\Dist(\{h(\bx)\neq y\})=\E[\ell_{0\text{-}1}]$, y error de Bayes | Probabilidad = esperanza de una indicadora |
| 2 | k-NN | $2\eta(1-\eta)\le 2\min\{\eta,1-\eta\}$ (caso $1-\eta(\bx)$) | $\eta(1-\eta)=\min\cdot\max$ y $\max\le 1$ |
| 3 | Ridge | Propiedades (3.12): media, covarianza, sesgo y error cuadrático | $M(X^\T X)=I-\lambda M$ y diagonalización de $X^\T X$ |
| 4 | Ridge | $Z^\T\uno=\uno^\T Z=\cero$; derivada (3.8); solución para $\bbeta_s$ | Centrar las columnas las hace ortogonales a $\uno$ |
| 5 | Ridge | Igualdades (3.11) y $\beta_0^\star=\bar y=0$ | Linealidad de la suma |
| 6 | Lasso | Estimadores lasso: KKT, umbral suave, conjunto activo, LARS | Subdiferencial de $\lvert\cdot\rvert$ |
| 7 | Árboles | Ejercicio 6 (Sec. 12): ubicar el error en la solución | Recalcular Gini con fracciones exactas |
| 8 | AdaBoost | Origen de los pasos del Alg. 5.1 y error de entrenamiento $\le e^{-2\gamma^2K}$ | $\Ind_{[f\neq y]}\le e^{-yF}$ y producto de $Z_i$ |

```{admonition} Dos hallazgos que conviene conocer antes de leer
:class: danger

1. **§2.** En las notas, la cadena intermedia $2(1-\eta)^2\le 2\min\{\eta,1-\eta\}$ es *falsa* en general
   (p. ej. $\eta=0.1$ da $1.62\le 0.20$). La desigualdad final sí es correcta; aquí se da una prueba
   válida para ambos casos.
2. **§7.** El error de la solución del Ejercicio 6 *no* está en el primer árbol (umbrales
   $27, 33, 36, 39,\dots$), cuyos números son correctos. Está en el segundo nivel: el índice de
   `Like Dogs` se reporta como $0.17$ cuando es $0.25$, se omite el umbral $\text{Age}=49$ (que da
   $\text{Gini}=0$) y el árbol final asigna mal las ramas, clasificando mal 2 de los 10 datos de
   entrenamiento.
```

## Convenciones

Los vectores son columnas; $\lVert\cdot\rVert$ es la norma euclídea; $\uno$ es el vector de unos; $(t)_+=\max\{t,0\}$;
$\sgn(t)\in\{-1,0,1\}$ es el signo. La función indicadora se escribe $\Ind_A$ (las notas la escriben $\unicode{x1D7D9}_A$).
Cuando una casilla del archivo solo dice «Demostrar», se indica explícitamente en una *nota de
interpretación* qué resultado se tomó; si la intención era otra, la estructura de cada capítulo permite
adaptarla fácilmente.

```{tableofcontents}
```
