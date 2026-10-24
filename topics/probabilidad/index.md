# Probabilidad

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/probabilidad/>

Una empresa nunca sabe con certeza cuántos clientes entrarán mañana, cuáles abandonarán su suscripción o cuánto gastará el próximo pedido. Y sin embargo tiene que decidir hoy cuánto personal contratar, a quién ofrecer un descuento o cuánto stock pedir. Con este tema empieza el curso: es el lenguaje para **razonar con incertidumbre**, y todo lo que viene después se apoya en él.

Imagina que gestionas una tienda online. Ayer hubo 1000 visitas y 70 acabaron en compra. ¿Cuál es la probabilidad de que la próxima visita compre? ¿Cambia esa probabilidad si sabemos que el visitante entra desde el móvil? Si una compra acaba de entrar, ¿qué probabilidad hay de que viniera de un ordenador? Estas preguntas, que aparecen a diario en cualquier negocio, son preguntas de **probabilidad**. Este tema te da el lenguaje y las reglas para responderlas con rigor.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Describir una situación incierta con sucesos y combinarlos (unión, intersección, complementario).
- [ ] Calcular probabilidades de sucesos a partir de una tabla de datos, incluidas las probabilidades condicionadas.
- [ ] Decidir si dos sucesos son independientes y explicar qué significa para el negocio.
- [ ] Aplicar la ley de la probabilidad total y el teorema de Bayes, y reconocer la falacia de la tasa base.

## Antes de empezar

No necesitas conocimientos previos de estadística: basta con las matemáticas de bachillerato. Usaremos algo de notación de conjuntos ($\cup$, $\cap$), que se explica sobre la marcha.

Para el código necesitas lo básico de Python. Si nunca has usado NumPy, pandas o Matplotlib, haz antes este tutorial (1–2 h). Lo usarás en todo el curso:

**Tutorial: Python para análisis estadístico** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/probabilidad/python.ipynb) &nbsp; [descargar .ipynb](python.ipynb)

| Símbolo | Significado |
|---|---|
| $\Omega$, $A$, $B$ | espacio muestral (todos los resultados posibles) y sucesos |
| $A \cup B$, $A \cap B$, $\bar{A}$ | unión ($A$ o $B$), intersección ($A$ y $B$), complementario (no $A$) |
| $P(A)$, $P(A \mid B)$ | probabilidad de $A$; probabilidad de $A$ sabiendo que ha ocurrido $B$ |

El tema siguiente, [variables aleatorias](../variables-aleatorias/index.md), traduce estos sucesos a números.

## Por qué necesitamos la probabilidad

La mayor parte de las cosas que interesan en un negocio no se pueden predecir con certeza. No sabemos si un cliente concreto comprará, cuántas llamadas entrarán mañana en el call center ni si un préstamo se devolverá. Lo que sí podemos saber, observando muchos casos, es **con qué frecuencia** ocurre cada cosa. Y eso basta para tomar buenas decisiones: dimensionar un equipo, fijar un precio o decidir a qué clientes llamar.

La probabilidad es el lenguaje matemático para hablar de esa incertidumbre. Una probabilidad de 0.07 significa que, si repitiéramos la situación muchas veces, el suceso ocurriría aproximadamente en el 7 % de ellas. Esta lectura como **frecuencia a largo plazo** es la que usaremos en todo el curso y la que conecta la probabilidad con los datos.

## El lenguaje: experimentos, resultados y sucesos

Para razonar con probabilidades primero hay que describir con precisión **qué** es incierto.

> 📘 **Definición**
>
> - Un **experimento aleatorio** es cualquier proceso cuyo resultado no se puede predecir con certeza, aunque sepamos qué resultados son posibles. Por ejemplo, observar una visita a la web y ver si compra.
> - El **espacio muestral** $\Omega$ (omega) es el conjunto de todos los resultados posibles del experimento.
> - Un **suceso** es un conjunto de resultados: cualquier afirmación sobre el resultado de la que podamos decir si se cumple o no. Se nombran con letras mayúsculas: $A$, $B$, $C$…

En la tienda, el experimento es "llega una visita". Si nos interesan el dispositivo y si compra, el espacio muestral tiene cuatro resultados:

$$\Omega = \{(\text{móvil}, \text{compra}),\ (\text{móvil}, \text{no compra}),\ (\text{ordenador}, \text{compra}),\ (\text{ordenador}, \text{no compra})\}$$

"La visita compra" es un suceso, $C$, formado por dos de esos resultados. "La visita llega desde el móvil" es otro suceso, $M$.

Elegir $\Omega$ es ya una **decisión de modelización**: podríamos haber incluido también el navegador, la hora o el importe. Conviene incluir lo que necesitamos para responder la pregunta y nada más.

Los sucesos se combinan igual que los conjuntos, y cada operación tiene una traducción directa al lenguaje cotidiano:

| Notación | Se lee | Ocurre cuando… | En la tienda |
|---|---|---|---|
| $A \cup B$ | $A$ o $B$ (unión) | ocurre al menos uno de los dos | entra desde el móvil **o** compra |
| $A \cap B$ | $A$ y $B$ (intersección) | ocurren los dos a la vez | entra desde el móvil **y** compra |
| $\bar{A}$ | no $A$ (complementario) | no ocurre $A$ | **no** compra |
| $A - B$ | $A$ pero no $B$ (diferencia) | ocurre $A$ y no ocurre $B$ | entra desde el móvil y **no** compra |

Dos sucesos son **incompatibles** (o disjuntos) si no pueden ocurrir a la vez, es decir, si $A \cap B = \emptyset$. Por ejemplo, "entra desde el móvil" y "entra desde el ordenador" son incompatibles. El suceso $\Omega$ se llama **suceso seguro** y el conjunto vacío $\emptyset$, **suceso imposible**.

## Asignar probabilidades

Una probabilidad es un número entre 0 y 1 asociado a cada suceso. Hay tres formas habituales de obtenerlo, y en la práctica las combinarás todas.

**1. Por simetría (regla de Laplace).** Si todos los resultados son igual de probables, basta con contar:

$$P(A) = \frac{\text{número de resultados en } A}{\text{número total de resultados}}$$

Es lo que hacemos con dados, monedas o sorteos. Al lanzar dos dados hay 36 pares $(d_1, d_2)$ igual de probables. Seis suman 7, así que $P(\text{suma} = 7) = 6/36 = 1/6$. La regla **solo** vale con resultados equiprobables. Las once sumas posibles (de 2 a 12) no lo son, y por eso $P(\text{suma} = 7)$ no es $1/11$.

**2. Por frecuencias observadas.** Casi siempre en los negocios, la probabilidad se estima con datos. Esta es la tabla de las 1000 visitas de ayer:

| | Compra | No compra | Total |
|---|---|---|---|
| **Móvil** | 30 | 570 | 600 |
| **Ordenador** | 40 | 360 | 400 |
| **Total** | 70 | 930 | 1000 |

Con ella estimamos $P(C) = 70/1000 = 0.07$ y $P(M) = 600/1000 = 0.6$. Una tabla así, que cruza dos variables, se llama **tabla de contingencia**. Volverá a aparecer muchas veces en el curso.

**3. Por un modelo.** A veces la probabilidad sale de un modelo matemático con unas suposiciones, como las [distribuciones con nombre propio](../variables-aleatorias/distribuciones.md) del próximo tema. Otras veces sale del juicio de un experto, cuando no hay datos.

Sea cual sea el origen, toda asignación de probabilidades debe cumplir tres reglas básicas, los **axiomas de Kolmogórov**:

> 📘 **Axiomas de la probabilidad**
>
> 1. $P(A) \ge 0$ para cualquier suceso $A$.
> 2. $P(\Omega) = 1$: algún resultado ocurre seguro.
> 3. Si $A$ y $B$ son incompatibles, $P(A \cup B) = P(A) + P(B)$.

De estas tres reglas se deduce todo lo demás. Por ejemplo, como $A$ y $\bar{A}$ son incompatibles y juntos forman $\Omega$, se cumple $P(A) + P(\bar{A}) = 1$. De ahí sale la **regla del complementario**, que usarás constantemente:

$$P(\bar{A}) = 1 - P(A)$$

Esta regla es especialmente útil para sucesos del tipo "al menos uno". Calcular directamente "al menos una de tres visitas compra" obliga a sumar muchos casos. En cambio, su complementario, "ninguna compra", es un solo caso.

### Probabilidad de la unión

¿Qué proporción de visitas llega desde el móvil **o** compra? Si sumamos $P(M) + P(C) = 0.6 + 0.07$, las 30 visitas que son de móvil **y** compran se cuentan dos veces. Hay que restarlas una vez:

$$P(A \cup B) = P(A) + P(B) - P(A \cap B)$$

En la tienda, $P(M \cup C) = 0.6 + 0.07 - 0.03 = 0.64$. Compruébalo en la tabla: 600 visitas de móvil más las 40 de ordenador que compran, 640 de 1000. Si los sucesos son incompatibles, $P(A \cap B) = 0$ y recuperamos el axioma 3.

## Probabilidad condicionada

Esta es probablemente la idea más importante del tema. Toda la estadística y el machine learning consisten, en el fondo, en calcular probabilidades **condicionadas a lo que sabemos**.

Volvamos a la pregunta inicial: si sabemos que la visita llega desde el móvil, ¿cuál es la probabilidad de que compre? Ya no nos interesan las 1000 visitas, solo las 600 de móvil. De ellas, 30 compran:

$$P(C \mid M) = \frac{30}{600} = 0.05$$

Saber el dispositivo **cambia** la probabilidad: del 7 % general pasamos al 5 % en móvil. En ordenador sube al 10 % ($40/400$). Condicionar es **restringir el universo** a los casos en que se cumple lo que sabemos y recalcular las proporciones dentro de él.

> 📘 **Definición**
>
> La **probabilidad de $A$ condicionada a $B$** (se lee "probabilidad de $A$ dado $B$"), con $P(B) > 0$, es
> $$P(A \mid B) = \frac{P(A \cap B)}{P(B)}$$

Comprueba que coincide con el recuento: $P(C \cap M) / P(M) = 0.03 / 0.6 = 0.05$. Si despejamos, obtenemos la **regla del producto**, muy útil para calcular probabilidades de que ocurran varias cosas a la vez:

$$P(A \cap B) = P(A \mid B)\, P(B)$$

> ⚠️ **$P(A \mid B)$ no es $P(B \mid A)$**
>
> La probabilidad de que una visita de móvil compre, $P(C \mid M) = 0.05$, no tiene nada que ver con la probabilidad de que una compra venga del móvil, $P(M \mid C) = 30/70 \approx 0.43$. Los denominadores son poblaciones distintas: visitas de móvil en un caso, compras en el otro. Confundirlas es uno de los errores más frecuentes, y tiene consecuencias serias en medicina, en justicia y en la evaluación de modelos.

## Independencia

Dos sucesos son **independientes** cuando saber que ocurrió uno no cambia la probabilidad del otro.

> 📘 **Definición**
>
> $A$ y $B$ son **independientes** si $P(A \mid B) = P(A)$, o lo que es equivalente,
> $$P(A \cap B) = P(A)\, P(B)$$

En la tienda, comprar y el dispositivo **no** son independientes: $P(C \mid M) = 0.05$ es distinto de $P(C) = 0.07$. También se ve con la segunda forma de la definición: $P(C)\,P(M) = 0.07 \cdot 0.6 = 0.042$, distinto de $P(C \cap M) = 0.03$. Esto ya es información de negocio: la experiencia de compra en móvil convierte peor, y quizá merece una revisión.

Con datos reales, las proporciones casi nunca coinciden exactamente aunque haya independencia, porque los datos tienen ruido. ¿Cuánta diferencia es "suficiente" para afirmar que no hay independencia? Responder a esa pregunta es el objetivo del [contraste de hipótesis](../contraste-hipotesis/index.md).

La independencia también es una **suposición** que hacemos al modelizar. Cuando digamos que las visitas son independientes entre sí, estaremos suponiendo, por ejemplo, que un usuario no influye en la compra de otro. Siempre conviene preguntarse si es razonable: durante una campaña viral, por ejemplo, podría no serlo.

> ⚠️ **Independientes no es lo mismo que incompatibles**
>
> Son conceptos casi opuestos. Si $A$ y $B$ son incompatibles y ocurre $A$, sabemos con certeza que **no** ocurre $B$, así que saber uno cambia muchísimo la probabilidad del otro. Dos sucesos incompatibles (con probabilidad positiva) nunca son independientes.

## Probabilidad total y teorema de Bayes

A menudo conocemos la probabilidad de un suceso **dentro de cada grupo** y queremos la probabilidad global. Si los grupos $B_1, \ldots, B_k$ no se solapan y cubren todos los casos, la probabilidad global es la media de las probabilidades de cada grupo, ponderada por el tamaño del grupo:

$$P(A) = P(A \mid B_1)\,P(B_1) + \cdots + P(A \mid B_k)\,P(B_k)$$

Es la **ley de la probabilidad total**. En la tienda: $P(C) = 0.05 \cdot 0.6 + 0.10 \cdot 0.4 = 0.07$.

El **teorema de Bayes** responde a la pregunta inversa: sabiendo que ha ocurrido $A$, ¿qué probabilidad hay de que viniera del grupo $B$? Basta con escribir la regla del producto de las dos formas posibles, $P(A \cap B) = P(A \mid B)P(B) = P(B \mid A)P(A)$, y despejar:

> 📘 **Teorema de Bayes**
>
> $$P(B \mid A) = \frac{P(A \mid B)\, P(B)}{P(A)}$$
>
> $P(B)$ es la probabilidad **a priori**, antes de saber nada. $P(B \mid A)$ es la probabilidad **a posteriori**, una vez sabemos que ocurrió $A$. El denominador $P(A)$ se calcula casi siempre con la ley de la probabilidad total.

En la tienda, si acaba de entrar una compra, la probabilidad de que venga de un ordenador es $P(O \mid C) = 0.10 \cdot 0.4 / 0.07 \approx 0.57$. Aunque solo el 40 % de las visitas son de ordenador, el 57 % de las compras lo son.

> ✏️ **Ejemplo resuelto: ¿cuánto vale una alerta de fraude?**
>
> El 1 % de las transacciones de un banco son fraudulentas. El sistema de alertas detecta el 95 % de los fraudes, pero también salta, por error, en el 5 % de las transacciones legítimas. Salta una alerta: ¿qué probabilidad hay de que sea un fraude?
>
> La intuición suele decir "alrededor del 95 %". Hagamos las cuentas con **frecuencias naturales**: imagina 10 000 transacciones.
>
> - Son fraude el 1 %, es decir, 100. El sistema detecta el 95 % de ellas: **95 alertas verdaderas**.
> - Son legítimas 9 900. El sistema salta en el 5 % de ellas: **495 alertas falsas**.
> - En total hay $95 + 495 = 590$ alertas, y solo 95 son fraude: $95/590 \approx 0.16$.
>
> Con la fórmula se llega a lo mismo. Con $F$ = fraude y $A$ = alerta:
>
> $$P(F \mid A) = \frac{P(A \mid F)\,P(F)}{P(A \mid F)\,P(F) + P(A \mid \bar F)\,P(\bar F)} = \frac{0.95 \cdot 0.01}{0.95 \cdot 0.01 + 0.05 \cdot 0.99} \approx 0.16$$
>
> **Solo el 16 % de las alertas son fraudes.** El sistema no es malo: el problema es que el fraude es tan raro que hasta un 5 % de falsas alarmas sobre una población enorme de transacciones legítimas genera muchas más alertas que fraudes. Ignorar la probabilidad a priori es la **falacia de la tasa base**. Cuando evalúes modelos de clasificación, esta cantidad se llamará *precision* ([regresión logística](../regresion-logistica/index.md)).

## Frecuencias y probabilidades: la ley de los grandes números

Hemos estimado probabilidades con frecuencias observadas, como el 70/1000. ¿Por qué es legítimo? Porque, cuando un experimento se repite muchas veces de forma independiente, la **frecuencia relativa** de un suceso se acerca cada vez más a su probabilidad. Es la **ley de los grandes números**.

La ley tiene una consecuencia práctica importante: con pocas repeticiones, la frecuencia puede alejarse bastante de la probabilidad. Una estimación basada en 20 visitas es mucho menos fiable que una basada en 1000. En el tema de [inferencia](../inferencia/intervalos.md) aprenderemos a cuantificar exactamente cuánto.

Experimenta con dos dados: con pocos lanzamientos, las frecuencias saltan mucho; con miles, se pegan a la probabilidad teórica.

▶️ [Demo interactiva: dados](https://alberpro.com/nebrija-mpan/topics/probabilidad/demo_dados.html) (en la web)

## En Python

Con pandas, una tabla de contingencia y sus probabilidades condicionadas salen en una línea:

```python
import pandas as pd

# Reconstruimos las 1000 visitas de la tabla
visitas = pd.DataFrame({
    "dispositivo": ["móvil"] * 600 + ["ordenador"] * 400,
    "compra": ["sí"] * 30 + ["no"] * 570 + ["sí"] * 40 + ["no"] * 360,
})

pd.crosstab(visitas.dispositivo, visitas.compra, margins=True)        # recuentos
```

```text
compra        no  sí   All
dispositivo               
móvil        570  30   600
ordenador    360  40   400
All          930  70  1000
```

```python
pd.crosstab(visitas.dispositivo, visitas.compra, normalize="all")     # P(A ∩ B)
```

```text
compra         no    sí
dispositivo            
móvil        0.57  0.03
ordenador    0.36  0.04
```

```python
pd.crosstab(visitas.dispositivo, visitas.compra, normalize="index")   # P(compra | dispositivo)
```

```text
compra         no    sí
dispositivo            
móvil        0.95  0.05
ordenador    0.90  0.10
```

```python
pd.crosstab(visitas.dispositivo, visitas.compra, normalize="columns") # P(dispositivo | compra)
```

```text
compra           no      sí
dispositivo                
móvil        0.6129  0.4286
ordenador    0.3871  0.5714
```

```python
(visitas.compra == "sí").mean()                                       # P(compra) = 0.07
```

```text
0.07
```

Y para comprobar un cálculo por simulación (útil cuando dudes de una fórmula):

```python
import numpy as np

rng = np.random.default_rng(42)
dados = rng.integers(1, 7, size=(100_000, 2))     # 100 000 lanzamientos de dos dados
print((dados.sum(axis=1) == 7).mean())            # ≈ 1/6 ≈ 0.167
```

```text
0.16827
```

## Errores frecuentes

- **Aplicar Laplace sin resultados equiprobables.** Antes de "contar casos", pregúntate si todos son igual de probables.
- **Confundir $P(A \mid B)$ con $P(B \mid A)$.** Identifica siempre cuál es el denominador: ¿proporción **de qué**?
- **Sumar probabilidades de sucesos que se solapan.** La unión solo es una suma si los sucesos son incompatibles.
- **Confundir independientes con incompatibles.** Ver la advertencia de la sección de independencia.
- **Ignorar la tasa base.** Ante un suceso raro, incluso un indicador bastante bueno da muchos falsos positivos.

> ✅ **Lo esencial**
>
> - La probabilidad mide la frecuencia con la que esperamos que ocurra un suceso si repetimos la situación muchas veces. Con datos, se estima con frecuencias observadas, y la estimación mejora con más datos.
> - Las reglas básicas son tres: complementario, $P(\bar{A}) = 1 - P(A)$; unión, $P(A \cup B) = P(A) + P(B) - P(A \cap B)$; y producto, $P(A \cap B) = P(A \mid B)\,P(B)$.
> - Condicionar es restringir el universo a lo que sabemos. $P(A \mid B)$ y $P(B \mid A)$ son cosas distintas.
> - $A$ y $B$ son independientes si saber uno no cambia la probabilidad del otro: $P(A \cap B) = P(A)\,P(B)$.
> - Bayes permite invertir condicionadas, pero el resultado depende mucho de la probabilidad a priori.

## Ejercicios

**1.** Una encuesta a 500 clientes recoge si están satisfechos y si han vuelto a comprar: 300 están satisfechos y, de ellos, 240 volvieron a comprar. De los 200 insatisfechos, 40 volvieron. Calcula P(volver), P(volver | satisfecho) y P(satisfecho | volvió). ¿Son independientes la satisfacción y volver a comprar?

<details>
<summary>✅ <b>Solución</b></summary>

Organiza los datos en una tabla de contingencia:

| | Vuelve | No vuelve | Total |
|---|---|---|---|
| Satisfecho | 240 | 60 | 300 |
| Insatisfecho | 40 | 160 | 200 |
| Total | 280 | 220 | 500 |

- $P(\text{volver}) = 280/500 = 0.56$.
- $P(\text{volver} \mid \text{satisfecho}) = 240/300 = 0.8$: restringimos a los 300 satisfechos.
- $P(\text{satisfecho} \mid \text{volvió}) = 240/280 \approx 0.86$: restringimos a los 280 que volvieron. Es una pregunta distinta y da otro número.
- No son independientes: $P(\text{volver} \mid \text{satisfecho}) = 0.8 \ne P(\text{volver}) = 0.56$. La satisfacción está asociada a la recompra.

</details>

**2.** La tasa de conversión de una web es 0.07 por visita. Si las visitas son independientes, ¿cuál es la probabilidad de que de 3 visitas al menos una compre?

<details>
<summary>✅ <b>Solución</b></summary>

"Al menos una" es más fácil por el complementario: "ninguna compra". Cada visita no compra con probabilidad $1 - 0.07 = 0.93$ y, por independencia, multiplicamos: $P(\text{ninguna}) = 0.93^3 \approx 0.804$. Por tanto, $P(\text{al menos una}) = 1 - 0.804 \approx 0.196$.

Un error típico es sumar $0.07 + 0.07 + 0.07 = 0.21$. Los sucesos "compra la visita $i$" no son incompatibles (pueden comprar varias), así que la unión no es una suma.

</details>

**3.** El 30 % de los usuarios de una app son premium y el 40 % la usa a diario. Si ambas cosas fueran independientes, ¿qué porcentaje sería premium y de uso diario? En los datos observas un 25 %. ¿Qué sugiere?

<details>
<summary>✅ <b>Solución</b></summary>

Con independencia, $P(\text{premium} \cap \text{diario}) = 0.3 \cdot 0.4 = 0.12$, un 12 %. Observar un 25 %, más del doble, sugiere una asociación positiva: los premium usan la app a diario mucho más que el resto. En concreto, $P(\text{diario} \mid \text{premium}) = 0.25/0.3 \approx 0.83$, frente al 40 % general.

</details>

**4.** Una prueba de detección de una enfermedad que afecta al 2 % de la población tiene una sensibilidad del 90 % (da positivo en el 90 % de los enfermos) y una especificidad del 95 % (da negativo en el 95 % de los sanos). Si una persona da positivo, ¿qué probabilidad hay de que esté enferma? Hazlo con frecuencias naturales y con la fórmula.

<details>
<summary>✅ <b>Solución</b></summary>

**Frecuencias naturales** sobre 10 000 personas:

- 200 están enfermas y el 90 % da positivo: **180 positivos verdaderos**.
- 9 800 están sanas y el 5 % da positivo: **490 positivos falsos**.
- $P(\text{enfermo} \mid +) = 180 / (180 + 490) \approx 0.27$.

**Fórmula:**

$$\frac{0.9 \cdot 0.02}{0.9 \cdot 0.02 + 0.05 \cdot 0.98} \approx 0.27$$

Tres de cada cuatro positivos son falsos, porque la enfermedad es poco frecuente. Por eso los positivos de un cribado se confirman con una segunda prueba.

</details>

**5.** En el ejercicio del fraude, el banco añade un segundo sistema de alertas independiente del primero y con las mismas tasas. Si saltan las dos alertas, ¿cuál es ahora la probabilidad de fraude?

<details>
<summary>✅ <b>Solución</b></summary>

Por independencia (dentro de cada grupo), $P(\text{dos alertas} \mid F) = 0.95^2 = 0.9025$ y $P(\text{dos alertas} \mid \bar F) = 0.05^2 = 0.0025$. Aplicando Bayes:

$$P(F \mid \text{dos alertas}) = \frac{0.9025 \cdot 0.01}{0.9025 \cdot 0.01 + 0.0025 \cdot 0.99} \approx 0.78$$

Combinar evidencias independientes cambia muchísimo la conclusión: del 16 % pasamos al 78 %. Muchos modelos de clasificación funcionan precisamente combinando señales.

</details>

## Slides

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a esta página.

📄 [Slides (PDF)](slides.pdf)

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

[Siguiente tema: Variables aleatorias →](../variables-aleatorias/index.md)
