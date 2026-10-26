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

# 9. Verificaciones numéricas

Cada resultado se comprobó con código (Python, NumPy, scikit-learn). Las celdas de este capítulo se
ejecutan al construir el libro, de modo que cualquier lector puede reproducirlas.

- **§2:** en una malla de $10^5$ puntos, $\max[2\eta(1-\eta)-2\min\{\eta,1-\eta\}]=0$; la cadena de las
  notas se viola exactamente para $\eta<0.382$.
- **§3:** simulación Monte Carlo: media, covarianza, sesgo y error cuadrático
  coinciden con las fórmulas; se verificó $MA=I-\lambda M$.
- **§4–5:** $Z^\T\uno=\cero$ y $\sum_iz_{ij}^2=n$ a precisión de máquina; $S_\lambda$ es mínima en $\beta_0^\star=\bar y$; centrar $\by$
  no cambia $\hat\bbeta_s$.
- **§6:** descenso por coordenadas con umbral suave vs. `Lasso` de scikit-learn (con $\alpha=\lambda/2n$); KKT; fórmula
  del conjunto activo en cada nodo de `lars_path`; $\lambda_{\max}$ coincide con el primer nodo.
- **§7:** todos los índices de Gini con fracciones exactas (ver el capítulo 7).
- **§8:** la cadena $L_S(f)\le\prod Z_i\le e^{-2\sum\gamma_i^2}$ se cumple en todas las iteraciones (ver el capítulo 8).

```{code-cell} ipython3
import numpy as np

# §2 ------------------------------------------------------------------------
eta = np.linspace(0, 1, 100_001)
print("§2  max[2η(1-η) - 2min] =", (2*eta*(1-eta) - 2*np.minimum(eta, 1-eta)).max())
m = eta <= 0.5
viol = eta[m][2*(1-eta[m])**2 > 2*eta[m]]
print("    la cadena de las notas falla para η <", round(viol.max(), 4),
      " (teórico (3-√5)/2 =", round((3-np.sqrt(5))/2, 4), ")")
```

```{code-cell} ipython3
# §3 ------------------------------------------------------------------------
rng = np.random.default_rng(0)
n, p, lam, sigma, R = 30, 4, 5.0, 1.0, 200_000
X = rng.normal(size=(n, p)); X = (X - X.mean(0)) / X.std(0)
beta = np.array([1.0, -0.5, 0.0, 2.0])
A = X.T @ X
M = np.linalg.inv(A + lam*np.eye(p))
print("§3  ‖MA - (I-λM)‖ =", np.abs(M@A - (np.eye(p) - lam*M)).max())

eps = rng.normal(scale=sigma, size=(R, n))
Y = X @ beta + eps                                 # R réplicas de y
B = Y @ X @ M                                      # filas = β̂_R (M simétrica)
l = np.linalg.eigvalsh(A)
media_t = M @ A @ beta
cov_t = sigma**2 * M @ A @ M
ecm_t = sigma**2*np.sum(l/(l+lam)**2) + lam**2 * beta @ M @ M @ beta
print("    media:  dif. máx =", np.abs(B.mean(0) - media_t).max().round(4))
print("    cov:    dif. máx =", np.abs(np.cov(B.T) - cov_t).max().round(4))
print("    sesgo:  dif. máx =", np.abs((B.mean(0) - beta) - (-lam*M@beta)).max().round(4))
print("    ECM:    MC =", np.mean(np.sum((B-beta)**2, 1)).round(4), " fórmula =", ecm_t.round(4))
```

```{code-cell} ipython3
# §4–5 ----------------------------------------------------------------------
Xo = rng.normal(loc=3, scale=2, size=(n, p)); yo = rng.normal(loc=10, size=n)
Z = (Xo - Xo.mean(0)) / Xo.std(0)
print("§4  ‖Zᵀ1‖∞ =", np.abs(Z.T @ np.ones(n)).max(), "  Σz² =", (Z**2).sum(0))

def S(b0, bs):
    r = yo - b0 - Z @ bs
    return r @ r + lam * bs @ bs

bs_hat = np.linalg.solve(Z.T@Z + lam*np.eye(p), Z.T @ yo)
grid = yo.mean() + np.linspace(-1, 1, 2001)
print("    argmin_β0 S =", grid[np.argmin([S(b, bs_hat) for b in grid])].round(4),
      "  ȳ =", yo.mean().round(4))
bs_c = np.linalg.solve(Z.T@Z + lam*np.eye(p), Z.T @ (yo - yo.mean()))
print("§5  centrar y cambia β̂_s en", np.abs(bs_hat - bs_c).max())
```

```{code-cell} ipython3
# §6 ------------------------------------------------------------------------
from sklearn.linear_model import Lasso, lars_path

n, p = 60, 6
X = rng.normal(size=(n, p)); X[:, 3] += 0.6*X[:, 0]
y = X @ np.array([3, -2, 0, .3, .8, .3]) + rng.normal(scale=1.5, size=n)
X = (X - X.mean(0)) / X.std(0); y = y - y.mean()
lam = 40.0

def soft(z, t):
    return np.sign(z) * np.maximum(np.abs(z) - t, 0)

b = np.zeros(p)
for _ in range(2000):
    for j in range(p):
        r = y - X @ b + X[:, j]*b[j]
        b[j] = soft(X[:, j] @ r / n, lam/(2*n))

sk = Lasso(alpha=lam/(2*n), fit_intercept=False, tol=1e-14, max_iter=100_000).fit(X, y).coef_
print("§6  descenso por coordenadas vs sklearn:", np.abs(b - sk).max())

c = X.T @ (y - X @ b)
act = b != 0
print("    KKT activos: ", np.allclose(2*c[act], lam*np.sign(b[act])),
      "  inactivos:", np.all(np.abs(2*c[~act]) <= lam + 1e-9))

alphas, _, coefs = lars_path(X, y, method="lasso")
err = 0.0
for k, a in enumerate(alphas[:-1]):
    L = 2*n*a; bk = coefs[:, k]; A_ = bk != 0
    if A_.any():
        XA = X[:, A_]
        f = np.linalg.solve(XA.T@XA, XA.T@y - L/2*np.sign(bk[A_]))
        err = max(err, np.abs(f - bk[A_]).max())
print("    fórmula del conjunto activo en los nodos de LARS, error máx:", err)
print("    λ_max =", 2*np.abs(X.T@y).max(), "  primer nodo:", 2*n*alphas[0])
```

```{admonition} Limitación
:class: warning

Las figuras `TREEDECISION1--6` de la página de soluciones no pudieron abrirse; el
análisis del capítulo 7 se apoya en el recálculo y en el texto de la solución. Los datos de las
figuras 3 y 5 son simulados (semilla fija), por lo que sus valores concretos pueden diferir de los del
documento original; las propiedades verificadas no dependen de ellos.
```
