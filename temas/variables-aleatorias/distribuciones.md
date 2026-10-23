# 3. Distribuciones con nombre propio

> Versión web, con demos interactivas: <https://nebrija-mpan.alberpro.com/temas/variables-aleatorias/distribuciones/>

> ℹ️ **Lo que necesitas antes**
>
> Qué es una variable aleatoria, y cómo se usan la PMF, la PDF y la CDF para calcular probabilidades. Lo tienes en [2. Variables aleatorias](variables.md).

## Por qué unas pocas distribuciones bastan para casi todo

En la página anterior construimos la distribución del número de artículos por pedido a partir de una tabla de datos. Funcionó, pero tiene dos limitaciones. La primera es que necesitamos una probabilidad para cada valor posible: con cinco valores es manejable, con cientos no. La segunda es que una tabla no explica **por qué** los datos tienen esa forma, así que no nos dice qué pasará si cambian las condiciones (más tráfico, otra campaña, otra hora del día).

La buena noticia es que muchos fenómenos muy distintos comparten el mismo **mecanismo** de generación, y por tanto la misma forma de distribución. Contar cuántos clientes de 200 compran, cuántas piezas de un lote salen defectuosas o cuántos pacientes responden a un tratamiento son, en el fondo, el mismo problema: contar éxitos entre intentos independientes. Esos patrones recurrentes tienen nombre propio, una fórmula conocida y unos pocos **parámetros** que ajustan la forma a cada caso concreto. Escribimos $X \sim \text{Binomial}(n, p)$ para decir "$X$ sigue una distribución binomial con parámetros $n$ y $p$".

Modelar con una distribución con nombre propio significa hacer una **suposición** sobre cómo se generan los datos. Si la suposición es razonable, a cambio obtienes mucho: con uno o dos números describes todo el comportamiento de la variable, puedes calcular cualquier probabilidad y razonar sobre escenarios que todavía no has observado. Por eso, para cada distribución veremos tres cosas: la **historia** que la genera, de dónde sale su **fórmula** y cómo **comprobar con datos** si encaja.

## ¿Qué distribución uso?

La pregunta clave es **qué estás contando o midiendo**. Esta guía cubre los casos de este tema:

| Lo que observas | Distribución | Ejemplo de negocio |
|---|---|---|
| Un único sí/no | Bernoulli$(p)$ | ¿este cliente renueva? |
| Nº de éxitos en $n$ intentos independientes con la misma $p$ | Binomial$(n, p)$ | compras entre 200 visitas |
| Nº de eventos en un intervalo, a ritmo constante | Poisson$(\lambda)$ | clientes por hora, incidencias al día |
| $k$ resultados igual de probables | Uniforme discreta$(a, b)$ | un dado, un sorteo |
| Una medida continua simétrica alrededor de un centro | Normal$(\mu, \sigma^2)$ | errores de medición, peso de un envase, medias de muchas observaciones |

Hay muchas más (exponencial para tiempos de espera, geométrica, t de Student, beta…). Algunas aparecerán más adelante en el curso; la t de Student, por ejemplo, en [contraste de hipótesis](../contraste-hipotesis/index.md). El explorador de abajo te permite ver cómo cambia la forma de cada una al mover sus parámetros y calcular probabilidades de intervalos. Úsalo mientras lees cada sección.

▶️ [Demo interactiva: distribuciones](https://nebrija-mpan.alberpro.com/temas/variables-aleatorias/demo_distribuciones.html) (en la web)

## Uniforme discreta: todos los resultados igual de probables

**La historia.** Hay $k$ resultados posibles, los enteros de $a$ a $b$, y ninguno tiene ventaja sobre los demás: un dado equilibrado, una persona elegida por sorteo, un número de lotería.

> 📘 **Definición: uniforme discreta$(a, b)$**
>
> $$P(X = x) = \frac{1}{b - a + 1}, \quad x = a, a+1, \ldots, b \qquad\qquad \mathbb{E}[X] = \frac{a + b}{2}, \qquad \mathbb{V}[X] = \frac{(b - a + 1)^2 - 1}{12}$$

Es la regla de Laplace de la [página 1](probabilidad.md) convertida en distribución. Para un dado, $a = 1$ y $b = 6$, así que cada cara tiene probabilidad $1/6$, la media es $3.5$ y la varianza $35/12 \approx 2.92$. Que la media sea 3.5, un valor que el dado nunca muestra, recuerda que el valor esperado es un promedio a largo plazo, no el resultado "típico".

**Cuándo no encaja.** En negocio es rara: casi nunca todos los valores son igual de probables. Las ventas por día de la semana o la hora a la que llegan los pedidos casi nunca son uniformes, y suponerlo lleva a planificar mal.

## Bernoulli: un sí o un no

**La historia.** Un único experimento con dos resultados posibles, que codificamos como **éxito** ($X = 1$) con probabilidad $p$ y **fracaso** ($X = 0$) con probabilidad $1 - p$. Que un cliente renueve, que un préstamo se devuelva, que un usuario acepte las cookies. "Éxito" es solo una etiqueta: puede ser algo indeseable, como que un cliente se dé de baja.

> 📘 **Definición: Bernoulli$(p)$**
>
> $$P(X = x) = p^x (1 - p)^{1 - x}, \quad x \in \{0, 1\} \qquad\qquad \mathbb{E}[X] = p, \qquad \mathbb{V}[X] = p(1 - p)$$

La fórmula es una forma compacta de escribir $P(X = 1) = p$ y $P(X = 0) = 1 - p$: sustituye $x = 1$ y $x = 0$ y lo verás. Codificar con 0 y 1 tiene una ventaja práctica enorme: **la media de una columna de ceros y unos es la proporción de unos**. Si 589 de 1000 usuarios aceptan las cookies, la media de la columna es 0.589, que es a la vez la proporción observada y la estimación natural de $p$. Lo usaremos en la [práctica guiada](practica.md).

La varianza $p(1 - p)$ es máxima cuando $p = 0.5$, la situación de máxima incertidumbre, y vale 0 cuando $p = 0$ o $p = 1$, porque entonces     el resultado es seguro.

## Binomial: contar éxitos

**La historia.** Repetimos $n$ veces un experimento de Bernoulli y contamos cuántos éxitos hay. Para que el resultado sea binomial hacen falta tres condiciones:

1. el número de intentos $n$ está **fijado de antemano**;
2. los intentos son **independientes** entre sí;
3. la probabilidad de éxito $p$ es **la misma** en todos.

Dicho de otro modo, si $X_1, \ldots, X_n$ son Bernoulli$(p)$ independientes, su suma $X = X_1 + \cdots + X_n$ es Binomial$(n, p)$.

### De dónde sale la fórmula

Construyámosla con un caso pequeño. Tres clientes reciben una oferta, cada uno la acepta con probabilidad $p = 0.3$, independientemente. ¿Qué probabilidad hay de que **exactamente dos** acepten? Escribiendo A para "acepta" y R para "rechaza", hay tres formas de que ocurra: AAR, ARA y RAA. Por la independencia, cada una tiene probabilidad $0.3 \cdot 0.3 \cdot 0.7 = p^2 (1 - p)$. Como son sucesos que no pueden ocurrir a la vez, se suman:

$$P(X = 2) = 3 \cdot p^2 (1 - p) = 3 \cdot 0.09 \cdot 0.7 = 0.189.$$

El razonamiento general es idéntico: cada secuencia concreta con $k$ éxitos y $n - k$ fracasos tiene probabilidad $p^k (1 - p)^{n - k}$, y el número de secuencias distintas es el **número combinatorio** $\binom{n}{k}$, las formas de elegir qué $k$ de los $n$ intentos son éxitos. Con $n = 3$ y $k = 2$, $\binom{3}{2} = 3$.

> 📘 **Definición: Binomial$(n, p)$**
>
> $$P(X = k) = \binom{n}{k} p^k (1 - p)^{n - k}, \quad k = 0, 1, \ldots, n, \qquad \text{con } \binom{n}{k} = \frac{n!}{k!\,(n - k)!}$$
>
> $$\mathbb{E}[X] = np, \qquad \mathbb{V}[X] = np(1 - p)$$

La media tiene una lectura inmediata: si cada intento acierta con probabilidad $p$, en $n$ intentos esperas $np$ aciertos.

> ✏️ **Ejemplo resuelto: conversión de una campaña**
>
> El 5 % de las visitas a una página de producto acaba en compra. Mañana esperas 200 visitas. Si las visitas se comportan de forma independiente y con la misma probabilidad de compra, el número de compras es $X \sim \text{Binomial}(200, 0.05)$.
>
> 1. **Cuántas compras esperar.** $\mathbb{E}[X] = 200 \cdot 0.05 = 10$, con desviación típica $\sqrt{200 \cdot 0.05 \cdot 0.95} = \sqrt{9.5} \approx 3.08$. Lo normal será ver entre 7 y 13 compras.
> 2. **Probabilidad de exactamente 10.** $P(X = 10) = \binom{200}{10} 0.05^{10}\, 0.95^{190} \approx 0.128$. Aunque 10 es el valor más probable, solo ocurre una de cada ocho veces: con una distribución así, acertar un valor exacto es raro.
> 3. **Probabilidad de 15 compras o más**, por ejemplo para decidir si hay stock suficiente. "15 o más" es lo contrario de "14 o menos": $P(X \ge 15) = 1 - F(14) \approx 0.078$. Un día de cada trece, aproximadamente.

**Cuándo no encaja.** Si $p$ cambia entre intentos (visitas de campañas distintas, días distintos) o los intentos no son independientes (amigos que compran juntos, un fallo de máquina que afecta a varias piezas seguidas), la binomial subestima la variabilidad real. En la [práctica guiada](practica.md) veremos un caso en que $p$ cambia de un día a otro.

## Poisson: contar eventos en el tiempo

**La historia.** Contamos cuántas veces ocurre un evento en un intervalo de tiempo (o de espacio): clientes que entran en una hora, llamadas a un call center, incidencias técnicas al día, pedidos por minuto. A diferencia de la binomial, no hay un número fijo de intentos: el evento puede ocurrir 0, 1, 2… veces, sin límite superior. La Poisson describe este conteo cuando los eventos:

1. ocurren **independientemente** unos de otros;
2. a un **ritmo medio constante** $\lambda$ por intervalo;
3. y nunca dos exactamente a la vez.

### De dónde sale: muchos intentos, cada uno muy improbable

Piensa en una hora como 3600 segundos. En cada segundo entra un cliente o no, con una probabilidad minúscula. Si de media entran 4 clientes por hora, esa probabilidad es $4/3600$. El número de clientes por hora sería entonces Binomial$(3600, 4/3600)$: muchísimos intentos con probabilidad diminuta, pero con un número esperado de éxitos fijo, $np = 4$. Si dividimos la hora en intervalos cada vez más finos, la binomial converge a una distribución que solo depende de $\lambda = np$: la Poisson. Numéricamente, la probabilidad de 2 clientes sale $0.14648$ con la binomial de 3600 segundos y $0.14653$ con la Poisson.

> 📘 **Definición: Poisson$(\lambda)$**
>
> $$P(X = k) = \frac{e^{-\lambda} \lambda^k}{k!}, \quad k = 0, 1, 2, \ldots \qquad\qquad \mathbb{E}[X] = \mathbb{V}[X] = \lambda$$

El parámetro $\lambda$ es el número medio de eventos **en el intervalo que estás considerando**, y se escala con su longitud: 4 clientes por hora son 2 por media hora o 32 en una jornada de 8 horas. Este mismo argumento justifica usar la Poisson como **aproximación de la binomial** cuando $n$ es grande y $p$ pequeña: con 1000 clientes, cada uno con probabilidad 0.003 de reclamar, $P(X = 5)$ vale $0.1009$ con la binomial exacta y $0.1008$ con Poisson$(3)$.

> ✏️ **Ejemplo resuelto: clientes por hora**
>
> En una tienda entran de media 4 clientes por hora, así que $X \sim \text{Poisson}(4)$.
>
> 1. **Una hora sin clientes.** $P(X = 0) = e^{-4} \approx 0.018$. Ocurre menos de una vez cada 50 horas: si pasa a menudo, algo ha cambiado (la puerta cerrada, una obra en la calle…).
> 2. **Una hora tranquila, con 2 clientes o menos.** $P(X \le 2) = e^{-4}\left(1 + 4 + \frac{4^2}{2}\right) = 13\,e^{-4} \approx 0.238$.
> 3. **Necesidad de refuerzo.** Si con más de 8 clientes en una hora hace falta un segundo dependiente, $P(X > 8) = 1 - F(8) \approx 0.021$. Sucederá en torno al 2 % de las horas, unas 2 veces en una semana de 100 horas de apertura.

### Cómo comprobar con datos si es Poisson

Como en una Poisson **la media y la varianza coinciden**, la comprobación más rápida es calcular ambas en los conteos observados. Si la varianza es claramente mayor que la media (**sobredispersión**), suele indicar que el ritmo no es constante, por ejemplo porque se mezclan horas punta y horas valle, o días laborables y fines de semana. La solución habitual es separar los grupos y modelar cada uno con su propio $\lambda$.

## Normal: la campana de Gauss

**La historia.** Una magnitud continua que fluctúa simétricamente alrededor de un valor central, con valores cercanos al centro frecuentes y valores alejados cada vez más raros. Aparece cuando una medida es la **suma de muchos efectos pequeños e independientes**: el peso de un envase de 500 g resulta de pequeñas variaciones de la máquina, la temperatura, el material… Este hecho tiene una versión precisa, el [teorema del límite central](inferencia.md), que explica por qué la normal es la distribución más importante de la estadística: las **medias** de muchas observaciones son aproximadamente normales, sea cual sea la distribución de partida.

> 📘 **Definición: Normal$(\mu, \sigma^2)$**
>
> $$f(x) = \frac{1}{\sqrt{2\pi\sigma^2}}\, e^{-\frac{(x - \mu)^2}{2\sigma^2}} \qquad\qquad \mathbb{E}[X] = \mu, \qquad \mathbb{V}[X] = \sigma^2$$

Los dos parámetros tienen una lectura directa: $\mu$ coloca el centro de la campana (que es a la vez media, mediana y moda) y $\sigma$ fija su anchura. No hay una fórmula sencilla para el área bajo la curva, así que las probabilidades se calculan con la CDF en Python, o con la regla siguiente para estimar de cabeza:

> 💡 **Regla 68–95–99.7**
>
> En cualquier normal, un 68.3 % de los valores está a menos de $1\sigma$ de la media, un 95.4 % a menos de $2\sigma$ y un 99.7 % a menos de $3\sigma$. Un valor a más de 3 desviaciones típicas de la media es muy raro.

### Normal estándar

Toda normal es la misma campana desplazada y estirada. Si restas la media y divides por la desviación típica, obtienes la **normal estándar** $Z \sim N(0, 1)$:

$$Z = \frac{X - \mu}{\sigma} \sim N(0, 1), \qquad\qquad X = \mu + \sigma Z.$$

El valor $z$ de una observación dice **a cuántas desviaciones típicas de la media está**, con signo. Eso permite comparar valores medidos en escalas distintas. Por ejemplo, un vendedor que factura 82 000 € en una región donde la media es 70 000 € con $\sigma = 10\,000$ € tiene $z = 1.2$. Otro que factura 7 500 € en una región pequeña con media 6 000 € y $\sigma = 1\,000$ € tiene $z = 1.5$. En relación con su entorno, el segundo destaca más, aunque facture mucho menos.

> ✏️ **Ejemplo resuelto: alturas**
>
> Las alturas de una población siguen $X \sim N(170, 8^2)$ en centímetros.
>
> 1. **¿Es muy alta una persona de 185 cm?** Su valor z es $(185 - 170)/8 = 1.875$, casi dos desviaciones típicas por encima. Por la regla 68–95–99.7, más allá de $2\sigma$ por arriba queda un 2.5 %, así que esperamos algo más que eso. El valor exacto es $P(X > 185) \approx 0.030$, un 3 %.
> 2. **Proporción entre 160 y 180 cm.** Es un intervalo de $\pm 1.25\sigma$ alrededor de la media: $P(160 < X < 180) = F(180) - F(160) \approx 0.789$.
> 3. **Altura de diseño.** Para que un producto sirva al 95 % de la población, buscamos el percentil 95: $q_{0.95} = 170 + 1.645 \cdot 8 \approx 183.2$ cm.

**Cuándo no encaja.** Muchos datos de negocio **no** son normales: los ingresos, los importes de compra o los tiempos de espera son asimétricos, con una cola larga a la derecha. Tampoco lo son las variables acotadas cerca de un extremo (una tasa de error cercana a 0). Antes de suponer normalidad, mira el histograma y compara la media con la mediana: si difieren mucho, la distribución es asimétrica.

## Aproximación normal de la binomial

Una binomial es una suma de $n$ Bernoullis independientes. Por el mismo motivo que hace normal la suma de muchos efectos pequeños, cuando $n$ es grande la binomial se parece a una normal con su misma media y varianza:

$$\text{Binomial}(n, p) \approx N\big(np,\ np(1 - p)\big) \qquad \text{si } np > 5 \text{ y } n(1 - p) > 5.$$

La condición pide que haya suficientes éxitos y fracasos esperados para que la campana no quede "aplastada" contra el 0 o contra $n$. La aproximación fue esencial antes de los ordenadores y sigue siendo la base de los intervalos de confianza para proporciones que veremos en [5. Inferencia](inferencia.md).

**La corrección de continuidad.** La binomial reparte la probabilidad en barras de anchura 1 centradas en los enteros; la normal la reparte de forma continua. La barra del valor 14 ocupa el intervalo de 13.5 a 14.5, así que "14 o menos" en la binomial corresponde a "hasta 14.5" en la normal:

$$P(X \le k) \approx P(Y \le k + 0.5), \qquad P(X \ge k) \approx P(Y \ge k - 0.5).$$

En el ejemplo de la campaña, $Y \sim N(10, 9.5)$ y $P(X \ge 15) \approx P(Y \ge 14.5) \approx 0.072$, frente a 0.078 exacto. La aproximación no es perfecta aquí porque $np = 10$ está cerca del límite y la binomial todavía es algo asimétrica. Activa la opción *Mostrar aproximación normal* del explorador para ver cuándo encaja bien y cuándo no.

## En Python: `scipy.stats`

Todas las distribuciones de `scipy.stats` comparten la misma interfaz, así que basta con aprenderla una vez:

| Método | Qué calcula | Ejemplo |
|---|---|---|
| `.pmf(k)` / `.pdf(x)` | PMF (discretas) / PDF (continuas) | `stats.binom.pmf(10, 200, 0.05)` |
| `.cdf(x)` | $P(X \le x)$ | `stats.poisson.cdf(2, 4)` |
| `.sf(x)` | $P(X > x) = 1 - F(x)$ | `stats.norm.sf(185, 170, 8)` |
| `.ppf(q)` | cuantil: el $x$ tal que $F(x) = q$ | `stats.norm.ppf(0.95, 170, 8)` |
| `.rvs(size)` | simula una muestra aleatoria | `stats.poisson.rvs(4, size=1000)` |
| `.mean()`, `.var()`, `.std()` | momentos teóricos | `stats.binom(200, 0.05).mean()` |

```python
from scipy import stats

campaña = stats.binom(n=200, p=0.05)          # "congelamos" la distribución con sus parámetros
print(campaña.mean(), campaña.std())          # 10.0 y 3.08
```

```text
10.0 3.082207001484488
```

```python
print(campaña.pmf(10))                        # P(X = 10)  ≈ 0.128
```

```text
0.12835737340035233
```

```python
print(campaña.sf(14))                         # P(X ≥ 15) = P(X > 14) ≈ 0.078
```

```text
0.07813441567325656
```

```python
clientes = stats.poisson(mu=4)                # en scipy, λ se llama mu
print(clientes.pmf(0), clientes.sf(8))        # ≈ 0.018 y ≈ 0.021
```

```text
0.01831563888873418 0.021363434487984143
```

```python
alturas = stats.norm(loc=170, scale=8)        # ¡scale es σ, no σ²!
print(alturas.cdf(180) - alturas.cdf(160))    # ≈ 0.789
```

```text
0.7887004526662893
```

```python
print(alturas.ppf(0.95))                      # percentil 95 ≈ 183.2
```

```text
183.1588290156118
```

```python
# ¿Encajan unos conteos con una Poisson? Compara media y varianza
conteos = stats.poisson.rvs(4, size=500, random_state=1)
print(conteos.mean(), conteos.var(ddof=1))    # parecidas: compatible con Poisson
```

```text
4.094 4.377919839679358
```

> ⚠️ **Errores frecuentes**
>
> - **Pasar la varianza en vez de la desviación típica a `stats.norm`.** `stats.norm(170, 64)` es una normal con $\sigma = 64$, no con $\sigma^2 = 64$.
> - **Usar `sf(k)` para "$k$ o más" en una discreta.** $P(X \ge k)$ es `sf(k - 1)`.
> - **Olvidar que `stats.randint(a, b)` excluye $b$.** Un dado es `stats.randint(1, 7)`.
> - **No escalar $\lambda$ al intervalo.** Si hay 12 llamadas por hora, en 15 minutos $\lambda = 3$, no 12.
> - **Suponer normalidad por costumbre** en datos claramente asimétricos, como importes o tiempos.
> - **Leer `pdf` como una probabilidad.** `stats.norm.pdf(170, 170, 8)` $\approx 0.0499$ es una densidad, no la probabilidad de medir 170 cm.

> ✅ **Lo esencial**
>
> - Una distribución con nombre propio resume, con uno o dos parámetros, un **mecanismo** que genera datos; usarla es una **suposición** que conviene comprobar.
> - **Bernoulli:** un sí/no; la media de una columna 0/1 es la proporción de unos.
> - **Binomial:** éxitos en $n$ intentos independientes con la misma $p$; media $np$. Su fórmula cuenta secuencias por probabilidad de cada secuencia.
> - **Poisson:** eventos en un intervalo a ritmo constante; media igual a varianza, y $\lambda$ se escala con el intervalo.
> - **Normal:** simétrica alrededor de $\mu$; con la escala $z$ y la regla 68–95–99.7 estimas probabilidades de cabeza. Aproxima a la binomial cuando $np$ y $n(1 - p)$ superan 5.

## Ejercicios

**1.** ¿Qué distribución usarías? (a) Nº de emails de spam entre los 50 que llegan hoy, si cada uno es spam con probabilidad 0.2. (b) Nº de pedidos por minuto en una web. (c) Peso de un paquete de café de 250 g. (d) Si un cliente concreto renueva o no su suscripción.

<details>
<summary>✅ <b>Solución</b></summary>

- (a) **Binomial$(50, 0.2)$:** número fijo de intentos (50 emails), cada uno con la misma probabilidad, suponiendo independencia.
- (b) **Poisson:** eventos en un intervalo de tiempo, sin número máximo de intentos.
- (c) **Normal** centrada cerca de 250 g: una medida continua resultado de muchas pequeñas variaciones del proceso de envasado.
- (d) **Bernoulli:** un único sí/no.

</details>

**2.** Un call center recibe de media 12 llamadas por hora. ¿Qué distribución sigue el número de llamadas en 15 minutos? Calcula la probabilidad de que en un cuarto de hora no llegue ninguna y la de que lleguen 5 o más.

<details>
<summary>✅ <b>Solución</b></summary>

El ritmo se escala con el intervalo: $\lambda = 12 \cdot \frac{15}{60} = 3$, así que $X \sim \text{Poisson}(3)$.

- **Ninguna llamada:** $P(X = 0) = e^{-3} \approx 0.050$, un 5 % de los cuartos de hora.
- **5 o más:** es el complementario de "4 o menos", $P(X \ge 5) = 1 - F(4) \approx 0.185$. En Python, `stats.poisson.sf(4, 3)`.

</details>

**3.** Cinco clientes reciben una oferta de renovación, y cada uno la acepta con probabilidad 0.2, de forma independiente. Calcula a mano la probabilidad de que la acepten exactamente 2 y la de que la acepte al menos uno.

<details>
<summary>✅ <b>Solución</b></summary>

$X \sim \text{Binomial}(5, 0.2)$.

- **Exactamente 2:** hay $\binom{5}{2} = 10$ formas de elegir qué dos clientes aceptan, y cada una tiene probabilidad $0.2^2 \cdot 0.8^3 = 0.04 \cdot 0.512 = 0.02048$. En total, $10 \cdot 0.02048 = 0.2048$.
- **Al menos uno:** es más fácil por el complementario. La probabilidad de que nadie acepte es $0.8^5 = 0.32768$, así que $P(X \ge 1) = 1 - 0.32768 \approx 0.672$.

</details>

**4.** Los tiempos de entrega siguen N(48, 6²) horas. (a) Estima con la regla 68–95–99.7 qué porcentaje de pedidos tarda más de 60 h y compruébalo con Python. (b) ¿Qué porcentaje llega entre 42 y 54 h? (c) ¿Qué plazo de entrega puedes prometer si quieres cumplirlo en el 95 % de los pedidos?

<details>
<summary>✅ <b>Solución</b></summary>

- (a) 60 h está a $z = (60 - 48)/6 = 2$ desviaciones típicas por encima de la media. Fuera de $\pm 2\sigma$ queda aproximadamente un 5 %, la mitad por cada lado, así que esperamos un 2.5 %. El valor exacto es `stats.norm.sf(60, 48, 6)` $\approx 0.023$.
- (b) Es el intervalo $\mu \pm 1\sigma$: aproximadamente un **68 %** (exactamente 68.3 %).
- (c) Hay que buscar el percentil 95: `stats.norm.ppf(0.95, 48, 6)` $= 48 + 1.645 \cdot 6 \approx 57.9$ horas. Se puede prometer **58 horas**.

</details>

**5.** Unos conteos diarios de devoluciones tienen media 20 y varianza 85. ¿Te parece razonable modelarlos con una Poisson? ¿Qué harías?

<details>
<summary>✅ <b>Solución</b></summary>

Poco razonable. En una Poisson la varianza es igual a la media, y aquí es más de cuatro veces mayor: hay **sobredispersión**. Suele indicar que el ritmo de devoluciones no es constante, por ejemplo porque se mezclan días normales con días posteriores a campañas, o laborables con fines de semana. Lo indicado es separar los datos por esos grupos y comprobar si, dentro de cada grupo, media y varianza se parecen.

</details>

**6.** Una landing recibe 400 visitas y cada una convierte con probabilidad 0.3. Calcula P(X ≤ 110) con la aproximación normal, con y sin corrección de continuidad, y compara con el valor exacto. ¿Se cumple la condición para aproximar?

<details>
<summary>✅ <b>Solución</b></summary>

La condición se cumple con holgura: $np = 120$ y $n(1 - p) = 280$, ambos muy por encima de 5. La normal aproximada es $Y \sim N(120, 84)$, con $\sigma = \sqrt{400 \cdot 0.3 \cdot 0.7} = \sqrt{84} \approx 9.17$.

- **Con corrección:** $P(Y \le 110.5) = P\left(Z \le \frac{110.5 - 120}{9.17}\right) = P(Z \le -1.04) \approx 0.150$.
- **Sin corrección:** $P(Y \le 110) \approx 0.138$.
- **Exacto:** `stats.binom.cdf(110, 400, 0.3)` $\approx 0.150$.

Con corrección de continuidad la aproximación coincide con el valor exacto hasta la tercera cifra; sin ella, el error es de más de un punto porcentual.

</details>

[← Variables aleatorias](variables.md) [Siguiente: Estadísticos →](estadisticos.md)
