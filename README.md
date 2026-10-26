# Demostraciones de Machine Learning — Jupyter Book

Libro (Jupyter Book 1.x) con las demostraciones resueltas de la tarea de Machine Learning
(k-NN, Ridge, Lasso, Árboles y AdaBoost), basado en las notas del Dr. Lihki Rubio.

## Construir

```bash
pip install -r requirements.txt
jupyter-book build .
# abrir _build/html/index.html
```

Las celdas de código (figuras y verificaciones numéricas) se ejecutan al construir el libro.

## Publicar en GitHub Pages

```bash
pip install ghp-import
ghp-import -n -p -f _build/html
```

## Estructura

| Archivo | Contenido |
|---|---|
| `intro.md` | Portada, panorama y convenciones |
| `cap01_knn_riesgo.md` | Error verdadero y riesgo esperado (Ec. 2.2) |
| `cap02_knn_desigualdad.md` | $2\eta(1-\eta)\le 2\min\{\eta,1-\eta\}$ (+ Figura 1) |
| `cap03_ridge_propiedades.md` | Media, covarianza, sesgo y ECM de ridge |
| `cap04_ridge_intercepto.md` | $Z^T\mathbf 1=0$ y derivadas |
| `cap05_ridge_estandarizacion.md` | Estandarización y $\beta_0^\star=0$ |
| `cap06_lasso.md` | KKT, umbral suave, LARS (+ Figuras 2 y 3) |
| `cap07_arboles.md` | Error del Ejercicio 6 (+ Figura 4, Gini exacto) |
| `cap08_adaboost.md` | Derivación de AdaBoost y cota del error (+ Figura 5) |
| `cap09_verificaciones.md` | Verificaciones numéricas |
| `referencias.md` | Referencias |
