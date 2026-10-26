# 4. Ridge: $Z^\T\uno=\cero$ y la derivada respecto al intercepto (Ec. 3.8)

````{admonition} Enunciado
:class: note

Sea $Z\in\mathbb R^{n\times p}$ la matriz de predictores estandarizados, $z_{ij}=(x_{ij}-\bar x_j)/s_j$ con
$s_j>0$, y

$$
S_\lambda(\beta_0^\star,\bbeta_s)=(\by-\beta_0^\star\uno-Z\bbeta_s)^\T(\by-\beta_0^\star\uno-Z\bbeta_s)+\lambda\bbeta_s^\T\bbeta_s.
$$

Demostrar que:

(a) $Z^\T\uno=\uno^\T Z=\cero$ (lo que las notas piden «verificar»);

(b) $\dfrac{\partial S_\lambda}{\partial\beta_0^\star}=-2n\bar y+2n\beta_0^\star$, de modo que $\hat\beta_0^\star=\bar y$ (Ec. 3.8);

(c) *(complemento)* $\dfrac{\partial S_\lambda}{\partial\bbeta_s}=-2Z^\T\by+2Z^\T Z\bbeta_s+2\lambda\bbeta_s$ y $\hat\bbeta_s=(Z^\T Z+\lambda I_p)^{-1}Z^\T\by$ (Ec. 3.10).
````

```{admonition} Idea
:class: tip

Centrar las columnas de $Z$ las vuelve ortogonales al vector $\uno$. Eso hace que el intercepto
y los coeficientes de pendiente no interactúen en las ecuaciones de optimalidad.
```

## Demostración de (a)

````{admonition} Demostración
:class: demo
La $j$-ésima componente de $Z^\T\uno$ es

$$
(Z^\T\uno)_j=\sum_{i=1}^n z_{ij}=\frac1{s_j}\sum_{i=1}^n(x_{ij}-\bar x_j)=\frac1{s_j}\Big(\sum_{i=1}^n x_{ij}-n\bar x_j\Big)=\frac1{s_j}\big(n\bar x_j-n\bar x_j\big)=0,
$$

pues $\bar x_j=\frac1n\sum_i x_{ij}$. Esto vale para $j=1,\dots,p$, luego $Z^\T\uno=\cero$. Trasponiendo, $\uno^\T Z=\cero^\T$.
````

## Demostración de (b)

````{admonition} Demostración
:class: demo
**Expansión de $S_\lambda$.** Sea $\br=\by-\beta_0^\star\uno-Z\bbeta_s$. Desarrollando $\br^\T\br$ término a término:

$$
\br^\T\br=\by^\T\by+\beta_0^{\star2}\uno^\T\uno+\bbeta_s^\T Z^\T Z\bbeta_s-\beta_0^\star\by^\T\uno-\beta_0^\star\uno^\T\by-\by^\T Z\bbeta_s-\bbeta_s^\T Z^\T\by+\beta_0^\star\uno^\T Z\bbeta_s+\beta_0^\star\bbeta_s^\T Z^\T\uno.
$$

Cada término cruzado es un escalar y por tanto igual a su transpuesto ($\by^\T\uno=\uno^\T\by$, $\by^\T Z\bbeta_s=\bbeta_s^\T Z^\T\by$,
$\uno^\T Z\bbeta_s=\bbeta_s^\T Z^\T\uno$). Usando también $\uno^\T\uno=n$ y $\uno^\T\by=n\bar y$:

$$
S_\lambda=\by^\T\by-2n\bar y\,\beta_0^\star-2\by^\T Z\bbeta_s+2\beta_0^\star\uno^\T Z\bbeta_s+n\beta_0^{\star2}+\bbeta_s^\T Z^\T Z\bbeta_s+\lambda\bbeta_s^\T\bbeta_s.
$$ (eq-S-expandida)

(En las notas, en el cuarto término aparece $\beta_0$; debe ser $\beta_0^\star$.)

**Derivada.** Solo dependen de $\beta_0^\star$ los términos segundo, cuarto y quinto de {eq}`eq-S-expandida`:

$$
\frac{\partial S_\lambda}{\partial\beta_0^\star}=-2n\bar y+2\underbrace{\uno^\T Z}_{=\cero^\T\text{ por (a)}}\bbeta_s+2n\beta_0^\star=-2n\bar y+2n\beta_0^\star.
$$

Igualando a cero, $\hat\beta_0^\star=\bar y$. Como $\partial^2S_\lambda/\partial(\beta_0^\star)^2=2n>0$, es un mínimo.
````

```{admonition} Observación
:class: warning

Sin la propiedad (a) quedaría $\beta_0^\star=\bar y-\frac1n\uno^\T Z\bbeta_s$, es decir, el intercepto
dependería de las pendientes. Que $\uno^\T Z=\cero^\T$ desacople ambos bloques es lo que permite
estimar primero $\beta_0^\star=\bar y$ y luego $\bbeta_s$ por separado.
```

## Demostración de (c) (complemento: derivada respecto a $\bbeta_s$)

````{admonition} Demostración
:class: demo
**Derivada de una forma cuadrática.** Sea $B$ una matriz simétrica $p\times p$ y $q(\bx)=\bx^\T B\bx=\sum_j\sum_i x_iB_{ij}x_j$. Derivando
respecto a $x_k$ (hay que contar el $x_k$ que aparece como $x_i$ y como $x_j$):

$$
\frac{\partial q}{\partial x_k}=\sum_{j=1}^p B_{kj}x_j+\sum_{i=1}^p x_iB_{ik}=(B\bx)_k+(B^\T\bx)_k=2(B\bx)_k,
$$

usando $B^\T=B$. Luego $\nabla q(\bx)=2B\bx$. Además $\nabla(\ba^\T\bx)=\ba$.

**Aplicación.** $B=Z^\T Z$ es simétrica ($(Z^\T Z)^\T=Z^\T Z$). Derivando {eq}`eq-S-expandida` respecto a $\bbeta_s$ término a término:

$$
\nabla_{\bbeta_s}S_\lambda=-2Z^\T\by+2\beta_0^\star\underbrace{Z^\T\uno}_{=\cero}+2Z^\T Z\bbeta_s+2\lambda\bbeta_s=-2Z^\T\by+2Z^\T Z\bbeta_s+2\lambda\bbeta_s.
$$

Igualando a cero: $(Z^\T Z+\lambda I_p)\bbeta_s=Z^\T\by$. Por el {prf:ref}`lem-M` (con $Z$ en lugar de $X$), $Z^\T Z+\lambda I_p$ es
invertible, de donde

$$
\hat\bbeta_s=(Z^\T Z+\lambda I_p)^{-1}Z^\T\by.
$$

El hessiano $2(Z^\T Z+\lambda I_p)$ es definido positivo, así que $S_\lambda$ es estrictamente convexa y este punto
crítico es el *único* mínimo global.
````

```{admonition} Estimadores en las variables originales
:class: seealso

De $\beta_0^\star=\beta_0+\sum_j\beta_j\bar x_j$ y $\beta_j^\star=s_j\beta_j$ (Ec. 3.5) se obtiene

$$
\hat\beta_j=\hat\beta_{s,j}/s_j,\qquad \hat\beta_0=\bar y-\sum_{j=1}^p\hat\beta_j\bar x_j.
$$
```
