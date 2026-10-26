# 1. k-NN: error verdadero y riesgo esperado (Ec. 2.2)

````{admonition} Enunciado
:class: note

Sea $\Dist$ una distribución de probabilidad sobre $\mathcal X\times\mathcal Y$, con $\mathcal Y=\{0,1\}$, y sea
$h:\mathcal X\to\mathcal Y$ una función medible. La Definición 2.3 de las notas define el *error verdadero*
(riesgo) como

$$
L_\Dist(h) := \Prob_{(\bx,y)\sim\Dist}\big[h(\bx)\neq y\big] := \Dist\big(\{(\bx,y):\ h(\bx)\neq y\}\big).
$$ (eq-riesgo)

Demostrar que:

(a) las dos expresiones de {eq}`eq-riesgo` son el mismo número, y $0\le L_\Dist(h)\le 1$;

(b) $L_\Dist(h)=\E_{(\bx,y)\sim\Dist}\big[\ell_{0\text{-}1}(h,(\bx,y))\big]$; es decir, las Ecs. (2.2) y (2.4) son equivalentes para
la pérdida 0-1;

(c) el riesgo empírico $L_S(h)=|\{i\in[m]: h(\bx_i)\neq y_i\}|/m$ es la misma definición evaluada
en la distribución empírica de $S$, y $\E_{S\sim\Dist^m}[L_S(h)]=L_\Dist(h)$ cuando $h$ no depende de $S$;

(d) *(complemento, se usa en el capítulo 2)* con $\eta(\bx)=\Prob[y=1\mid\bx]$ y $h^\star(\bx)=\Ind_{[\eta(\bx)>1/2]}$ se cumple
$L_\Dist(h^\star)=\E_{\bx}\big[\min\{\eta(\bx),1-\eta(\bx)\}\big]$ y $L_\Dist(g)\ge L_\Dist(h^\star)$ para toda $g$.
````

```{admonition} Nota de interpretación
:class: important

En el archivo, la casilla solo dice «Demostrar» y resalta la Ec. (2.2).
Las notas afirman justo después que (2.2) y (2.4) «son equivalentes» sin dar los pasos; eso
es lo que se demuestra en (a)–(c). El inciso (d) es el resultado del que depende el capítulo 2 (en la
prueba del Lema 2.1 se usa $L_\Dist(h^\star)=\E[\min\{\eta,1-\eta\}]$).
```

```{admonition} Idea
:class: tip

Una probabilidad es la esperanza de una indicadora: $\Prob(A)=\E[\Ind_A]$. Como la pérdida
0-1 *es* la indicadora del evento de error, el riesgo definido por esperanza y el definido por
probabilidad coinciden.
```

## Demostración

````{admonition} Demostración
:class: demo
**Paso 0 (hecho básico).** Si $\alpha$ es una variable aleatoria con valores en $\{0,1\}$,
entonces

$$
\E[\alpha]=0\cdot\Prob(\alpha=0)+1\cdot\Prob(\alpha=1)=\Prob(\alpha=1).
$$

En particular, para cualquier evento $A$, $\E[\Ind_A]=\Prob(A)$. (Es el hecho que las notas recuerdan antes
de afirmar la equivalencia.)

**Paso 1 (el evento de error es medible).** Sea $A_h:=\{(\bx,y)\in\mathcal X\times\mathcal Y:\ h(\bx)\neq y\}$. Como
$y\in\{0,1\}$, tenemos $h(\bx)\neq y$ si y solo si ($h(\bx)=0$ y $y=1$) o ($h(\bx)=1$ y $y=0$). Luego

$$
A_h=\big(h^{-1}(0)\times\{1\}\big)\ \cup\ \big(h^{-1}(1)\times\{0\}\big),
$$

una unión de dos rectángulos medibles (porque $h$ es medible). Por tanto $\Dist(A_h)$ está bien definido.

**Paso 2 (inciso (a)).** La probabilidad de que el par aleatorio $(\bx,y)\sim\Dist$ caiga en $A_h$ es, por
definición de ley de probabilidad, $\Dist(A_h)$:

$$
\Prob_{(\bx,y)\sim\Dist}[h(\bx)\neq y]=\Prob\big[(\bx,y)\in A_h\big]=\Dist(A_h).
$$

Así, las dos expresiones de {eq}`eq-riesgo` son el mismo número (la segunda es la primera escrita como
medida de un conjunto). Como $\Dist$ es una medida de probabilidad, $0\le\Dist(A_h)\le\Dist(\mathcal X\times\mathcal Y)=1$.

**Paso 3 (inciso (b)).** Por definición,

$$
\ell_{0\text{-}1}(h,(\bx,y))=\begin{cases}0, & h(\bx)=y,\\ 1, & h(\bx)\neq y,\end{cases}
\qquad\text{es decir,}\qquad \ell_{0\text{-}1}(h,(\bx,y))=\Ind_{A_h}(\bx,y).
$$

Entonces $\alpha:=\ell_{0\text{-}1}(h,(\bx,y))$ toma valores en $\{0,1\}$ y por el Paso 0,

$$
\E_{(\bx,y)\sim\Dist}\big[\ell_{0\text{-}1}(h,(\bx,y))\big]=\Prob[\alpha=1]=\Prob[(\bx,y)\in A_h]=\Dist(A_h)=L_\Dist(h).
$$

Es decir, la definición (2.4) con $\ell=\ell_{0\text{-}1}$ reproduce exactamente (2.2).

**Paso 4 (inciso (c)).** La distribución empírica de $S=((\bx_1,y_1),\dots,(\bx_m,y_m))$ es
$\Dist_S:=\frac1m\sum_{i=1}^m\delta_{(\bx_i,y_i)}$, que asigna a cada conjunto $A$ la proporción de datos que caen en él. Entonces

$$
\Dist_S(A_h)=\frac1m\sum_{i=1}^m\Ind_{A_h}(\bx_i,y_i)=\frac{|\{i\in[m]:\ h(\bx_i)\neq y_i\}|}{m}=L_S(h).
$$

Si además $h$ no depende de $S$ y los $(\bx_i,y_i)$ son i.i.d. con ley $\Dist$, por linealidad de la esperanza,

$$
\E_{S\sim\Dist^m}[L_S(h)]=\frac1m\sum_{i=1}^m\E\big[\Ind_{A_h}(\bx_i,y_i)\big]=\frac1m\cdot m\cdot\Dist(A_h)=L_\Dist(h).
$$

**Paso 5 (inciso (d)).** Condicionando en $\bx$ (ley de la esperanza total), $L_\Dist(h)=\E_{\bx}\big[\Prob(y\neq h(\bx)\mid\bx)\big]$. Para $\bx$ fijo:

- si $h(\bx)=0$, hay error cuando $y=1$, con probabilidad $\eta(\bx)$;
- si $h(\bx)=1$, hay error cuando $y=0$, con probabilidad $1-\eta(\bx)$.

Por tanto $\Prob(y\neq h(\bx)\mid\bx)\in\{\eta(\bx),1-\eta(\bx)\}$, y en cualquier caso

$$
\Prob(y\neq h(\bx)\mid\bx)\ \ge\ \min\{\eta(\bx),1-\eta(\bx)\}.
$$ (eq-cond-min)

Para $h^\star(\bx)=\Ind_{[\eta(\bx)>1/2]}$: si $\eta(\bx)>1/2$ entonces $h^\star(\bx)=1$ y el error condicional es $1-\eta(\bx)$, que
es el mínimo (pues $1-\eta<1/2<\eta$); si $\eta(\bx)\le1/2$ entonces $h^\star(\bx)=0$ y el error condicional es
$\eta(\bx)$, de nuevo el mínimo. Así, {eq}`eq-cond-min` se cumple con igualdad para $h^\star$. Tomando esperanza en $\bx$:

$$
L_\Dist(h^\star)=\E_{\bx}\big[\min\{\eta(\bx),1-\eta(\bx)\}\big]\ \le\ L_\Dist(g)\quad\text{para toda } g.
$$
````

```{admonition} Observación
:class: warning

La regla de Bayes de la Ec. (2.3) usa $\ge 1/2$ y la del Lema 2.1 usa $>1/2$.
Difieren solo en $\{\eta=1/2\}$, donde ambos errores condicionales valen $1/2$; por eso tienen el
mismo riesgo. *Ejemplo:* si $h$ clasifica mal 2 de $m=8$ ejemplos, $L_S(h)=2/8=0.25$, que es
la proporción de datos en $A_h$.
```
