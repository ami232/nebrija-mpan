# 4.3 Comparar dos grupos

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/04-contraste-hipotesis/dos-muestras/>

> ℹ️ **Antes de empezar**
>
> Esta página usa el procedimiento de la [página 4.1](logica.md) y los contrastes Z y t de la [página 4.2](una-muestra.md), en especial la [t de Student](una-muestra.md#la-t-de-student) y la [relación con los intervalos de confianza](una-muestra.md#contraste-e-intervalo-de-confianza).

## El problema

Muchas preguntas de negocio no comparan un dato con una cifra fija, sino **dos grupos entre sí**. ¿Compran más los clientes que reciben el email el lunes que los que lo reciben otro día? ¿Gastan más los clientes que reciben la versión nueva del email? ¿Preparan los empleados del almacén los pedidos más deprisa con el nuevo sistema? Es la pregunta de cualquier test A/B.

En todas ellas, la hipótesis nula es que **no hay diferencia** entre los grupos, y el estadístico sigue la misma forma de siempre: la diferencia observada entre los grupos, dividida entre su error estándar. Lo que cambia de un caso a otro es cómo se calcula ese error estándar, y eso depende de dos cosas: si comparamos **proporciones** o **medias**, y si los dos grupos son **independientes** o están **emparejados**.

## Dos proporciones

En el tema anterior vimos que los lunes la tasa de compra parece mayor que el resto de la semana. Con las ocho semanas de datos:

| | Emails | Ventas | Tasa |
|---|---:|---:|---:|
| Lunes | 4000 | 373 | 9.33 % |
| Resto de días | 24 000 | 1595 | 6.65 % |

¿Es la diferencia de 2.7 puntos real, o podría deberse al azar? Si la tasa fuera la misma todos los días, la diferencia entre las dos proporciones muestrales $\hat{p}_1 - \hat{p}_2$ estaría centrada en cero. Su error estándar sale de las propiedades de la varianza: para dos muestras independientes, la varianza de una diferencia es la suma de las varianzas, $\text{SE}^2 = p_1(1 - p_1)/n_1 + p_2(1 - p_2)/n_2$. Si $H_0$ es cierta, las dos tasas son una misma $p$, y la mejor estimación de esa $p$ común junta los dos grupos.

> 📘 **Definición: contraste Z para dos proporciones**
>
> Para contrastar $H_0: p_1 = p_2$ con dos muestras independientes, de tamaños $n_1$ y $n_2$, con $x_1$ y $x_2$ éxitos y proporciones $\hat{p}_1 = x_1/n_1$ y $\hat{p}_2 = x_2/n_2$:
>
> $$z = \frac{\hat{p}_1 - \hat{p}_2}{\sqrt{\hat{p}\,(1 - \hat{p})\left(\dfrac{1}{n_1} + \dfrac{1}{n_2}\right)}} \qquad \text{con } \hat{p} = \frac{x_1 + x_2}{n_1 + n_2}$$
>
> $\hat{p}$ es la **proporción conjunta**: la proporción de éxitos juntando los dos grupos, que es la estimación de la $p$ común si $H_0$ es cierta. Si $H_0$ es cierta, $z$ sigue aproximadamente una normal estándar, siempre que haya al menos 5 éxitos y 5 fracasos en cada grupo.

> ✏️ **Ejemplo resuelto: lunes frente al resto de días**
>
> 1. **Hipótesis.** $H_0: p_{\text{lunes}} = p_{\text{resto}}$ frente a $H_1: p_{\text{lunes}} \ne p_{\text{resto}}$. Bilateral: antes de mirar los datos no teníamos motivo para esperar que los lunes fueran mejores o peores. $\alpha = 0.05$.
> 2. **Proporción conjunta.** $\hat{p} = (373 + 1595)/(4000 + 24\,000) = 1968/28\,000 = 0.0703$, la tasa global.
> 3. **Error estándar.** $\sqrt{0.0703 \cdot 0.9297 \cdot (1/4000 + 1/24\,000)} = 0.00437$.
> 4. **Estadístico.** $z = (0.0933 - 0.0665)/0.00437 = 6.14$.
> 5. **p-valor.** $2 \cdot P(Z \ge 6.14) \approx 8 \cdot 10^{-10}$.
> 6. **Decisión.** Rechazamos $H_0$. La diferencia está a más de 6 errores estándar de cero: los lunes la tasa de compra es mayor que el resto de la semana.
>
> Para saber **cuánto** mayor, el intervalo de confianza de la diferencia usa el error estándar sin juntar los grupos, porque ya no suponemos que las tasas son iguales: $\sqrt{0.0933 \cdot 0.9067/4000 + 0.0665 \cdot 0.9335/24\,000} = 0.00487$. El intervalo al 95 % es $0.0268 \pm 1.96 \cdot 0.00487 = [0.017,\ 0.036]$: los lunes compran entre 1.7 y 3.6 clientes más de cada 100. Con 500 emails, son entre 9 y 18 ventas más cada lunes, y eso tiene consecuencias para el almacén.

Este resultado confirma lo que la práctica del tema anterior sugería con intervalos de confianza separados para cada grupo. El contraste de dos proporciones es la forma directa de hacer esa comparación. Comparar si dos intervalos se solapan no es equivalente: dos intervalos al 95 % pueden solaparse un poco y la diferencia ser significativa, porque el error estándar de la diferencia es menor que la suma de los dos errores estándar.

## Dos medias con muestras independientes

Marketing prueba una versión nueva del email que incluye recomendaciones de productos. Cree que hará que los clientes gasten más por pedido. Durante dos semanas, cada cliente recibe al azar la versión actual o la nueva, y se anota el importe de cada pedido:

| | Pedidos | Importe medio | Desviación típica |
|---|---:|---:|---:|
| Versión actual | 40 | 48.2 € | 14.5 € |
| Versión nueva | 45 | 54.9 € | 16.8 € |

Los dos grupos son **independientes**: son clientes distintos, asignados al azar, y lo que haga uno no influye en el otro.

> 📘 **Definición: contraste t para dos muestras independientes**
>
> Para contrastar $H_0: \mu_1 = \mu_2$ con dos muestras independientes de tamaños $n_1$ y $n_2$, medias $\bar{x}_1$ y $\bar{x}_2$ y desviaciones típicas $s_1$ y $s_2$:
>
> $$t = \frac{\bar{x}_1 - \bar{x}_2}{s_p\sqrt{\dfrac{1}{n_1} + \dfrac{1}{n_2}}} \qquad \text{con } s_p = \sqrt{\frac{(n_1 - 1)s_1^2 + (n_2 - 1)s_2^2}{n_1 + n_2 - 2}}$$
>
> $s_p$ es la **desviación típica conjunta**: una media de las dos desviaciones típicas, ponderada por el tamaño de cada grupo. Si $H_0$ es cierta, $t$ sigue una t de Student con $n_1 + n_2 - 2$ grados de libertad.
>
> El contraste supone que las dos poblaciones tienen una variabilidad parecida. Si las desviaciones típicas son muy distintas (una más del doble que la otra), se usa la **versión de Welch**, que no lo supone y calcula el error estándar como $\sqrt{s_1^2/n_1 + s_2^2/n_2}$. En Python es `stats.ttest_ind(..., equal_var=False)`. Con grupos de tamaño parecido, las dos versiones dan casi lo mismo.

> ✏️ **Ejemplo resuelto: ¿gastan más con la versión nueva?**
>
> 1. **Hipótesis.** Marketing quiere demostrar que la versión nueva **aumenta** el importe, y lo decidió antes de lanzar la prueba: $H_0: \mu_{\text{nueva}} = \mu_{\text{actual}}$ frente a $H_1: \mu_{\text{nueva}} > \mu_{\text{actual}}$. Unilateral, con $\alpha = 0.05$.
> 2. **Desviación típica conjunta.** $s_p = \sqrt{(39 \cdot 14.5^2 + 44 \cdot 16.8^2)/83} = 15.76$ €.
> 3. **Error estándar.** $15.76 \cdot \sqrt{1/40 + 1/45} = 3.43$ €.
> 4. **Estadístico.** $t = (54.9 - 48.2)/3.43 = 1.96$, con $40 + 45 - 2 = 83$ grados de libertad.
> 5. **p-valor.** $P(t_{83} \ge 1.96) = 0.027$. El valor crítico es $t_{0.05,\,83} = 1.663$.
> 6. **Decisión.** $0.027 < 0.05$: rechazamos $H_0$. Hay evidencia de que la versión nueva aumenta el importe medio del pedido.
>
> Fíjate en lo ajustado del resultado. Si el contraste hubiera sido bilateral, el p-valor sería el doble, $0.054$, y no se rechazaría $H_0$. El intervalo de confianza al 95 % de la diferencia, $6.7 \pm 1.989 \cdot 3.43 = [-0.1,\ 13.5]$ €, va desde casi cero hasta 13.5 € más por pedido. La versión nueva probablemente funciona mejor, pero la prueba no dice si la mejora es de 1 € o de 13 €. Antes de cambiar todos los emails, convendría ampliar la prueba.

## Muestras pareadas

El almacén instala un nuevo sistema para localizar los productos en las estanterías. Para saber si reduce el tiempo de preparación de un pedido, se mide el tiempo medio de cada uno de los 10 empleados antes y después del cambio:

| Empleado | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
|---|---|---|---|---|---|---|---|---|---|---|
| Antes (min) | 12.1 | 8.4 | 15.3 | 10.2 | 9.8 | 13.7 | 11.5 | 7.9 | 14.2 | 10.8 |
| Después (min) | 10.9 | 7.9 | 13.7 | 9.8 | 8.9 | 12.6 | 11.2 | 7.2 | 12.8 | 11.0 |
| Diferencia | 1.2 | 0.5 | 1.6 | 0.4 | 0.9 | 1.1 | 0.3 | 0.7 | 1.4 | −0.2 |

Aquí los dos grupos **no son independientes**: son los mismos 10 empleados medidos dos veces. Un empleado rápido es rápido antes y después, y uno lento es lento antes y después. Los tiempos van de 8 a 15 minutos según el empleado, pero el cambio de cada uno es mucho más regular: casi todos ganan entre 0.3 y 1.6 minutos. La forma correcta de analizarlo es trabajar con la **diferencia** de cada empleado, que elimina las diferencias entre empleados.

> 📘 **Definición: contraste t para muestras pareadas**
>
> Cuando cada dato de un grupo está emparejado con uno del otro (la misma persona, tienda o día medidos dos veces, o parejas formadas a propósito), se calcula la diferencia $d_i$ de cada pareja y se hace un **contraste t para una media** sobre las diferencias. Para $H_0: \mu_d = 0$, con $n$ parejas, media de las diferencias $\bar{d}$ y desviación típica de las diferencias $s_d$:
>
> $$t = \frac{\bar{d}}{s_d/\sqrt{n}} \qquad \text{que, si } H_0 \text{ es cierta, sigue una } t_{n-1}$$

> ✏️ **Ejemplo resuelto: ¿reduce el nuevo sistema el tiempo de preparación?**
>
> 1. **Hipótesis.** Con $d = $ antes $-$ después, $H_0: \mu_d = 0$ frente a $H_1: \mu_d > 0$ (el sistema reduce el tiempo). $\alpha = 0.05$.
> 2. **Diferencias.** $\bar{d} = 0.79$ min y $s_d = 0.555$ min.
> 3. **Estadístico.** $\text{SE} = 0.555/\sqrt{10} = 0.175$ y $t = 0.79/0.175 = 4.50$, con 9 grados de libertad.
> 4. **p-valor.** $P(t_9 \ge 4.50) = 0.0007$.
> 5. **Decisión.** Rechazamos $H_0$: el nuevo sistema reduce el tiempo de preparación. El intervalo al 95 % para la reducción media es $0.79 \pm 2.262 \cdot 0.175 = [0.39,\ 1.19]$ minutos por pedido.

¿Qué habría pasado si hubiéramos tratado los datos como dos muestras independientes? Las medias son 11.39 y 10.60 minutos, con desviaciones típicas de 2.46 y 2.15. El error estándar de la diferencia sería $s_p\sqrt{1/10 + 1/10} = 1.03$ minutos, casi seis veces mayor que el del contraste pareado, y el resultado, $t = 0.77$ con p-valor 0.23: **no** se rechazaría $H_0$. La diferencia de medias es la misma, 0.79 minutos, pero el contraste independiente la compara con la enorme variación entre empleados, que no tiene nada que ver con el sistema. El contraste pareado la compara solo con la variación del cambio dentro de cada empleado.

Por eso, cuando se puede, conviene **diseñar** los estudios con parejas: medir a los mismos clientes antes y después, comparar las mismas tiendas, o enviar las dos versiones de un email el mismo día. Con el mismo número de datos se detectan diferencias mucho más pequeñas.

## Qué contraste usar

| Comparación | ¿Grupos independientes? | Contraste | En Python |
|---|---|---|---|
| dos proporciones | sí | Z para dos proporciones | a mano (ver abajo) |
| dos medias | sí | t para dos muestras independientes | `stats.ttest_ind(a, b)` |
| dos medias, variabilidad muy distinta | sí | t de Welch | `stats.ttest_ind(a, b, equal_var=False)` |
| dos medias de los mismos individuos | no, emparejados | t pareado | `stats.ttest_rel(despues, antes)` |

La pregunta clave es si cada dato de un grupo tiene una **pareja natural** en el otro. Si la tiene, el contraste es pareado. Si los grupos están formados por individuos distintos, sin relación entre ellos, son independientes. Un test A/B con clientes asignados al azar a cada versión da grupos independientes.

> ⛔ **Errores frecuentes**
>
> - **Analizar datos pareados como independientes.** Se pierde la ventaja del emparejamiento y se puede no detectar una diferencia clara, como en el ejemplo del almacén.
> - **Analizar como pareados datos que no lo son**, por ejemplo emparejando la fila 1 de un grupo con la fila 1 del otro cuando son clientes distintos. El resultado no tiene sentido.
> - **Decidir si hay diferencia mirando si los intervalos de cada grupo se solapan.** Hay que hacer el contraste, o el intervalo, de la diferencia.
> - **Olvidar el orden de la resta** al plantear un contraste unilateral con `ttest_ind` o `ttest_rel`: `alternative="greater"` significa que el primer argumento tiene media mayor que el segundo.
> - **Comparar grupos que no se formaron al azar** y atribuir la diferencia a la variable estudiada. Si los clientes eligen qué versión ven, la diferencia puede deberse al tipo de cliente.

## En Python

```python
import numpy as np
import pandas as pd
from scipy import stats

# 1. Dos proporciones: lunes frente al resto de días
ventas = pd.read_csv("https://raw.githubusercontent.com/ami232/nebrija-mpan/main/data/ventas_campana.csv")
es_lunes = ventas.dia_semana == "lunes"
x1, n1 = ventas.ventas[es_lunes].sum(), ventas.emails[es_lunes].sum()
x2, n2 = ventas.ventas[~es_lunes].sum(), ventas.emails[~es_lunes].sum()

def contraste_dos_proporciones(x1, n1, x2, n2):
    """z y p-valor bilateral de H0: p1 = p2."""
    p1, p2 = x1 / n1, x2 / n2
    p = (x1 + x2) / (n1 + n2)                                  # proporción conjunta
    z = (p1 - p2) / np.sqrt(p * (1 - p) * (1 / n1 + 1 / n2))
    return z, 2 * stats.norm.sf(abs(z))

z, p_valor = contraste_dos_proporciones(x1, n1, x2, n2)
print(f"lunes {x1 / n1:.5f}, resto {x2 / n2:.5f}: z = {z:.2f}, p-valor = {p_valor:.1e}")
```

```text
lunes 0.09325, resto 0.06646: z = 6.14, p-valor = 8.4e-10
```

```python
# Intervalo de confianza al 95 % de la diferencia (error estándar sin juntar los grupos)
dif = x1 / n1 - x2 / n2
se = np.sqrt((x1 / n1) * (1 - x1 / n1) / n1 + (x2 / n2) * (1 - x2 / n2) / n2)
print("IC 95 % de la diferencia:", np.round([dif - 1.96 * se, dif + 1.96 * se], 4))
```

```text
IC 95 % de la diferencia: [0.0172 0.0363]
```

```python
# 2. Dos medias independientes a partir de los resúmenes
res = stats.ttest_ind_from_stats(mean1=54.9, std1=16.8, nobs1=45,
                                 mean2=48.2, std2=14.5, nobs2=40, alternative="greater")
print(f"importe: t = {res.statistic:.2f}, p-valor = {res.pvalue:.3f}")
```

```text
importe: t = 1.96, p-valor = 0.027
```

```python
# 3. Muestras pareadas: el mismo empleado antes y después
antes = np.array([12.1, 8.4, 15.3, 10.2, 9.8, 13.7, 11.5, 7.9, 14.2, 10.8])
despues = np.array([10.9, 7.9, 13.7, 9.8, 8.9, 12.6, 11.2, 7.2, 12.8, 11.0])
res = stats.ttest_rel(antes, despues, alternative="greater")      # H1: antes > después
print(f"pareado:       t = {res.statistic:.2f}, p-valor = {res.pvalue:.4f}")
```

```text
pareado:       t = 4.50, p-valor = 0.0007
```

```python
res = stats.ttest_ind(antes, despues, alternative="greater")      # el análisis incorrecto
print(f"independiente: t = {res.statistic:.2f}, p-valor = {res.pvalue:.2f}")
```

```text
independiente: t = 0.77, p-valor = 0.23
```

Si tienes los datos individuales en lugar de los resúmenes, `stats.ttest_ind(a, b)` hace el mismo contraste que `ttest_ind_from_stats`.

> ✅ **Lo esencial**
>
> - Para comparar dos grupos, $H_0$ dice que **no hay diferencia**, y el estadístico es la diferencia observada dividida entre su error estándar.
> - **Dos proporciones:** contraste Z con la proporción conjunta. Para el intervalo de la diferencia se usa el error estándar sin juntar los grupos.
> - **Dos medias independientes:** contraste t con la desviación típica conjunta y $n_1 + n_2 - 2$ grados de libertad, o la versión de Welch si la variabilidad de los grupos es muy distinta.
> - **Muestras pareadas:** se calculan las diferencias de cada pareja y se hace un contraste t de una media sobre ellas. Elimina la variación entre individuos y detecta diferencias mucho más pequeñas.
> - Además del p-valor, da siempre el **intervalo de confianza de la diferencia**: dice cuánto difieren los grupos.

## Ejercicios

**1.** Una tienda envía dos versiones de su newsletter a 800 suscriptores cada una, asignados al azar. La versión A consigue 60 clics y la B, 84. Contrasta con $\alpha = 0.05$ si las dos versiones tienen la misma tasa de clics, y da el intervalo de confianza al 95 % de la diferencia.

<details>
<summary>✅ <b>Solución</b></summary>

$\hat{p}_A = 0.075$, $\hat{p}_B = 0.105$ y la proporción conjunta es $\hat{p} = 144/1600 = 0.09$.

$\text{SE} = \sqrt{0.09 \cdot 0.91 \cdot (1/800 + 1/800)} = 0.0143$ y $z = (0.105 - 0.075)/0.0143 = 2.10$. El p-valor bilateral es $2 \cdot P(Z \ge 2.10) = 0.036$: **rechazamos** $H_0$, la versión B consigue más clics.

Intervalo: $\text{SE} = \sqrt{0.075 \cdot 0.925/800 + 0.105 \cdot 0.895/800} = 0.0143$ y $0.030 \pm 1.96 \cdot 0.0143 = [0.002,\ 0.058]$. La mejora está entre 0.2 y 5.8 puntos: casi seguro positiva, pero de tamaño muy incierto.

</details>

**2.** Se comparan los tiempos de entrega de dos empresas de transporte con 30 envíos cada una. La empresa A tarda de media 26.1 horas ($s = 4.2$) y la B, 24.3 horas ($s = 3.9$). ¿Hay diferencia entre las dos empresas, con $\alpha = 0.05$?

<details>
<summary>✅ <b>Solución</b></summary>

Muestras independientes, contraste bilateral. $s_p = \sqrt{(29 \cdot 4.2^2 + 29 \cdot 3.9^2)/58} = 4.05$, $\text{SE} = 4.05 \cdot \sqrt{2/30} = 1.05$ y $t = (26.1 - 24.3)/1.05 = 1.72$, con 58 grados de libertad.

El p-valor es 0.091 (`stats.ttest_ind_from_stats(26.1, 4.2, 30, 24.3, 3.9, 30)`): **no rechazamos** $H_0$. Con 30 envíos por empresa, una diferencia de 1.8 horas puede deberse al azar.

</details>

**3.** Seis tiendas de una cadena cambian la colocación de los productos en el escaparate. Las ventas semanales (miles de euros) antes del cambio fueron 41, 35, 52, 47, 38 y 44, y después, en el mismo orden, 45, 38, 55, 47, 43 y 46. ¿Ha aumentado la venta media, con $\alpha = 0.05$?

<details>
<summary>✅ <b>Solución</b></summary>

Son las mismas tiendas antes y después: contraste **pareado**. Las diferencias (después − antes) son 4, 3, 3, 0, 5 y 2, con $\bar{d} = 2.83$ y $s_d = 1.72$.

$H_0: \mu_d = 0$ frente a $H_1: \mu_d > 0$. $\text{SE} = 1.72/\sqrt{6} = 0.70$ y $t = 2.83/0.70 = 4.03$, con 5 grados de libertad. El p-valor es $P(t_5 \ge 4.03) = 0.005$: **rechazamos** $H_0$. El cambio aumentó las ventas, unos 2 800 € por tienda y semana de media.

Con solo 6 tiendas el resultado es claro porque todas mejoran o se quedan igual. Aun así, falta un grupo de control: si en esas semanas las ventas subieron en toda la cadena, parte de la mejora no se debería al escaparate.

</details>

**4.** Indica qué contraste usarías. (a) Se mide la satisfacción de 50 clientes antes y después de un cambio en la atención telefónica. (b) Se compara la proporción de clientes que repiten compra en Madrid y en Barcelona. (c) Se compara el gasto medio de 200 clientes que vieron un anuncio con el de otros 200 que no lo vieron. (d) Se compara el precio de 30 productos en dos supermercados.

<details>
<summary>✅ <b>Solución</b></summary>

(a) **t pareado**: los mismos clientes medidos dos veces.

(b) **Z para dos proporciones**: dos grupos de clientes distintos.

(c) **t para dos muestras independientes**: son clientes distintos. Si las desviaciones típicas fueran muy distintas, la versión de Welch.

(d) **t pareado**: cada producto tiene un precio en cada supermercado, así que cada producto es una pareja. Analizarlo como independiente mezclaría la diferencia entre supermercados con la enorme diferencia de precio entre productos.

</details>

**5.** *(Más difícil)* La comparación entre lunes y el resto de días también puede hacerse con las ventas diarias: 8 lunes con media 46.6 y desviación típica 6.59, y 48 días con media 33.2 y desviación típica 6.24. Haz el contraste t para dos muestras independientes y compara el resultado con el contraste de dos proporciones. ¿Por qué tiene sentido que el estadístico sea algo menor?

<details>
<summary>✅ <b>Solución</b></summary>

$s_p = \sqrt{(7 \cdot 6.59^2 + 47 \cdot 6.24^2)/54} = 6.29$ y $\text{SE} = 6.29 \cdot \sqrt{1/8 + 1/48} = 2.40$. La diferencia es $46.6 - 33.2 = 13.4$ ventas al día, así que $t = 13.4/2.40 = 5.58$ con 54 grados de libertad, y el p-valor es $8 \cdot 10^{-7}$. Se rechaza $H_0$, igual que con las proporciones.

El estadístico es algo menor que el del contraste de proporciones (6.14) por la misma razón que en la [página 4.2](una-muestra.md#otra-forma-de-contrastar-si-se-cubren-costes): el contraste t usa la variación entre días que se ha observado, y el de proporciones, la que predice el modelo binomial, que es algo menor. Además, el contraste t solo tiene 8 lunes, y no aprovecha que cada lunes son 500 clientes. Cuando las dos formas coinciden, la conclusión es sólida.

</details>

[← Contrastes para una muestra](una-muestra.md) [Siguiente: Errores y potencia →](errores-potencia.md)
