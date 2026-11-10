# 4.4 Errores, potencia y buen uso de los contrastes

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/04-contraste-hipotesis/errores-potencia/>

> ℹ️ **Antes de empezar**
>
> Esta página usa el contraste de la cifra de la agencia de la [página 4.1](logica.md), con el nivel de significación $\alpha$ y el valor crítico, y los contrastes de dos grupos de la [página 4.3](dos-muestras.md).

## El problema

En la [página 4.1](logica.md) contrastamos la afirmación de la agencia, $H_0: p = 0.08$. Con la primera semana de datos no la rechazamos; con ocho semanas, sí, y con muchísima evidencia. Si la tasa real es del 7 %, el contraste de la primera semana se equivocó: dejó pasar una afirmación falsa. ¿Fue mala suerte, o era lo esperable con una semana de datos? Y al revés: cuando un contraste rechaza $H_0$, ¿puede estar equivocándose?

Un contraste toma una decisión con datos incompletos, así que siempre puede equivocarse. Esta página estudia los dos errores posibles, cómo se controlan y cómo se planifica un estudio para que el contraste tenga buenas probabilidades de acertar. Termina con los usos incorrectos más frecuentes de los contrastes.

## Dos tipos de error

$H_0$ es cierta o falsa, y el contraste la rechaza o no. Hay cuatro combinaciones, dos correctas y dos erróneas:

| | $H_0$ es cierta | $H_0$ es falsa |
|---|---|---|
| **No se rechaza $H_0$** | decisión correcta, con probabilidad $1 - \alpha$ | **error de tipo II**, con probabilidad $\beta$ |
| **Se rechaza $H_0$** | **error de tipo I**, con probabilidad $\alpha$ | decisión correcta, con probabilidad $1 - \beta$ (la **potencia**) |

> 📘 **Definición: errores de tipo I y II y potencia**
>
> - El **error de tipo I** es rechazar $H_0$ cuando es cierta: un **falso positivo**. Su probabilidad es el nivel de significación $\alpha$.
> - El **error de tipo II** es no rechazar $H_0$ cuando es falsa: un **falso negativo**. Su probabilidad se escribe $\beta$.
> - La **potencia** de un contraste es la probabilidad de rechazar $H_0$ cuando es falsa: $1 - \beta$. Es la probabilidad de detectar una diferencia que existe.

En el caso de la agencia, un error de tipo I sería reclamar a la agencia por una tasa que en realidad sí es del 8 %. Un error de tipo II sería no reclamar cuando la tasa real es menor. En un test A/B, un error de tipo I es cambiar a una versión que no es mejor, con el coste del cambio y sin ganancia; un error de tipo II es quedarse con la versión antigua cuando la nueva era mejor, y perder las ventas que habría traído.

Las dos probabilidades no se tratan igual. $\alpha$ la elige el analista y se controla directamente. $\beta$ no se puede calcular sin saber cuánto vale de verdad el parámetro, porque depende de lo lejos que esté de $H_0$: es fácil detectar que la tasa es del 5 % en vez del 8 %, y difícil detectar que es del 7.9 %.

## α controla los falsos positivos

¿Qué significa que $\alpha = 0.05$ sea la probabilidad de un error de tipo I? Lo comprobamos simulando. Suponemos que la agencia **tiene razón** ($p = 0.08$), simulamos 10 000 semanas de 3500 emails y hacemos el contraste con cada una:

```python
import numpy as np
from scipy import stats

rng = np.random.default_rng(0)
n, p0 = 3500, 0.08
se0 = np.sqrt(p0 * (1 - p0) / n)
z_critico = stats.norm.ppf(0.975)

# 10 000 semanas en las que H0 es cierta
p_hat = rng.binomial(n, p0, size=10_000) / n
z = (p_hat - p0) / se0
print(f"semanas en las que se rechaza H0 (falsos positivos): {np.mean(np.abs(z) > z_critico):.3f}")
```

```text
semanas en las que se rechaza H0 (falsos positivos): 0.048
```

En un 4.8 % de las semanas el contraste rechaza $H_0$, aunque es cierta: muy cerca del 5 %. El nivel de significación es la proporción de falsos positivos que aceptamos **cuando $H_0$ es cierta**. Un 5 % parece poco, pero quiere decir que, de cada 20 contrastes en los que no hay ninguna diferencia real, uno dará un resultado significativo por azar. Lo usaremos al final de la página.

Si un falso positivo es muy caro, se elige un $\alpha$ menor, como 0.01. El precio es que el contraste se vuelve más exigente y detecta peor las diferencias reales, como veremos ahora.

## La potencia: la probabilidad de detectar una diferencia real

Ahora suponemos que la agencia **se equivoca** y la tasa real es del 7 %. Repetimos la simulación de 10 000 semanas con $p = 0.07$, contrastando siempre $H_0: p = 0.08$:

```python
p_real = 0.07
p_hat = rng.binomial(n, p_real, size=10_000) / n
z = (p_hat - p0) / se0
print(f"semanas en las que se rechaza H0 (potencia): {np.mean(np.abs(z) > z_critico):.3f}")
```

```text
semanas en las que se rechaza H0 (potencia): 0.592
```

Solo en un 59 % de las semanas el contraste detecta que la agencia se equivoca. En el 41 % restante comete un error de tipo II, como nos pasó a nosotros en la primera semana. No fue mala suerte excepcional: con una semana de datos, el contraste tenía una probabilidad cercana a la de lanzar una moneda de detectar una diferencia de un punto.

La potencia también se calcula sin simular. El contraste rechaza $H_0$ si $\hat{p}$ cae fuera de $0.08 \pm 1.96 \cdot \text{SE}_0$, es decir, por debajo de 0.0710 o por encima de 0.0890. Si la tasa real es 0.07, $\hat{p}$ sigue aproximadamente una normal de media 0.07 y error estándar $\sqrt{0.07 \cdot 0.93/3500} = 0.00431$, y la probabilidad de que caiga por debajo de 0.0710 es `stats.norm.cdf(0.0710, 0.07, 0.00431)` $= 0.592$. La de caer por encima de 0.0890 es de solo 0.001, y sumando las dos sale la potencia, 0.593.

### De qué depende la potencia

La potencia depende de cuatro cosas. Con el contraste de la agencia, $H_0: p = 0.08$, bilateral:

**El tamaño de la diferencia real.** Cuanto más lejos esté el valor real del de $H_0$, más fácil es detectarlo. Con una semana de datos y $\alpha = 0.05$:

| Tasa real | 7.5 % | 7.0 % | 6.5 % | 6.0 % |
|---|---:|---:|---:|---:|
| Potencia | 0.19 | 0.59 | 0.93 | 1.00 |

**El tamaño de la muestra.** Con más datos, el error estándar es menor y la misma diferencia se ve con más claridad. Si la tasa real es del 7 %:

| Emails | 1000 | 3500 (1 semana) | 7000 (2 semanas) | 14 000 (4 semanas) | 28 000 (8 semanas) |
|---|---:|---:|---:|---:|---:|
| Potencia | 0.20 | 0.59 | 0.88 | 0.99 | 1.00 |

Con dos semanas, el contraste ya detectaría la diferencia en casi 9 de cada 10 ocasiones. Por eso no rechazar con la primera semana decía tan poco.

**El nivel de significación.** Un $\alpha$ menor pide más evidencia para rechazar, así que reduce los falsos positivos pero también la potencia. Con una semana de datos y una tasa real del 7 %, la potencia es 0.72 con $\alpha = 0.10$, 0.59 con $\alpha = 0.05$ y solo 0.34 con $\alpha = 0.01$. Con un tamaño de muestra fijo, no se pueden reducir a la vez los dos errores: lo que se gana en uno se pierde en el otro. La única forma de reducir los dos es **aumentar la muestra**.

**La variabilidad de los datos y el diseño.** Con datos menos variables, el error estándar es menor y la potencia, mayor. Un diseño pareado, como el del [almacén](dos-muestras.md#muestras-pareadas), aumenta mucho la potencia porque elimina la variación entre individuos. Un contraste unilateral también es más potente que uno bilateral en la dirección que contrasta.

En la demo, la curva gris es la distribución de $\hat{p}$ si la agencia tiene razón, y la azul, si la tasa real es otra. La zona roja es $\alpha$ y la azul, la potencia. Mueve el tamaño de muestra, la tasa real y $\alpha$, y observa cómo cambian los dos errores:

▶️ [Demo interactiva: potencia](https://alberpro.com/nebrija-mpan/topics/04-contraste-hipotesis/demo_potencia.html) (en la web)

## Planificar el tamaño de muestra

La potencia sirve sobre todo **antes** de recoger los datos, para decidir cuántos hacen falta. Lo habitual es pedir una potencia de al menos 0.80: si la diferencia que nos importa existe, el estudio la detectará 8 de cada 10 veces.

> 📘 **Tamaño de muestra para comparar dos proporciones**
>
> Para detectar con un contraste bilateral de nivel $\alpha$ y potencia $1 - \beta$ que la proporción pasa de $p_1$ a $p_2$, hacen falta en **cada grupo**
>
> $$n = \frac{\left(z_{\alpha/2}\sqrt{2\,\bar{p}(1 - \bar{p})} + z_{\beta}\sqrt{p_1(1 - p_1) + p_2(1 - p_2)}\right)^2}{(p_1 - p_2)^2} \qquad \text{con } \bar{p} = \frac{p_1 + p_2}{2}$$
>
> donde $z_{\beta}$ es el valor que deja una probabilidad $\beta$ en la cola de la normal estándar: 0.842 para una potencia de 0.80 y 1.282 para 0.90.

La fórmula sale de exigir las dos cosas a la vez: que el valor crítico esté a $z_{\alpha/2}$ errores estándar de la diferencia cero, y que la diferencia real esté a $z_\beta$ errores estándar más allá. Lo importante es su forma: $n$ crece con el **cuadrado** de $1/(p_1 - p_2)$. Detectar una diferencia la mitad de grande requiere cuatro veces más datos.

> ✏️ **Ejemplo resuelto: planificar un test A/B del email**
>
> Marketing quiere probar un nuevo asunto del email y le interesa detectar si sube la tasa de compra del 7 % al 8 %. Cada día enviará 250 emails con el asunto actual y 250 con el nuevo, asignados al azar. ¿Cuántos días debe durar la prueba, con $\alpha = 0.05$ bilateral y potencia 0.80?
>
> 1. $p_1 = 0.07$, $p_2 = 0.08$, $\bar{p} = 0.075$, $z_{0.025} = 1.960$ y $z_{0.20} = 0.842$.
> 2. Numerador: $\left(1.960 \sqrt{2 \cdot 0.075 \cdot 0.925} + 0.842\sqrt{0.07 \cdot 0.93 + 0.08 \cdot 0.92}\right)^2 = (0.730 + 0.314)^2 = 1.089$.
> 3. $n = 1.089/0.01^2 = 10\,889.1$, así que hacen falta **10 890 emails por versión**, redondeando hacia arriba.
> 4. Con 250 emails al día por versión, hacen falta $10\,890/250 = 43.6$, es decir, **44 días**.
>
> Una prueba de una semana tendría una potencia muy baja: si el nuevo asunto fuera mejor, lo más probable sería no detectarlo, y marketing concluiría por error que "el asunto no importa". Planificar la duración antes de empezar evita ese error y evita también la tentación de parar la prueba en cuanto el resultado sale significativo.

## Significación estadística y relevancia práctica

"Significativo" en estadística quiere decir que la diferencia es difícil de explicar por azar, no que sea grande ni importante. Con muestras muy grandes, diferencias minúsculas salen significativas.

Supón que una gran cadena compara dos versiones de su email con 2 millones de envíos por versión, y obtiene una tasa del 7.00 % con una y del 7.10 % con la otra. El contraste de dos proporciones da $z = 3.91$ y un p-valor de 0.0001: muy significativo. Pero la diferencia es de una décima: en nuestra tienda, con 500 emails al día, serían 0.5 ventas más al día, unos 7.50 € de margen. Si cambiar de versión cuesta algo, no compensa.

Por eso un contraste nunca va solo. Hay que dar el **tamaño de la diferencia** con su intervalo de confianza y traducirlo a la unidad del negocio (ventas, euros, minutos), y decidir con esa cifra. El p-valor dice si la diferencia es real; el intervalo dice si importa.

Lo contrario también ocurre: con muestras pequeñas, diferencias grandes e importantes pueden salir no significativas, porque la potencia es baja. "No significativo" no quiere decir "sin efecto".

## Muchos contrastes a la vez

Al revisar los datos de la campaña, un analista contrasta cada día de la semana frente a los demás: siete contrastes, cada uno con $\alpha = 0.05$. Si en realidad no hubiera ninguna diferencia entre días, cada contraste tendría un 5 % de probabilidad de dar un falso positivo, y la probabilidad de que **al menos uno** lo diera sería

$$1 - 0.95^7 = 0.30$$

Con 20 contrastes, la probabilidad sube a $1 - 0.95^{20} = 0.64$. Quien hace muchos contrastes y solo informa de los que salen significativos casi seguro encontrará "efectos" que no existen. Probar muchas comparaciones, muchas formas de medir o muchos subgrupos hasta que algo sale significativo se llama **p-hacking**, y es una de las causas principales de resultados que luego no se repiten.

Hay tres defensas sencillas:

- **Fijar de antemano** qué se va a contrastar, con qué datos y con qué $\alpha$, y respetarlo.
- **Corregir $\alpha$** cuando se hacen $m$ contrastes. La corrección más simple, la **de Bonferroni**, usa $\alpha/m$ en cada uno: con siete días y $\alpha = 0.05$, cada contraste usa $0.05/7 = 0.007$.
- **Confirmar con datos nuevos** lo que se ha descubierto explorando. Un resultado encontrado mirando los datos es una hipótesis para el siguiente estudio, no una conclusión.

En nuestro caso, la diferencia de los lunes resiste cualquier corrección: su p-valor, $8 \cdot 10^{-10}$, es muchísimo menor que 0.007.

> ⛔ **Errores frecuentes**
>
> - **Concluir que no hay efecto porque el contraste no es significativo**, sin mirar la potencia ni el intervalo de confianza.
> - **Confundir significativo con importante.** Con muchos datos, cualquier diferencia minúscula es significativa.
> - **Lanzar un estudio sin calcular el tamaño de muestra** y descubrir al final que no tenía potencia para detectar nada útil.
> - **Mirar los resultados cada día y parar la prueba cuando sale significativa.** Cada mirada es un contraste más, y la probabilidad de un falso positivo sube muy por encima de $\alpha$.
> - **Hacer muchos contrastes y quedarse con los significativos**, sin corregir $\alpha$ ni confirmarlos con datos nuevos.

## En Python

```python
import numpy as np
from scipy import stats

def potencia_una_proporcion(n, p0, p_real, alpha=0.05):
    """Potencia del contraste bilateral de H0: p = p0 si la proporción real es p_real."""
    z = stats.norm.ppf(1 - alpha / 2)
    se0 = np.sqrt(p0 * (1 - p0) / n)
    se1 = np.sqrt(p_real * (1 - p_real) / n)
    abajo, arriba = p0 - z * se0, p0 + z * se0               # fuera de aquí se rechaza H0
    return stats.norm.cdf(abajo, p_real, se1) + stats.norm.sf(arriba, p_real, se1)

for n in [3500, 7000, 14_000]:
    print(f"n = {n:>6}: potencia = {potencia_una_proporcion(n, 0.08, 0.07):.3f}")
```

```text
n =   3500: potencia = 0.593
n =   7000: potencia = 0.884
n =  14000: potencia = 0.995
```

```python
def n_dos_proporciones(p1, p2, alpha=0.05, potencia=0.80):
    """Tamaño de cada grupo para detectar el paso de p1 a p2 (contraste bilateral)."""
    z_a, z_b = stats.norm.ppf(1 - alpha / 2), stats.norm.ppf(potencia)
    p_media = (p1 + p2) / 2
    num = (z_a * np.sqrt(2 * p_media * (1 - p_media)) + z_b * np.sqrt(p1 * (1 - p1) + p2 * (1 - p2))) ** 2
    return int(np.ceil(num / (p1 - p2) ** 2))

n = n_dos_proporciones(0.07, 0.08)
print(f"test A/B del 7 % al 8 %: {n} emails por versión, {np.ceil(n / 250):.0f} días con 250 al día")
```

```text
test A/B del 7 % al 8 %: 10890 emails por versión, 44 días con 250 al día
```

```python
# Probabilidad de al menos un falso positivo con m contrastes, y corrección de Bonferroni
for m in [1, 7, 20]:
    print(f"{m:>2} contrastes: P(algún falso positivo) = {1 - 0.95 ** m:.2f}, alpha de Bonferroni = {0.05 / m:.4f}")
```

```text
 1 contrastes: P(algún falso positivo) = 0.05, alpha de Bonferroni = 0.0500
 7 contrastes: P(algún falso positivo) = 0.30, alpha de Bonferroni = 0.0071
20 contrastes: P(algún falso positivo) = 0.64, alpha de Bonferroni = 0.0025
```

> ✅ **Lo esencial**
>
> - Un contraste puede cometer dos errores: el de **tipo I** (rechazar una $H_0$ cierta, con probabilidad $\alpha$) y el de **tipo II** (no rechazar una $H_0$ falsa, con probabilidad $\beta$).
> - La **potencia**, $1 - \beta$, es la probabilidad de detectar una diferencia real. Aumenta con el tamaño de la diferencia, con el tamaño de la muestra y con $\alpha$, y con diseños que reducen la variabilidad, como el pareado.
> - Con una muestra fija, reducir $\alpha$ reduce la potencia. Para reducir los dos errores hay que aumentar la muestra, y el tamaño necesario se calcula **antes** de empezar.
> - **Significativo no es lo mismo que importante**: hay que dar el tamaño de la diferencia con su intervalo y traducirlo a euros, ventas o minutos.
> - Con muchos contrastes aparecen falsos positivos. Se fija el análisis de antemano, se corrige $\alpha$ y se confirma con datos nuevos.

## Ejercicios

**1.** Un banco usa un contraste para decidir si una transacción es fraudulenta, con $H_0$: "la transacción es legítima". Describe en este contexto el error de tipo I y el de tipo II. ¿Cuál crees que es más caro? ¿Subirías o bajarías $\alpha$?

<details>
<summary>✅ <b>Solución</b></summary>

**Tipo I:** marcar como fraude una transacción legítima. El cliente ve bloqueada su tarjeta, se enfada y hay que atenderle. **Tipo II:** dejar pasar un fraude real. El banco o el cliente pierden el dinero.

Normalmente el fraude no detectado es más caro, así que el banco prefiere más potencia aunque tenga más falsos positivos: usaría un $\alpha$ **mayor** que el 5 % habitual. No hay una respuesta única: depende de los costes de cada error, y es una decisión de negocio, no estadística.

</details>

**2.** Indica si cada cambio aumenta o reduce la potencia de un contraste, manteniendo lo demás igual. (a) Duplicar el tamaño de la muestra. (b) Pasar de $\alpha = 0.05$ a $\alpha = 0.01$. (c) Que la diferencia real sea mayor. (d) Pasar de un contraste bilateral a uno unilateral en la dirección correcta. (e) Medir a los mismos clientes antes y después en lugar de a clientes distintos.

<details>
<summary>✅ <b>Solución</b></summary>

(a) **Aumenta**: el error estándar baja. (b) **Reduce**: hace falta más evidencia para rechazar. (c) **Aumenta**: la diferencia es más fácil de ver. (d) **Aumenta**: toda la región crítica está en el lado correcto. (e) **Aumenta**: el diseño pareado elimina la variación entre clientes.

</details>

**3.** Una web tiene una tasa de conversión del 10 % y quiere detectar si un nuevo diseño la sube al 12 %, con $\alpha = 0.05$ bilateral y potencia 0.80. ¿Cuántos visitantes necesita en cada versión? ¿Y si quisiera detectar una subida al 11 %?

<details>
<summary>✅ <b>Solución</b></summary>

Con $p_1 = 0.10$, $p_2 = 0.12$ y $\bar{p} = 0.11$: $n = \left(1.96\sqrt{2 \cdot 0.11 \cdot 0.89} + 0.842\sqrt{0.10 \cdot 0.90 + 0.12 \cdot 0.88}\right)^2 / 0.02^2 = 3841$ visitantes por versión (`n_dos_proporciones(0.10, 0.12)`).

Para detectar una subida al 11 %, la diferencia es la mitad, así que hacen falta unas cuatro veces más: `n_dos_proporciones(0.10, 0.11)` da 14 751 por versión.

</details>

**4.** Un equipo de marketing prueba 20 variantes de un anuncio, cada una frente al anuncio actual, con $\alpha = 0.05$. Una de ellas sale significativa, con p-valor 0.03. ¿Deberían adoptarla? ¿Qué harías?

<details>
<summary>✅ <b>Solución</b></summary>

No sin más comprobaciones. Si ninguna variante fuera mejor, la probabilidad de que al menos una de 20 saliera significativa por azar sería $1 - 0.95^{20} = 0.64$. Un resultado así es lo que cabe esperar aunque ninguna variante funcione.

Con la corrección de Bonferroni, cada contraste debería usar $0.05/20 = 0.0025$, y 0.03 no lo cumple. Lo razonable es tratar esa variante como candidata y hacer un nuevo test, planificado con su tamaño de muestra, solo con ella frente al anuncio actual.

</details>

**5.** *(Más difícil)* Con una semana de datos (3500 emails) y $\alpha = 0.05$ bilateral, calcula la potencia del contraste de la agencia si la tasa real fuera del 7.5 %. ¿Cuántos emails harían falta para tener una potencia de 0.80 frente a esa tasa? Usa la función `potencia_una_proporcion` y prueba valores de $n$.

<details>
<summary>✅ <b>Solución</b></summary>

`potencia_una_proporcion(3500, 0.08, 0.075)` $= 0.19$. Con una semana, el contraste solo detectaría una diferencia de medio punto una de cada cinco veces.

Probando valores, la potencia llega a 0.80 hacia los 23 000 emails, unas 6.5 semanas de campaña. Comparado con una diferencia de un punto, que con dos semanas ya tenía potencia 0.88, se ve de nuevo la regla general: reducir a la mitad la diferencia que se quiere detectar multiplica por cuatro la muestra necesaria.

</details>

[← Comparar dos grupos](dos-muestras.md) [Siguiente: Práctica guiada →](practica.md)
