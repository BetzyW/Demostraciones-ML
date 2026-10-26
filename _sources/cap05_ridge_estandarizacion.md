# 5. Ridge: estandarización (Ec. 3.11) y $\beta_0^\star=\bar y=0$

````{admonition} Enunciado
:class: note

Se estandarizan la respuesta y los predictores mediante $y_i-\bar y$ y $(x_{ij}-\bar x_j)/s_j$,
con $\bar x_j=\frac1n\sum_i x_{ij}$ y $s_j^2=\frac1n\sum_i(x_{ij}-\bar x_j)^2>0$. Denotando de nuevo por $y_i$ y $x_{ij}$ los datos
transformados (como en las notas), demostrar

$$
\sum_{i=1}^n y_i=0,\qquad \sum_{i=1}^n x_{ij}=0\ \ (j=1,\dots,p),\qquad \sum_{i=1}^n x_{ij}^2=n,
$$ (eq-estandar)

y que entonces $\beta_0^\star=\bar y=\frac1n\sum_i y_i=0$.
````

```{admonition} Idea
:class: tip

Cada identidad sale de la linealidad de la suma. La última usa la definición de $s_j^2$ como
promedio de cuadrados centrados. El intercepto estimado es el promedio de la respuesta (capítulo 4),
y el promedio de una respuesta centrada es cero.
```

## Demostración

````{admonition} Demostración
:class: demo
Sean $\tilde y_i=y_i-\bar y$ y $\tilde x_{ij}=(x_{ij}-\bar x_j)/s_j$ los datos transformados.

**Respuesta.** $\displaystyle\sum_{i=1}^n\tilde y_i=\sum_{i=1}^n y_i-n\bar y=n\bar y-n\bar y=0.$

**Predictores (suma).** Es exactamente el cálculo del capítulo 4(a): $\displaystyle\sum_{i=1}^n\tilde x_{ij}=\frac1{s_j}\Big(\sum_i x_{ij}-n\bar x_j\Big)=0.$

**Predictores (suma de cuadrados).** Por la definición de $s_j^2$,

$$
\sum_{i=1}^n\tilde x_{ij}^2=\frac1{s_j^2}\sum_{i=1}^n(x_{ij}-\bar x_j)^2=\frac1{s_j^2}\cdot n\,s_j^2=n.
$$

**El intercepto.** Aplicamos el resultado del capítulo 4(b) a la respuesta transformada $\tilde\by$: el mínimo en
$\beta_0^\star$ se alcanza en el promedio de la respuesta, que ahora es $\hat\beta_0^\star=\frac1n\sum_i\tilde y_i=0$ por la primera
igualdad.
````

```{admonition} Consecuencias
:class: warning

1. **«Sin pérdida de generalidad».** Con $\beta_0^\star=0$ se puede trabajar con el modelo sin
   intercepto $\by=X\bbeta+\beps$ (como hacen las notas), y recuperar el intercepto original con
   $\hat\beta_0=\bar y-\sum_j\hat\beta_j\bar x_j$.
2. **Centrar $\by$ no cambia las pendientes.** Como $Z^\T\uno=\cero$, $Z^\T(\by-\bar y\uno)=Z^\T\by-\bar y\,Z^\T\uno=Z^\T\by$,
   así que $\hat\bbeta_s$ es el mismo con $\by$ centrado o sin centrar.
3. **$\frac1nX^\T X$ es la matriz de correlaciones.** Por {eq}`eq-estandar`, $\frac1n\sum_i\tilde x_{ij}\tilde x_{ik}=\frac1{n\,s_js_k}\sum_i(x_{ij}-\bar x_j)(x_{ik}-\bar x_k)=r_{jk}$,
   la correlación muestral entre los predictores $j$ y $k$. En particular, la diagonal
   de $X^\T X$ es $n$. (Esto se usa en el caso ortogonal del Lasso, capítulo 6.)
4. **Por qué estandarizar.** Ridge penaliza $\sum_j\beta_j^2$, y esa suma depende de las unidades de
   cada predictor: con $\sum_i x_{ij}^2=n$ todos los predictores quedan en la misma escala y la
   penalización los trata por igual.
```
