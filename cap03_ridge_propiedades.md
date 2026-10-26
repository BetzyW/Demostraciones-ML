# 3. Ridge: propiedades del estimador (Ec. 3.12)

````{admonition} Enunciado
:class: note

Considere el modelo $\by=X\bbeta+\beps$ con $X\in\mathbb R^{n\times p}$ fija (datos estandarizados, ver
el capítulo 5), $\E(\beps)=\cero$, $\Cov(\beps)=\sigma^2I_n$, y el estimador ridge con $\lambda>0$,

$$
\hat\bbeta_R=(X^\T X+\lambda I_p)^{-1}X^\T\by.
$$

Si $l_1,\dots,l_p$ son los autovalores de $X^\T X$, demostrar

$$
\E(\hat\bbeta_R)=(X^\T X+\lambda I_p)^{-1}X^\T X\,\bbeta,
$$ (eq-r-media)

$$
\Cov(\hat\bbeta_R)=\sigma^2(X^\T X+\lambda I_p)^{-1}X^\T X(X^\T X+\lambda I_p)^{-1},
$$ (eq-r-cov)

$$
\E(\hat\bbeta_R-\bbeta)=-\lambda(X^\T X+\lambda I_p)^{-1}\bbeta,
$$ (eq-r-sesgo)

$$
\E\big[(\hat\bbeta_R-\bbeta)^\T(\hat\bbeta_R-\bbeta)\big]=\sigma^2\sum_{j=1}^p\frac{l_j}{(l_j+\lambda)^2}+\lambda^2\bbeta^\T(X^\T X+\lambda I_p)^{-2}\bbeta.
$$ (eq-r-ecm)
````

```{admonition} Notación
:class: important

$A:=X^\T X$ (simétrica, semidefinida positiva) y $M:=(A+\lambda I_p)^{-1}$. Entonces
$\hat\bbeta_R=MX^\T\by$. Estas son las propiedades que las notas dejan como «ejercicio para el lector».
```

```{admonition} Idea
:class: tip

Con $\hat\bbeta_R=MX^\T\by$ *lineal* en $\by$, la media y la covarianza se obtienen con las reglas de
transformaciones lineales. El sesgo sale de la identidad $MA=I-\lambda M$. El error cuadrático
es «traza de la covarianza + sesgo²», y la traza se calcula diagonalizando $A$.
```

## Lemas auxiliares

````{prf:lemma} $M$ está bien definida
:label: lem-M

$A+\lambda I_p$ es simétrica definida positiva; por tanto es invertible,
$M$ es simétrica, y $M$ conmuta con $A$. Además

$$
MA=AM=I_p-\lambda M.
$$ (eq-MA)
````

````{admonition} Demostración
:class: demo
Para $\bv\neq\cero$: $\bv^\T(A+\lambda I)\bv=\lVert X\bv\rVert^2+\lambda\lVert\bv\rVert^2\ge\lambda\lVert\bv\rVert^2>0$. Una matriz
simétrica definida positiva es invertible y su inversa es simétrica. Como $(A+\lambda I)A=A(A+\lambda I)$,
multiplicando por $M$ a ambos lados se obtiene $AM=MA$. Finalmente $A=(A+\lambda I)-\lambda I=M^{-1}-\lambda I$,
de donde $MA=M(M^{-1}-\lambda I)=I-\lambda M$.
````

````{prf:lemma} Transformación lineal de un vector aleatorio
:label: lem-lineal

Si $W=\bc+BY$ con $\bc$ y $B$ constantes, entonces $\E[W]=\bc+B\,\E[Y]$ y $\Cov(W)=B\,\Cov(Y)\,B^\T$.
````

````{admonition} Demostración
:class: demo
La primera igualdad es linealidad de la esperanza. Para la segunda, $W-\E[W]=B(Y-\E[Y])$,
así que $\Cov(W)=\E\big[B(Y-\E Y)(Y-\E Y)^\T B^\T\big]=B\,\Cov(Y)\,B^\T$.
````

## Demostración de las cuatro propiedades

````{admonition} Demostración
:class: demo
Como $X$ y $\bbeta$ no son aleatorios, $\E(\by)=X\bbeta$ y $\Cov(\by)=\Cov(\beps)=\sigma^2I_n$.

**{eq}`eq-r-media` Media.** Por el {prf:ref}`lem-lineal` con $B=MX^\T$: $\E(\hat\bbeta_R)=MX^\T\E(\by)=MX^\T X\bbeta=MA\,\bbeta$.

**{eq}`eq-r-cov` Covarianza.** Por el {prf:ref}`lem-lineal`, $\Cov(\hat\bbeta_R)=(MX^\T)(\sigma^2I_n)(MX^\T)^\T=\sigma^2MX^\T XM^\T=\sigma^2MAM$,
usando que $M^\T=M$.

**{eq}`eq-r-sesgo` Sesgo.** Por {eq}`eq-r-media` y {eq}`eq-MA`,

$$
\E(\hat\bbeta_R)-\bbeta=(MA-I)\bbeta=(I-\lambda M-I)\bbeta=-\lambda M\bbeta.
$$

**{eq}`eq-r-ecm` Error cuadrático.** Sean $\bu:=\hat\bbeta_R-\E(\hat\bbeta_R)$ (aleatorio, de media cero) y $\bb:=\E(\hat\bbeta_R)-\bbeta$
(constante). Entonces $\hat\bbeta_R-\bbeta=\bu+\bb$ y

$$
\E\lVert\hat\bbeta_R-\bbeta\rVert^2=\E\lVert\bu\rVert^2+2\bb^\T\underbrace{\E[\bu]}_{=\cero}+\lVert\bb\rVert^2=\tr\Cov(\hat\bbeta_R)+\lVert\bb\rVert^2,
$$

pues $\E\lVert\bu\rVert^2=\E[\tr(\bu\bu^\T)]=\tr\E[\bu\bu^\T]=\tr\Cov(\hat\bbeta_R)$.

*Término de varianza.* Diagonalizamos $A=\Gamma L\Gamma^\T$ con $\Gamma$ ortogonal y $L=\operatorname{diag}(l_1,\dots,l_p)$. Entonces
$A+\lambda I=\Gamma(L+\lambda I)\Gamma^\T$ y $M=\Gamma(L+\lambda I)^{-1}\Gamma^\T$, luego

$$
MAM=\Gamma(L+\lambda I)^{-1}L(L+\lambda I)^{-1}\Gamma^\T=\Gamma\operatorname{diag}\Big(\tfrac{l_j}{(l_j+\lambda)^2}\Big)\Gamma^\T.
$$

Por la propiedad cíclica de la traza, $\tr(\Gamma D\Gamma^\T)=\tr(D\Gamma^\T\Gamma)=\tr D$, así que
$\tr\Cov(\hat\bbeta_R)=\sigma^2\sum_{j=1}^p\frac{l_j}{(l_j+\lambda)^2}$.

*Término de sesgo.* Por {eq}`eq-r-sesgo`, $\lVert\bb\rVert^2=\lambda^2\bbeta^\T M^\T M\bbeta=\lambda^2\bbeta^\T M^2\bbeta=\lambda^2\bbeta^\T(X^\T X+\lambda I)^{-2}\bbeta$.

Sumando ambos términos se obtiene {eq}`eq-r-ecm`.
````

```{admonition} Interpretación: el compromiso sesgo–varianza
:class: warning

En la base de autovectores, $\lVert\bb\rVert^2=\sum_j\frac{\lambda^2}{(l_j+\lambda)^2}(\bgamma_j^\T\bbeta)^2$, que *crece* con $\lambda$, mientras que cada sumando
$\frac{l_j}{(l_j+\lambda)^2}$ de la varianza *decrece* (su derivada es $-2l_j/(l_j+\lambda)^3<0$).

- Con $\lambda=0$ (MCO): sesgo 0 y varianza total $\sigma^2\sum_j 1/l_j$, que explota si algún $l_j\approx0$
  (multicolinealidad). Esto justifica ridge.
- Como la derivada del error cuadrático en $\lambda=0$ es $-2\sigma^2\sum_j l_j^{-2}<0$ (el término de
  sesgo² tiene derivada 0 en $\lambda=0$ por ser $\propto\lambda^2$), existe $\lambda>0$ con error cuadrático menor
  que el de MCO.
- Con $\lambda\to\infty$: $\hat\bbeta_R\to\cero$, sesgo $\to-\bbeta$ y varianza $\to0$.
```
