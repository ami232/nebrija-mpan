# 1. Muestras y estimación

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/inferencia/estimacion/>

> ℹ️ **Antes de empezar**
>
> Esta página usa la **esperanza** y la **varianza** de una variable aleatoria ([estadísticos](../variables-aleatorias/estadisticos.md)) y las distribuciones Poisson y normal ([distribuciones](../variables-aleatorias/distribuciones.md)).

En el tema anterior describimos variables aleatorias con su distribución y la resumimos con su media $\mu$ y su desviación típica $\sigma$. Pero hicimos trampa: casi siempre **dábamos** la distribución. "Entran de media 4 clientes por hora", "el 2 % de las tarjetas tiene un premio de 100 €". En la vida real nadie nos dice esos números. El director comercial no sabe cuánto gasta de media un cliente: tiene las facturas de los clientes de este mes. El call center no sabe cuál es su $\lambda$: tiene el registro de llamadas de unas cuantas horas.

Esta página da el primer paso para pasar de los datos a la distribución: distinguir lo que **queremos saber** (la población) de lo que **podemos calcular** (la muestra), y usar lo segundo para estimar lo primero.

## De la distribución a los datos: población y muestra

Lo que tenemos son **datos**: una **muestra** $x_1, \ldots, x_n$ de una **población** mucho mayor (los clientes encuestados frente a todos los clientes, los pedidos de este mes frente a todos los pedidos posibles). La distribución de la población, con su media $\mu$ y su varianza $\sigma^2$, existe pero **no la conocemos**. Con la muestra calculamos los **estadísticos muestrales**, que imitan a los de la población:

| | Población (desconocida) | Muestra (calculable) |
|---|---|---|
| Media | $\mu = \mathbb{E}[X]$ | $\bar{x} = \dfrac{1}{n}\displaystyle\sum_{i=1}^{n} x_i$ |
| Varianza | $\sigma^2 = \mathbb{V}[X]$ | $s^2 = \dfrac{1}{n - 1}\displaystyle\sum_{i=1}^{n} (x_i - \bar{x})^2$ |
| Desviación típica | $\sigma$ | $s = \sqrt{s^2}$ |
| Proporción | $p$ | $\hat{p} = $ proporción de unos en la muestra |
| Mediana, cuantiles | $q_p$ | los de la muestra ordenada |

La convención de notación es útil: **letras griegas** ($\mu$, $\sigma$, $p$) para los parámetros de la población y **letras latinas o con acento circunflejo** ($\bar{x}$, $s$, $\hat{p}$) para lo que calculamos con datos.

### ¿Por qué dividir entre n − 1?

La fórmula "natural" de la varianza muestral dividiría entre $n$. Pero la media muestral $\bar{x}$ se calcula con los mismos datos, y es justo el punto que **minimiza** la suma de distancias al cuadrado a esos datos. Por eso los datos siempre están, en conjunto, más cerca de $\bar{x}$ que de la verdadera $\mu$, y dividir entre $n$ **subestima** sistemáticamente $\sigma^2$.

Dividir entre $n - 1$ corrige exactamente ese sesgo. La idea intuitiva es que, una vez fijada $\bar{x}$, solo $n - 1$ desviaciones son libres: la última queda determinada, porque todas deben sumar cero. Por eso se habla de **$n - 1$ grados de libertad**.

El efecto se puede medir: con muestras de solo 3 datos de una normal con $\sigma^2 = 1$, la fórmula con $n$ da de media unos 0.67, es decir, $(n-1)/n$ del valor real. La fórmula con $n - 1$ da de media 1.00. Con muestras grandes, $n$ y $n - 1$ apenas se distinguen.

> ⚠️ **Cada herramienta usa una convención**
>
> - **pandas:** `serie.var()` y `serie.std()` dividen entre **n − 1** (`ddof=1`).
> - **NumPy:** `np.var(x)` y `np.std(x)` dividen entre **n** (`ddof=0`).
>
> Si dos personas obtienen varianzas distintas con los mismos datos, casi siempre es esto.

## Estimar parámetros: método de los momentos

Muchas veces suponemos que los datos siguen una distribución concreta (Poisson, normal…) pero no conocemos sus parámetros. El **método de los momentos** es la forma más directa de estimarlos: igualar la media (y si hace falta la varianza) teórica de la distribución a la de la muestra, y despejar.

> 📘 **Definición: método de los momentos**
>
> Si la distribución supuesta tiene media $\mathbb{E}[X]$ y varianza $\mathbb{V}[X]$ que dependen de sus parámetros, se eligen los parámetros que cumplen
>
> $$\mathbb{E}[X] = \bar{x}, \qquad \mathbb{V}[X] = s^2$$
>
> (la segunda ecuación solo si hay dos parámetros que estimar). Los parámetros estimados se marcan con un acento circunflejo ($\hat{\lambda}$, $\hat{\mu}$, $\hat{p}$).

El acento circunflejo recuerda que son **estimaciones con incertidumbre**, no los valores reales: con otra muestra saldrían otros números.

> ✏️ **Ejemplo resuelto: llamadas por hora (Poisson)**
>
> En 10 horas, un call center recibió 3, 5, 2, 4, 6, 3, 4, 1, 5 y 7 llamadas. Si suponemos una Poisson, como $\mathbb{E}[X] = \lambda$, estimamos $\hat{\lambda} = \bar{x} = 40/10 = 4$ llamadas por hora.
>
> **Comprobación de la suposición:** en una Poisson la media y la varianza coinciden. Aquí $s^2 = 3.33$, que es razonablemente cercano a 4 para solo 10 datos. Con el modelo estimado podemos responder preguntas nuevas: la probabilidad de recibir 8 o más llamadas en una hora es `stats.poisson(4).sf(7)` $\approx 0.051$, más o menos 1 hora de cada 20.

> ✏️ **Ejemplo resuelto: tiempos de entrega (normal)**
>
> Ocho entregas tardaron 46, 51, 49, 55, 44, 50, 47 y 53 horas. Si suponemos que son normales, estimamos $\hat{\mu} = \bar{x} = 49.4$ h y $\hat{\sigma} = s = 3.66$ h. La probabilidad estimada de que una entrega supere las 56 h es `stats.norm(49.4, 3.66).sf(56)` $\approx 0.035$.
>
> Con solo 8 datos, estas estimaciones son bastante inseguras. En la [siguiente página](intervalos.md) veremos cómo cuantificar esa incertidumbre.

Este método es exactamente lo que haremos en la [práctica guiada](practica.md): estimar la probabilidad $p$ de aceptar las cookies con la proporción observada $\hat{p}$, porque en una Bernoulli la media es $p$.

> ⛔ **Errores frecuentes**
>
> - **Confundir el estadístico con el parámetro.** $\bar{x} = 49.4$ es una estimación de $\mu$, no $\mu$. Con otra muestra saldría otro número.
> - **Mezclar $n$ y $n - 1$** entre herramientas sin darse cuenta.
> - **Estimar sin comprobar el modelo.** El método de los momentos siempre da un número, aunque la distribución supuesta no encaje. Compara lo que puedas (media y varianza en una Poisson, el histograma frente a la curva).

## En Python

```python
import numpy as np
import pandas as pd
from scipy import stats

# n frente a n − 1
x = [4, 8, 6, 2]
print(np.var(x), pd.Series(x).var())     # 5.0 (entre n) y 6.67 (entre n − 1)
```

```text
5.0 6.666666666666667
```

```python
# Estadísticos muestrales de datos reales
df = pd.read_csv("https://raw.githubusercontent.com/ami232/nebrija-mpan/main/data/telco_churn.csv")
factura = df["factura_mensual"]          # factura mensual de 5000 clientes de una operadora
print(round(factura.mean(), 2), round(factura.std(), 2))   # x̄ y s (n − 1)
```

```text
66.08 24.41
```

```python
# Método de los momentos para una Poisson
llamadas = pd.Series([3, 5, 2, 4, 6, 3, 4, 1, 5, 7])
lam_hat = llamadas.mean()                # 4.0
print(round(llamadas.var(), 2))          # 3.33: ¿parecido a la media?
```

```text
3.33
```

```python
print(round(stats.poisson(lam_hat).sf(7), 3))   # P(X ≥ 8) ≈ 0.051
```

```text
0.051
```

```python
# Método de los momentos para una normal
entregas = pd.Series([46, 51, 49, 55, 44, 50, 47, 53])
mu_hat, sigma_hat = entregas.mean(), entregas.std()
print(round(stats.norm(mu_hat, sigma_hat).sf(56), 3))   # P(entrega > 56 h) ≈ 0.035
```

```text
0.035
```

> ✅ **Lo esencial**
>
> - Los parámetros de la **población** ($\mu$, $\sigma$, $p$) son desconocidos. Con una **muestra** calculamos estadísticos ($\bar{x}$, $s$, $\hat{p}$) que los imitan.
> - La varianza muestral divide entre $n - 1$ para no subestimar $\sigma^2$. pandas lo hace por defecto, NumPy no.
> - El **método de los momentos** estima los parámetros de una distribución igualando su media (y su varianza) a las de la muestra.
> - Una estimación lleva un acento circunflejo porque tiene **incertidumbre**: con otra muestra saldría otra. Medir esa incertidumbre es el objetivo de la [siguiente página](intervalos.md).

## Ejercicios

**1.** Con la muestra 4, 8, 6, 2, calcula la varianza dividiendo entre $n$ y entre $n - 1$. ¿Qué daría `np.var` y qué daría `pd.Series.var`?

<details>
<summary>✅ <b>Solución</b></summary>

$\bar{x} = 5$. Las desviaciones son $-1, 3, 1, -3$, y sus cuadrados suman $1 + 9 + 1 + 9 = 20$. Dividiendo entre $n = 4$ sale **5**, que es lo que da `np.var(x)`. Dividiendo entre $n - 1 = 3$ sale **6.67**, que es lo que da `pd.Series(x).var()`. Con solo 4 datos la diferencia es notable.

</details>

**2.** Un compañero calcula la varianza de 5 datos con `np.var` y obtiene 8. ¿Qué habría dado `pd.Series.var` con los mismos datos?

<details>
<summary>✅ <b>Solución</b></summary>

`np.var` divide la suma de cuadrados entre $n = 5$, así que esa suma vale $8 \cdot 5 = 40$. pandas la divide entre $n - 1 = 4$: $40 / 4 = $ **10**. En general, $s^2_{n-1} = s^2_n \cdot n / (n - 1)$.

</details>

**3.** Una tienda registra las devoluciones diarias durante 8 días: 2, 0, 3, 1, 2, 4, 1 y 3. Suponiendo una Poisson, estima $\lambda$ por el método de los momentos. ¿Es razonable la suposición? Con el modelo estimado, ¿qué probabilidad hay de un día con 5 o más devoluciones?

<details>
<summary>✅ <b>Solución</b></summary>

$\bar{x} = 16/8 = 2$, así que $\hat{\lambda} = 2$ devoluciones al día.

En una Poisson la media y la varianza coinciden. Las desviaciones a la media son $0, -2, 1, -1, 0, 2, -1, 1$, sus cuadrados suman 12, y $s^2 = 12/7 \approx 1.71$: cercano a 2 para solo 8 días, así que la suposición es razonable.

$P(X \ge 5) = $ `stats.poisson(2).sf(4)` $\approx 0.053$: aproximadamente un día de cada 19.

</details>

**4.** *(Más difícil)* Los tiempos entre llegadas de clientes a una ventanilla (minutos) fueron 2, 5, 1, 8, 3, 6, 4 y 3. Suponemos una distribución exponencial de tasa $\lambda$, cuya media es $1/\lambda$ y cuya desviación típica también es $1/\lambda$. (a) Estima $\lambda$ por el método de los momentos. (b) Estima la probabilidad de esperar más de 10 minutos, sabiendo que en una exponencial $P(X > t) = e^{-\lambda t}$. (c) ¿Qué dice la desviación típica muestral sobre la suposición?

<details>
<summary>✅ <b>Solución</b></summary>

(a) Igualamos la media: $1/\lambda = \bar{x} = 32/8 = 4$, así que $\hat{\lambda} = 0.25$ llegadas por minuto.

(b) $P(X > 10) = e^{-0.25 \cdot 10} = e^{-2.5} \approx 0.082$. En Python, `stats.expon(scale=4).sf(10)`.

(c) En una exponencial la desviación típica es igual a la media, 4 minutos. La muestral es $s \approx 2.27$, bastante menor. Con 8 datos no es concluyente, pero es una señal para vigilar: si se confirma con más datos, la exponencial exageraría las esperas largas y la probabilidad del apartado (b) estaría sobrestimada.

</details>

[← Volver al tema](index.md) [Siguiente: Intervalos de confianza →](intervalos.md)
