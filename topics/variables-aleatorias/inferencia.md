# 5. Inferencia: de la muestra a la población

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/variables-aleatorias/inferencia/>

> ℹ️ **Antes de empezar**
>
> Esta página usa la **distribución normal** y la tipificación $z = (x - \mu)/\sigma$ ([página 3](distribuciones.md)), y la distinción entre **población y muestra**, con la media $\bar{x}$, la desviación típica $s$ y la proporción $\hat{p}$ muestrales ([página 4](estadisticos.md#de-la-distribución-a-los-datos-población-y-muestra)).

## El problema

Una marca encuesta a 400 clientes y 92 dicen que la recomendarían: un **23 %**. El director de marketing quiere poner en el informe anual que "el 23 % de nuestros clientes nos recomendaría". Pero la empresa tiene cientos de miles de clientes y solo ha preguntado a 400. Si hubiera encuestado a **otros** 400, elegidos igual de al azar, el resultado habría sido algo distinto: quizá un 21 %, quizá un 26 %.

¿Cuánto puede variar? ¿El verdadero porcentaje podría ser un 30 %? ¿Y un 15 %? La **inferencia estadística** responde a estas preguntas. Con **una sola muestra**, dice qué valores de la población son plausibles y con cuánta seguridad. Es la idea que hay detrás de cualquier encuesta, test A/B o estudio de mercado, y la base de los [contrastes de hipótesis](../contraste-hipotesis/index.md) del próximo tema.

## Una estimación es una variable aleatoria

La clave está en un cambio de perspectiva. La proporción muestral $\hat{p}$ no es un número fijo: **depende de qué clientes hayan caído en la muestra**. Si repitiéramos la encuesta muchas veces, cada repetición daría un $\hat{p}$ distinto. Por tanto, $\hat{p}$ es una **variable aleatoria**, y tiene su propia distribución, que se llama **distribución muestral**.

Podemos ver cómo es esa distribución con un experimento mental, o con un ordenador. Supón que en realidad el **25 %** de todos los clientes recomendaría la marca. Si simulamos 100 000 encuestas de 400 clientes cada una, los $\hat{p}$ que salen:

- **se centran en 0.25**: su media es 0.2499. La encuesta no se equivoca sistemáticamente ni hacia arriba ni hacia abajo;
- **se dispersan con una desviación típica de 0.0216**: el 95 % central de las encuestas cae entre **0.2075 y 0.2925**;
- **tienen forma de campana**, aunque cada respuesta individual sea un 0 o un 1.

Así que una encuesta concreta con un 23 % es perfectamente compatible con un 25 % real: el 19 % de las encuestas simuladas sale igual o más bajo. En la práctica no conocemos el 25 %, pero estas tres propiedades son generales y nos permiten razonar al revés: de la muestra a la población.

## Error estándar

Las tres propiedades anteriores se pueden calcular sin simular. Recuerda las [propiedades de la media y la varianza](estadisticos.md#combinar-variables-propiedades-de-la-media-y-la-varianza). Si $X_1, \ldots, X_n$ son observaciones independientes con media $\mu$ y varianza $\sigma^2$, la media muestral $\bar{X} = \frac{1}{n}\sum X_i$ cumple:

$$\mathbb{E}[\bar{X}] = \frac{1}{n} \cdot n\mu = \mu \qquad\qquad \mathbb{V}[\bar{X}] = \frac{1}{n^2} \cdot n\sigma^2 = \frac{\sigma^2}{n}$$

> 📘 **Definición: error estándar**
>
> El **error estándar** (*standard error*, SE) de un estimador es su desviación típica. Para la media muestral:
>
> $$\text{SE}(\bar{X}) = \frac{\sigma}{\sqrt{n}} \qquad\qquad \text{y, como no conocemos } \sigma, \text{ lo estimamos con } \frac{s}{\sqrt{n}}$$
>
> Para una proporción, que es la media de ceros y unos con $\sigma^2 = p(1 - p)$:
>
> $$\text{SE}(\hat{p}) = \sqrt{\frac{p(1 - p)}{n}} \;\approx\; \sqrt{\frac{\hat{p}(1 - \hat{p})}{n}}$$

Con $p = 0.25$ y $n = 400$, la fórmula da $\sqrt{0.25 \cdot 0.75/400} = 0.0217$, prácticamente lo que obtuvimos simulando.

Este resultado tiene dos consecuencias importantes.

**La media muestral es insesgada.** $\mathbb{E}[\bar{X}] = \mu$: en promedio, la estimación acierta. Una muestra concreta se desvía, pero no hay un error sistemático, siempre que la muestra sea **aleatoria**. Si solo encuestas a los clientes que responden a un email, la fórmula sigue dando un número, pero estimará otra cosa (veremos este problema en el [tema de sesgos](../sesgos/index.md)).

**La precisión crece con la raíz de $n$, no con $n$.** Para reducir el error a la mitad hay que **cuadruplicar** la muestra, y para dividirlo entre 10 hay que multiplicarla por 100. Por eso las encuestas electorales rara vez superan unos pocos miles de personas: a partir de ahí, cada punto de precisión cuesta muchísimo.

> ⚠️ **Desviación típica frente a error estándar**
>
> Son dos cosas distintas que se confunden a menudo:
>
> - $\sigma$ (o $s$) mide cuánto varían los **individuos**: un pedido de otro.
> - $\sigma/\sqrt{n}$ mide cuánto variaría la **estimación**: la media de una muestra de otra.
>
> Con más datos, $s$ no disminuye (los clientes no se vuelven más parecidos), pero el error estándar sí.

## El teorema del límite central

Ya sabemos la media y la desviación típica de $\bar{X}$. Para calcular probabilidades nos falta su **forma**. La respuesta es uno de los resultados más importantes de la estadística.

> 📘 **Teorema del límite central (TLC)**
>
> Si $X_1, \ldots, X_n$ son **independientes**, con la **misma distribución** de media $\mu$ y varianza $\sigma^2$ **finita**, entonces, para $n$ suficientemente grande:
>
> $$\bar{X} \approx N\!\left(\mu,\ \frac{\sigma^2}{n}\right) \qquad\Longleftrightarrow\qquad Z = \frac{\bar{X} - \mu}{\sigma / \sqrt{n}} \approx N(0, 1)$$
>
> **sea cual sea la distribución de las $X_i$.**

Lo sorprendente es la última frase. Los tiempos de espera son exponenciales, muy asimétricos. Los clics son ceros y unos. Los conteos de clientes son Poisson. Y aun así, la **media** de muchos de ellos tiene forma de campana. Al promediar, los valores altos y bajos se compensan, y lo que queda son muchas pequeñas fluctuaciones sumadas: la situación en la que aparece la normal. Por eso la normal es tan omnipresente, y por eso podemos hacer inferencia sobre medias **sin saber cuál es la distribución de los datos**.

Comprueba en la demo cómo la distribución de la media pasa de ser la población original ($n = 1$) a una campana al aumentar $n$:

▶️ [Demo interactiva: tlc](https://alberpro.com/nebrija-mpan/topics/variables-aleatorias/demo_tlc.html) (en la web)

**¿Qué es «$n$ suficientemente grande»?** Depende de lo asimétrica que sea la población. Con poblaciones aproximadamente simétricas, $n \approx 10$ ya basta. La regla práctica habitual es $n \ge 30$. Para proporciones se pide que haya bastantes éxitos y fracasos: $n\hat{p} \ge 5$ y $n(1 - \hat{p}) \ge 5$. Si la población tiene una cola muy larga, como los importes con unos pocos clientes enormes, puede hacer falta bastante más.

> ⚠️ **Lo que el TLC **no** dice**
>
> - **No dice que los datos se vuelvan normales.** Los tiempos de espera siguen siendo exponenciales tengas 30 o 30 000. Lo que se vuelve normal es la **media**.
> - **No arregla una muestra sesgada.** Si las observaciones no son independientes o no representan a la población, el TLC no ayuda.

> ✏️ **Ejemplo resuelto: el TLC en acción**
>
> El tiempo entre autobuses es exponencial con media 6 minutos, así que $\sigma = 6$ también. Para **un** intervalo concreto, $P(X > 12) = e^{-2} \approx 0.135$: esperar el doble de la media no es raro.
>
> ¿Y si medimos 50 intervalos? La media $\bar{X}$ es aproximadamente $N(6,\ 6^2/50)$, con error estándar $6/\sqrt{50} \approx 0.85$ min. La probabilidad de que la **media** de los 50 supere los 7 minutos es
>
> $$P(\bar{X} > 7) = P\left(Z > \frac{7 - 6}{0.85}\right) = P(Z > 1.18) \approx 0.119$$
>
> El cálculo exacto, que en este caso se puede hacer, da 0.122: la aproximación normal es muy buena incluso partiendo de una distribución tan asimétrica.

## Intervalos de confianza

### De dónde sale la fórmula

Ahora podemos dar la vuelta al razonamiento. Por el TLC, la media muestral tipificada es aproximadamente normal estándar. Y sabemos que una normal estándar cae entre $-1.96$ y $1.96$ con probabilidad 0.95:

$$P\left(-1.96 \le \frac{\bar{X} - \mu}{\sigma/\sqrt{n}} \le 1.96\right) \approx 0.95$$

Multiplicamos los tres miembros por $\sigma/\sqrt{n}$ y despejamos $\mu$:

$$P\left(\bar{X} - 1.96\,\frac{\sigma}{\sqrt{n}} \;\le\; \mu \;\le\; \bar{X} + 1.96\,\frac{\sigma}{\sqrt{n}}\right) \approx 0.95$$

Es decir, el intervalo $\bar{X} \pm 1.96\,\sigma/\sqrt{n}$ **contiene a $\mu$ en el 95 % de las muestras**. En la práctica sustituimos $\sigma$ por su estimación $s$. El 1.96 cambia si queremos otro nivel de confianza.

> 📘 **Definición: intervalo de confianza**
>
> Un **intervalo de confianza** al $100(1 - \alpha)\,\%$ para un parámetro es un intervalo, calculado con la muestra, que contiene el valor real del parámetro en una proporción $1 - \alpha$ de las muestras posibles. Para muestras grandes:
>
> $$\text{media: } \ \bar{x} \pm z_{\alpha/2}\,\frac{s}{\sqrt{n}} \qquad\qquad \text{proporción: } \ \hat{p} \pm z_{\alpha/2}\sqrt{\frac{\hat{p}(1 - \hat{p})}{n}}$$
>
> $z_{\alpha/2}$ es el valor que deja una probabilidad $\alpha/2$ en cada cola de la normal estándar: `stats.norm.ppf(1 - alpha/2)`. El término que se suma y resta, $z_{\alpha/2} \cdot \text{SE}$, se llama **margen de error**.

| Confianza | $\alpha$ | $z_{\alpha/2}$ |
|---|---|---|
| 90 % | 0.10 | 1.645 |
| 95 % | 0.05 | 1.960 |
| 99 % | 0.01 | 2.576 |

> ✏️ **Ejemplo resuelto: el porcentaje que recomendaría la marca**
>
> Con $n = 400$ y 92 respuestas positivas:
>
> 1. **Estimación puntual:** $\hat{p} = 92/400 = 0.23$.
> 2. **Condiciones:** $n\hat{p} = 92$ y $n(1 - \hat{p}) = 308$, ambos muy por encima de 5, así que la aproximación normal es válida.
> 3. **Error estándar:** $\text{SE} = \sqrt{0.23 \cdot 0.77/400} = 0.0210$.
> 4. **Margen de error al 95 %:** $1.96 \cdot 0.0210 = 0.041$.
> 5. **Intervalo:** $0.23 \pm 0.041 = [0.189,\ 0.271]$.
>
> Lo que el informe debería decir es: "entre el 19 % y el 27 % de nuestros clientes nos recomendaría (95 % de confianza)". Un 30 % no es plausible con estos datos, y un 25 % sí.

> ✏️ **Ejemplo resuelto: tiempo medio en la web**
>
> En 64 visitas, el tiempo medio en la página es $\bar{x} = 3.2$ min, con $s = 1.6$ min.
>
> 1. $\text{SE} = 1.6/\sqrt{64} = 0.2$ min.
> 2. Margen de error al 95 %: $1.96 \cdot 0.2 = 0.39$ min.
> 3. Intervalo: $3.2 \pm 0.39 = [2.81,\ 3.59]$ minutos.
>
> Observa que la desviación típica de 1.6 min dice que **las visitas individuales** varían mucho (de 1 a 6 minutos, aproximadamente). En cambio, el **tiempo medio** está acotado en un intervalo estrecho.

### Qué controla la anchura

El margen de error $z_{\alpha/2} \cdot s/\sqrt{n}$ depende de tres cosas:

- **El nivel de confianza.** Para estar más seguros hace falta un intervalo más ancho. Un intervalo al 100 % sería "entre $-\infty$ e $\infty$": seguro, pero inútil.
- **El tamaño de muestra.** El margen va como $1/\sqrt{n}$.
- **La variabilidad de los datos** ($s$). No la controlamos, pero a veces se reduce segmentando: medir por separado grupos homogéneos.

Margen de error para el ejemplo del tiempo en la web ($s = 1.6$ min):

| $n$ | 90 % | 95 % | 99 % |
|---:|---:|---:|---:|
| 25 | ± 0.53 | ± 0.63 | ± 0.82 |
| 100 | ± 0.26 | ± 0.31 | ± 0.41 |
| 400 | ± 0.13 | ± 0.16 | ± 0.21 |
| 1600 | ± 0.07 | ± 0.08 | ± 0.10 |

Cada vez que $n$ se multiplica por 4, el margen se reduce a la mitad.

### Planificar el tamaño de muestra

La misma fórmula sirve **antes** de recoger datos, para decidir cuántos hacen falta. Si quieres un margen de error $E$, despeja $n$:

$$n = \left(\frac{z_{\alpha/2}\, \sigma}{E}\right)^2 \quad \text{(media)} \qquad\qquad n = \frac{z_{\alpha/2}^2\; p(1 - p)}{E^2} \quad \text{(proporción)}$$

Hacen falta una estimación previa de $\sigma$ o de $p$, de un estudio piloto o de datos históricos. Redondea siempre **hacia arriba**.

> ✏️ **Ejemplo resuelto: ¿cuántos clientes encuestar?**
>
> Marketing quiere repetir la encuesta con un margen de error de **±3 puntos** al 95 %.
>
> - **Sin información previa**, se usa el peor caso, $p = 0.5$, que maximiza $p(1 - p)$: $n = 1.96^2 \cdot 0.25 / 0.03^2 = 1067.1$, así que hacen falta **1068 clientes**. De ahí sale el "unas 1000 personas" típico de las encuestas.
> - **Usando el 23 % anterior:** $n = 1.96^2 \cdot 0.23 \cdot 0.77 / 0.03^2 = 755.9$, así que bastan **756 clientes**.
>
> Para el tiempo en la web, con $s \approx 1.6$ y un margen de ±0.2 min: $n = (1.96 \cdot 1.6/0.2)^2 = 245.9$, es decir, **246 visitas**.

### Cómo interpretar un intervalo de confianza

> ⚠️ **Lo que significa el 95 %**
>
> El parámetro real $\mu$ es un número fijo, aunque desconocido. Lo que cambia de una muestra a otra es el **intervalo**. El 95 % es una propiedad del **procedimiento**: de cada 100 intervalos construidos así, unos 95 contienen a $\mu$.
>
> | ✅ Correcto | ❌ Incorrecto |
> |---|---|
> | "Con un 95 % de confianza, el tiempo medio está entre 2.81 y 3.59 min." | "Hay un 95 % de probabilidad de que $\mu$ esté entre 2.81 y 3.59." (Una vez calculado, el intervalo contiene a $\mu$ o no; no hay nada aleatorio.) |
> | "El método acierta en el 95 % de las muestras." | "El 95 % de las visitas dura entre 2.81 y 3.59 min." (El intervalo es para la **media**, no para los individuos.) |
> | "Los valores fuera del intervalo son poco compatibles con los datos." | "Si repito el estudio, el 95 % de las veces $\bar{x}$ caerá en [2.81, 3.59]." |

En la demo, cada línea es el intervalo de una muestra distinta. Las rojas no contienen el valor real. Cambia el nivel de confianza y el tamaño de muestra y observa qué pasa con la anchura y con el número de fallos:

▶️ [Demo interactiva: intervalos](https://alberpro.com/nebrija-mpan/topics/variables-aleatorias/demo_intervalos.html) (en la web)

### Hacia los contrastes de hipótesis y la t de Student

Los intervalos de confianza ya permiten juzgar afirmaciones. Si alguien dice que el 30 % de los clientes recomendaría la marca, y nuestro intervalo es $[0.189,\ 0.271]$, los datos **no apoyan** esa afirmación. El [contraste de hipótesis](../contraste-hipotesis/index.md) formaliza este razonamiento y añade el concepto de **p-valor**.

Allí veremos también qué hacer con **muestras pequeñas**. Si $n$ es pequeño y estimamos $\sigma$ con $s$, la tipificación ya no sigue exactamente una normal sino una **t de Student**, que da intervalos algo más anchos. Para $n \ge 30$ la diferencia es mínima.

> ⛔ **Errores frecuentes**
>
> - **Confundir $s$ con $s/\sqrt{n}$**, y dar un intervalo para la media usando la desviación típica de los datos.
> - **Pensar que el TLC hace normales los datos.** Solo la media (o la suma) es aproximadamente normal.
> - **Olvidar las condiciones:** muestra aleatoria e independiente, y $n$ suficiente, sobre todo para proporciones pequeñas.
> - **Interpretar el intervalo como un rango para los individuos** o como una probabilidad sobre el parámetro.
> - **Esperar que doblar la muestra divida el error a la mitad.** Hace falta cuadruplicarla.

## En Python

```python
import numpy as np
from scipy import stats

# 1. Intervalo para una proporción
n, exitos = 400, 92
p_hat = exitos / n
se = np.sqrt(p_hat * (1 - p_hat) / n)
z = stats.norm.ppf(0.975)                        # 1.96
print(p_hat - z * se, p_hat + z * se)            # (0.189, 0.271)
```

```text
0.18875917201531567 0.27124082798468435
```

```python
print(stats.norm.interval(0.95, loc=p_hat, scale=se))   # lo mismo en una línea
```

```text
(0.18875917201531567, 0.27124082798468435)
```

```python
# 2. Intervalo para una media a partir de los datos
x = np.array([3.1, 2.4, 4.0, 3.6, 2.9, 3.3, 5.1, 2.2, 3.8, 3.0, 2.7, 4.4])
se = x.std(ddof=1) / np.sqrt(len(x))
print(stats.norm.interval(0.95, loc=x.mean(), scale=se))
```

```text
(2.8960467604107465, 3.8539532395892544)
```

```python
# 3. Tamaño de muestra para un margen de error de ±3 puntos al 95 %
E = 0.03
print(np.ceil(z**2 * 0.5 * 0.5 / E**2))          # 1068
```

```text
1068.0
```

```python
# 4. Ver la distribución muestral simulando 10 000 encuestas
rng = np.random.default_rng(0)
p_hats = rng.binomial(n=400, p=0.25, size=10_000) / 400
print(p_hats.mean(), p_hats.std())               # ≈ 0.25 y ≈ 0.0217
```

```text
0.25000350000000005 0.02156258304911543
```

> ✅ **Lo esencial**
>
> - Toda estimación calculada con una muestra es una **variable aleatoria**: cambiaría con otra muestra. Su distribución es la **distribución muestral**.
> - La media muestral es **insesgada** y su **error estándar** es $\sigma/\sqrt{n}$: la precisión crece con la raíz del tamaño de muestra.
> - El **teorema del límite central** dice que la media de muchas observaciones independientes es aproximadamente normal, **sea cual sea** la distribución de los datos.
> - Un **intervalo de confianza** es la estimación ± $z_{\alpha/2}$ × error estándar. Más confianza da un intervalo más ancho, y más datos uno más estrecho.
> - El "95 %" describe al **procedimiento**: el 95 % de los intervalos así construidos contiene el valor real.
> - Despejando $n$ en el margen de error se **planifica** cuántos datos hacen falta antes de recogerlos.

## Ejercicios

**1.** Los importes de los pedidos tienen una desviación típica de 40 €. ¿Cuál es el error estándar de la media con 100 pedidos? ¿Y con 400? ¿Cuántos pedidos harían falta para un error estándar de 1 €?

<details>
<summary>✅ <b>Solución</b></summary>

$40/\sqrt{100} = 4$ € y $40/\sqrt{400} = 2$ €: cuadruplicar la muestra reduce el error a la mitad. Para un error estándar de 1 € hace falta $40/\sqrt{n} = 1$, es decir, $n = 1600$ pedidos.

</details>

**2.** En una muestra de 36 clientes, el gasto medio mensual es de 55 € con $s = 12$ €. Calcula el intervalo de confianza al 95 % para el gasto medio de todos los clientes e interprétalo.

<details>
<summary>✅ <b>Solución</b></summary>

$\text{SE} = 12/\sqrt{36} = 2$ €, y el margen de error es $1.96 \cdot 2 = 3.92$ €. El intervalo es $55 \pm 3.92 = [51.08,\ 58.92]$ €.

Interpretación: con un 95 % de confianza, el gasto medio de **todos** los clientes está entre 51 y 59 €. Eso **no** significa que el 95 % de los clientes gaste entre 51 y 59 €: los clientes individuales varían mucho más, con una desviación típica de 12 €.

</details>

**3.** Una cadena de restaurantes quiere estimar el tiempo medio de espera con un margen de error de ±1 minuto al 95 %. Un estudio piloto indica que la desviación típica es de unos 12 minutos. ¿Cuántas mesas debe observar?

<details>
<summary>✅ <b>Solución</b></summary>

$n = (1.96 \cdot 12 / 1)^2 = 553.2$, así que hacen falta **554 mesas**, redondeando hacia arriba.

</details>

**4.** Un partido quiere una encuesta con margen de error de ±2 puntos al **99 %** de confianza y no tiene ninguna estimación previa. ¿Cuántas personas necesita? Compáralo con el caso de ±3 puntos al 95 %.

<details>
<summary>✅ <b>Solución</b></summary>

Con $z_{0.005} = 2.576$ y el peor caso $p = 0.5$: $n = 2.576^2 \cdot 0.25/0.02^2 = 4146.8$, así que necesita **4147 personas**.

Con ±3 puntos al 95 % bastaban 1068. Pedir más confianza y más precisión a la vez multiplica el tamaño de muestra casi por 4.

</details>

**5.** En un test A/B, de 250 usuarios que vieron la nueva página, 30 compraron (un 12 %). La tasa de conversión histórica es del 15 %. (a) Calcula el intervalo al 95 % para la tasa de conversión de la nueva página. (b) ¿Puedes concluir que la nueva página convierte peor que la histórica?

<details>
<summary>✅ <b>Solución</b></summary>

(a) $\hat{p} = 0.12$ y $\text{SE} = \sqrt{0.12 \cdot 0.88/250} = 0.0206$. El intervalo es $0.12 \pm 1.96 \cdot 0.0206 = [0.080,\ 0.160]$. Las condiciones se cumplen: $n\hat{p} = 30$ y $n(1 - \hat{p}) = 220$.

(b) **No.** El 15 % histórico está **dentro** del intervalo, así que los datos son compatibles con que la nueva página convierta igual que la antigua. Una caída del 15 % al 12 % con 250 usuarios puede ser simple variabilidad muestral. Para detectar una diferencia de ese tamaño haría falta una muestra bastante mayor. Este tipo de pregunta se formaliza en el [contraste de hipótesis](../contraste-hipotesis/index.md).

</details>

**6.** *(Más difícil)* Un compañero dice: "He calculado un intervalo al 95 % y he obtenido [52, 58]. Entonces, si repito el estudio, el 95 % de las veces la media muestral caerá entre 52 y 58". ¿Es correcto? Si no, ¿qué afirmación correcta podría hacer sobre la repetición del estudio?

<details>
<summary>✅ <b>Solución</b></summary>

**No es correcto.** El intervalo [52, 58] está centrado en la $\bar{x}$ de *esta* muestra, que a su vez se ha desviado algo de $\mu$. En una repetición, la nueva $\bar{x}$ se repartirá alrededor de $\mu$, no alrededor de 55, así que la probabilidad de que caiga en [52, 58] es en general **menor** del 95 %, y depende de cuánto se desvió la primera muestra.

La afirmación correcta es sobre el procedimiento: "si repito el estudio muchas veces y en cada repetición calculo un intervalo al 95 %, alrededor del 95 % de **esos intervalos** contendrán la media real $\mu$". Es justo lo que muestra la demo de los 100 intervalos.

</details>

[← Estadísticos](estadisticos.md) [Siguiente: Práctica guiada →](practica.md)
