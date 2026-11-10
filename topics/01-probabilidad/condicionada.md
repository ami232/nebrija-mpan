# 1.2 Probabilidad condicionada y teorema de Bayes

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/01-probabilidad/condicionada/>

> ℹ️ **Lo que necesitas antes**
>
> Qué es un suceso, cómo se combinan (unión, intersección, complementario), las reglas del complementario y de la unión, y cómo se leen probabilidades en una tabla de contingencia. Lo tienes en [1.1 Sucesos y reglas de la probabilidad](probabilidad.md).

## Por qué condicionar

En la página anterior calculamos que el 7 % de las visitas de la tienda online acaban en compra. Pero casi nunca tomamos decisiones sin saber nada de la visita: sabemos desde qué dispositivo entra, si viene de un email, si ya es cliente. La pregunta útil no es "¿qué probabilidad hay de que una visita cualquiera compre?", sino "¿qué probabilidad hay de que **esta** visita compre, sabiendo lo que sabemos de ella?".

Esta es probablemente la idea más importante del tema. Toda la estadística y el machine learning consisten, en el fondo, en calcular probabilidades **condicionadas a lo que sabemos**. Un modelo que predice si un cliente abandonará la empresa calcula la probabilidad de abandono condicionada a su antigüedad, su consumo y sus reclamaciones.

Esta página construye las herramientas en orden: primero la probabilidad condicionada y la regla del producto; después la independencia, que es el caso en que la información no cambia nada; y por último la ley de la probabilidad total y el teorema de Bayes, que permiten pasar de las probabilidades dentro de cada grupo a la probabilidad global y dar la vuelta a las preguntas.

## Probabilidad condicionada

Recuerda la tabla de las 1000 visitas de ayer:

| | Compra | No compra | Total |
|---|---|---|---|
| **Móvil** | 30 | 570 | 600 |
| **Ordenador** | 40 | 360 | 400 |
| **Total** | 70 | 930 | 1000 |

Si sabemos que la visita llega desde el móvil, ¿cuál es la probabilidad de que compre? Ya no nos interesan las 1000 visitas, solo las 600 de móvil, la primera fila de la tabla. De ellas, 30 compran:

$$P(C \mid M) = \frac{30}{600} = 0.05$$

Saber el dispositivo **cambia** la probabilidad: del 7 % general pasamos al 5 % en móvil. En ordenador sube al 10 % ($40/400$). Condicionar es **restringir el universo** a los casos en que se cumple lo que sabemos y recalcular las proporciones dentro de él.

Para pasar de recuentos a probabilidades, divide numerador y denominador entre el total de visitas: $30/600 = (30/1000)/(600/1000) = P(C \cap M)/P(M)$. Eso es la definición general.

> 📘 **Definición**
>
> La **probabilidad de $A$ condicionada a $B$** (se lee "probabilidad de $A$ dado $B$"), con $P(B) > 0$, es
> $$P(A \mid B) = \frac{P(A \cap B)}{P(B)}$$
>
> $B$ es lo que sabemos que ha ocurrido (la condición) y $A$, el suceso cuya probabilidad queremos. El numerador son los casos en que ocurren los dos y el denominador, todos los casos en que ocurre la condición.

Comprueba que coincide con el recuento: $P(C \cap M) / P(M) = 0.03 / 0.6 = 0.05$. Exigimos $P(B) > 0$ porque no tiene sentido condicionar a algo que no puede ocurrir.

Una vez fijada la condición, $P(\cdot \mid B)$ es una probabilidad como cualquier otra y cumple todas las reglas de la página anterior. Por ejemplo, la regla del complementario dentro de las visitas de móvil: $P(\bar{C} \mid M) = 1 - P(C \mid M) = 0.95$, que son las 570 de 600 que no compran.

### La regla del producto

Si despejamos la definición, obtenemos la **regla del producto**, muy útil para calcular probabilidades de que ocurran varias cosas a la vez:

$$P(A \cap B) = P(A \mid B)\, P(B)$$

Se lee como una secuencia: para que ocurran $A$ y $B$, primero tiene que ocurrir $B$, y después, sabiendo que ocurrió $B$, tiene que ocurrir $A$. Con más sucesos se encadena igual: $P(A \cap B \cap C) = P(A)\,P(B \mid A)\,P(C \mid A \cap B)$.

> ✏️ **Ejemplo resuelto: elegir dos clientes para una entrevista**
>
> Una empresa tiene 10 clientes grandes, de los cuales 3 son de un mismo sector. Elige al azar 2 de los 10 para una entrevista. ¿Qué probabilidad hay de que los dos sean de ese sector?
>
> Sea $A_1$ = "el primero es del sector" y $A_2$ = "el segundo es del sector". La primera elección es entre 10 clientes, 3 del sector: $P(A_1) = 3/10$. Si el primero es del sector, quedan 9 clientes y 2 del sector: $P(A_2 \mid A_1) = 2/9$. Por la regla del producto,
>
> $$P(A_1 \cap A_2) = P(A_1)\,P(A_2 \mid A_1) = \frac{3}{10} \cdot \frac{2}{9} = \frac{6}{90} = \frac{1}{15} \approx 0.067$$
>
> La segunda probabilidad es condicionada porque la primera elección cambia la composición del grupo. Si eligiéramos con reemplazo (el mismo cliente podría salir dos veces), sería $3/10 \cdot 3/10 = 0.09$.

> ⚠️ **$P(A \mid B)$ no es $P(B \mid A)$**
>
> La probabilidad de que una visita de móvil compre, $P(C \mid M) = 0.05$, no tiene nada que ver con la probabilidad de que una compra venga del móvil, $P(M \mid C) = 30/70 \approx 0.43$. Los denominadores son poblaciones distintas: visitas de móvil en un caso, compras en el otro. Confundirlas es uno de los errores más frecuentes, y tiene consecuencias serias en medicina, en justicia y en la evaluación de modelos. Ante cualquier proporción, pregúntate: ¿proporción **de qué**?

## Independencia

A veces saber algo no aporta nada. Si la probabilidad de compra fuera la misma en móvil que en ordenador, conocer el dispositivo no nos ayudaría a predecir la compra. Ese es el caso que llamamos independencia.

> 📘 **Definición**
>
> $A$ y $B$ son **independientes** si saber que ocurrió uno no cambia la probabilidad del otro: $P(A \mid B) = P(A)$. Sustituyendo en la definición de probabilidad condicionada, es equivalente a
> $$P(A \cap B) = P(A)\, P(B)$$

La segunda forma es simétrica (no importa cuál de los dos sucesos es la condición) y vale también si alguno tiene probabilidad cero. Por eso es la que se suele usar como definición.

En la tienda, comprar y el dispositivo **no** son independientes: $P(C \mid M) = 0.05$ es distinto de $P(C) = 0.07$. También se ve con la segunda forma de la definición: $P(C)\,P(M) = 0.07 \cdot 0.6 = 0.042$, distinto de $P(C \cap M) = 0.03$. Esto ya es información de negocio: la experiencia de compra en móvil convierte peor, y quizá merece una revisión.

Con datos reales, las proporciones casi nunca coinciden exactamente aunque haya independencia, porque los datos tienen ruido. ¿Cuánta diferencia es "suficiente" para afirmar que no hay independencia? Responder a esa pregunta es el objetivo del [contraste de hipótesis](../04-contraste-hipotesis/index.md).

### La independencia como suposición

La independencia también es una **suposición** que hacemos al modelizar, y es muy útil porque permite multiplicar. Si los sucesos $A_1, A_2, \ldots, A_n$ son independientes, la probabilidad de que ocurran todos es el producto de sus probabilidades:

$$P(A_1 \cap A_2 \cap \cdots \cap A_n) = P(A_1)\,P(A_2) \cdots P(A_n)$$

Cuando digamos que las visitas son independientes entre sí, estaremos suponiendo, por ejemplo, que un usuario no influye en la compra de otro. Siempre conviene preguntarse si es razonable: durante una campaña viral, o cuando varios miembros de una misma empresa visitan la web, podría no serlo.

> ✏️ **Ejemplo resuelto: al menos una compra en tres visitas**
>
> La tasa de conversión de una web es 0.07 por visita. Si las visitas son independientes, ¿cuál es la probabilidad de que de 3 visitas al menos una compre?
>
> "Al menos una" es más fácil por el complementario: "ninguna compra". Cada visita no compra con probabilidad $1 - 0.07 = 0.93$ y, por independencia, multiplicamos: $P(\text{ninguna}) = 0.93^3 \approx 0.804$. Por tanto, $P(\text{al menos una}) = 1 - 0.804 \approx 0.196$.
>
> Un error típico es sumar $0.07 + 0.07 + 0.07 = 0.21$. Los sucesos "compra la visita $i$" no son incompatibles (pueden comprar varias), así que la unión no es una suma. Con 15 visitas, la suma daría $1.05$, una probabilidad imposible, mientras que el cálculo correcto da $1 - 0.93^{15} \approx 0.66$.

> ⚠️ **Independientes no es lo mismo que incompatibles**
>
> Son conceptos casi opuestos. Si $A$ y $B$ son incompatibles y ocurre $A$, sabemos con certeza que **no** ocurre $B$, así que saber uno cambia muchísimo la probabilidad del otro. Dos sucesos incompatibles (con probabilidad positiva) nunca son independientes: $P(A \cap B) = 0$, pero $P(A)\,P(B) > 0$.

## Ley de la probabilidad total

A menudo conocemos la probabilidad de un suceso **dentro de cada grupo** y queremos la probabilidad global. Es la situación típica cuando los datos vienen por segmentos de clientes, canales o regiones.

Una tienda online envía un email promocional a sus clientes. Por campañas anteriores sabe que el 20 % de los clientes abre el email. De los que lo abren, el 19 % compra en la semana siguiente; de los que no lo abren, compra el 4 % (compran igualmente, por otros canales). ¿Qué proporción de clientes comprará?

Lo más claro es imaginar 10 000 clientes y seguir las ramas:

| Grupo | Clientes | Compran | No compran |
|---|---|---|---|
| Abren el email (20 %) | 2000 | $2000 \cdot 0.19 = 380$ | 1620 |
| No lo abren (80 %) | 8000 | $8000 \cdot 0.04 = 320$ | 7680 |
| Total | 10 000 | 700 | 9300 |

Compran $380 + 320 = 700$ de 10 000, un 7 %. Con probabilidades, cada rama es una regla del producto, $P(C \cap E) = P(C \mid E)\,P(E)$, donde $E$ es "abre el email", y la compra global es la suma de las ramas:

$$P(C) = P(C \mid E)\,P(E) + P(C \mid \bar{E})\,P(\bar{E}) = 0.19 \cdot 0.2 + 0.04 \cdot 0.8 = 0.038 + 0.032 = 0.07$$

> 📘 **Ley de la probabilidad total**
>
> Si los sucesos $B_1, \ldots, B_k$ son incompatibles entre sí y entre todos cubren $\Omega$ (cada caso está en exactamente un grupo), para cualquier suceso $A$
> $$P(A) = P(A \mid B_1)\,P(B_1) + \cdots + P(A \mid B_k)\,P(B_k)$$

La deducción es la que acabamos de hacer con la tabla: $A$ se parte en los trozos incompatibles $A \cap B_1, \ldots, A \cap B_k$, se suman sus probabilidades (axioma 3) y cada una se escribe con la regla del producto.

La interpretación es sencilla: la probabilidad global es la **media de las probabilidades de cada grupo, ponderada por el tamaño del grupo**. Por eso queda siempre entre la menor y la mayor de las probabilidades de los grupos, y más cerca de la del grupo grande. En la tienda, aunque los que abren el email compran mucho, la compra global (7 %) está más cerca del 4 % porque cuatro de cada cinco clientes no lo abren. Con los dispositivos pasa lo mismo: $P(C) = 0.05 \cdot 0.6 + 0.10 \cdot 0.4 = 0.07$.

## Teorema de Bayes

El **teorema de Bayes** responde a la pregunta inversa. En la campaña de email conocemos $P(C \mid E)$, la probabilidad de comprar si se abre el email. Pero cuando entra una compra, el equipo de marketing quiere saber $P(E \mid C)$: ¿qué probabilidad hay de que ese cliente hubiera abierto el email? Es decir, ¿qué parte de las compras se puede relacionar con que el email se leyó?

En la tabla de 10 000 clientes la respuesta es inmediata: de las 700 compras, 380 son de clientes que abrieron el email, $380/700 \approx 0.54$. Para obtener una fórmula general, basta con escribir la regla del producto de las dos formas posibles, $P(A \cap B) = P(A \mid B)P(B) = P(B \mid A)P(A)$, y despejar:

> 📘 **Teorema de Bayes**
>
> $$P(B \mid A) = \frac{P(A \mid B)\, P(B)}{P(A)}$$
>
> $P(B)$ es la probabilidad **a priori**, antes de saber nada. $P(B \mid A)$ es la probabilidad **a posteriori**, una vez sabemos que ocurrió $A$. El denominador $P(A)$ se calcula casi siempre con la ley de la probabilidad total.

En la campaña, $P(E \mid C) = 0.19 \cdot 0.2 / 0.07 \approx 0.54$. Antes de saber nada, la probabilidad de que un cliente haya abierto el email es del 20 %; saber que ha comprado la eleva al 54 %. Con los dispositivos: si acaba de entrar una compra, la probabilidad de que venga de un ordenador es $P(O \mid C) = 0.10 \cdot 0.4 / 0.07 \approx 0.57$. Aunque solo el 40 % de las visitas son de ordenador, el 57 % de las compras lo son.

Bayes describe cómo **actualizar** una probabilidad con información nueva: partimos de la probabilidad a priori, observamos algo y obtenemos la probabilidad a posteriori. Esa probabilidad a posteriori puede servir como nueva probabilidad a priori si llega más información (el ejercicio 6 lo aplica).

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
> Con la fórmula se llega a lo mismo. Con $F$ = fraude y $A$ = alerta, y el denominador calculado con la ley de la probabilidad total:
>
> $$P(F \mid A) = \frac{P(A \mid F)\,P(F)}{P(A \mid F)\,P(F) + P(A \mid \bar F)\,P(\bar F)} = \frac{0.95 \cdot 0.01}{0.95 \cdot 0.01 + 0.05 \cdot 0.99} \approx 0.16$$
>
> **Solo el 16 % de las alertas son fraudes.** El sistema no es malo: el problema es que el fraude es tan raro que hasta un 5 % de falsas alarmas sobre una población enorme de transacciones legítimas genera muchas más alertas que fraudes. Ignorar la probabilidad a priori es la **falacia de la tasa base**. Cuando evalúes modelos de clasificación, esta cantidad se llamará *precision* ([regresión logística](../07-regresion-logistica/index.md)).

El ejemplo enseña dos cosas que conviene recordar. La primera: $P(A \mid F) = 0.95$ y $P(F \mid A) = 0.16$ son preguntas distintas, y es la segunda la que interesa a quien recibe la alerta. La segunda: las **frecuencias naturales** (imaginar un número concreto de casos y repartirlos) hacen estos cálculos mucho más fáciles de seguir que la fórmula, y son la mejor forma de explicar el resultado a alguien que no conoce el teorema.

## Errores frecuentes

- **Confundir $P(A \mid B)$ con $P(B \mid A)$.** Identifica siempre cuál es el denominador: ¿proporción **de qué**?
- **Ignorar la tasa base.** Ante un suceso raro, incluso un indicador bastante bueno da muchos falsos positivos.
- **Confundir independientes con incompatibles.** Los sucesos incompatibles son muy dependientes: si ocurre uno, el otro no.
- **Multiplicar probabilidades sin comprobar la independencia.** $P(A \cap B) = P(A)\,P(B)$ solo vale si los sucesos son independientes; en general, hay que usar $P(A \mid B)\,P(B)$.
- **Promediar probabilidades de grupos sin ponderar.** La probabilidad global no es $(0.19 + 0.04)/2$, sino la media ponderada por el tamaño de cada grupo.
- **Leer una dependencia como causa.** Que las compras sean más frecuentes entre los que abren el email no demuestra que el email cause las compras: quizá lo abren los clientes que ya pensaban comprar. Lo veremos en el [tema de sesgos](../12-sesgos/index.md).

## En Python

Con pandas, una tabla de contingencia y sus probabilidades condicionadas salen en una línea. El argumento `normalize` decide el denominador: todas las visitas, cada fila o cada columna.

```python
import pandas as pd

# Reconstruimos las 1000 visitas de la tabla
visitas = pd.DataFrame({
    "dispositivo": ["móvil"] * 600 + ["ordenador"] * 400,
    "compra": ["sí"] * 30 + ["no"] * 570 + ["sí"] * 40 + ["no"] * 360,
})

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

Para comprobar la independencia, comparamos la probabilidad de la intersección con el producto de las probabilidades:

```python
movil = visitas.dispositivo == "móvil"
compra = visitas.compra == "sí"

print("P(C ∩ M)  =", round((movil & compra).mean(), 3))           # 0.03
```

```text
P(C ∩ M)  = 0.03
```

```python
print("P(C)·P(M) =", round(compra.mean() * movil.mean(), 3))      # 0.042: no son independientes
```

```text
P(C)·P(M) = 0.042
```

```python
print("P(C | M)  =", round(compra[movil].mean(), 3))              # 0.05: filtramos las de móvil
```

```text
P(C | M)  = 0.05
```

La ley de la probabilidad total y el teorema de Bayes son dos líneas de código. Con los datos de la alerta de fraude:

```python
p_fraude = 0.01
p_alerta_si_fraude = 0.95
p_alerta_si_legitima = 0.05

p_alerta = p_alerta_si_fraude * p_fraude + p_alerta_si_legitima * (1 - p_fraude)   # probabilidad total
p_fraude_si_alerta = p_alerta_si_fraude * p_fraude / p_alerta                       # Bayes
print(round(p_alerta, 4), round(p_fraude_si_alerta, 3))          # 0.059 y 0.161
```

```text
0.059 0.161
```

Y para comprobar un cálculo por simulación (útil cuando dudes de una fórmula), generamos un millón de transacciones y miramos qué proporción de las alertas son fraudes:

```python
import numpy as np

rng = np.random.default_rng(1)
n = 1_000_000
fraude = rng.random(n) < p_fraude
alerta = np.where(fraude, rng.random(n) < p_alerta_si_fraude, rng.random(n) < p_alerta_si_legitima)

print(round(fraude[alerta].mean(), 3))   # ≈ 0.161: proporción de fraudes entre las alertas
```

```text
0.162
```

> ✅ **Lo esencial**
>
> - Condicionar es **restringir el universo** a lo que sabemos: $P(A \mid B) = P(A \cap B)/P(B)$. De ahí sale la regla del producto, $P(A \cap B) = P(A \mid B)\,P(B)$.
> - $P(A \mid B)$ y $P(B \mid A)$ son cosas distintas: tienen denominadores distintos.
> - $A$ y $B$ son **independientes** si saber uno no cambia la probabilidad del otro: $P(A \cap B) = P(A)\,P(B)$. Es una suposición de modelización que conviene justificar.
> - La **ley de la probabilidad total** calcula la probabilidad global como media de las probabilidades de cada grupo, ponderada por su tamaño.
> - El **teorema de Bayes** invierte las condicionadas y actualiza la probabilidad a priori con información nueva; el resultado depende mucho de la probabilidad a priori (**tasa base**).

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

**2.** Dos sucesos $A$ y $B$ tienen $P(A) = 0.3$ y $P(B) = 0.5$. Calcula $P(A \cup B)$ en dos casos: si son incompatibles y si son independientes.

<details>
<summary>✅ <b>Solución</b></summary>

- **Incompatibles:** $P(A \cap B) = 0$, así que $P(A \cup B) = 0.3 + 0.5 = 0.8$.
- **Independientes:** $P(A \cap B) = 0.3 \cdot 0.5 = 0.15$, así que $P(A \cup B) = 0.3 + 0.5 - 0.15 = 0.65$.

Los resultados son distintos porque las dos situaciones son distintas: en la primera, los sucesos nunca ocurren a la vez; en la segunda, ocurren a la vez en el 15 % de los casos.

</details>

**3.** El 30 % de los usuarios de una app son premium y el 40 % la usa a diario. Si ambas cosas fueran independientes, ¿qué porcentaje sería premium y de uso diario? En los datos observas un 25 %. ¿Qué sugiere?

<details>
<summary>✅ <b>Solución</b></summary>

Con independencia, $P(\text{premium} \cap \text{diario}) = 0.3 \cdot 0.4 = 0.12$, un 12 %. Observar un 25 %, más del doble, sugiere una asociación positiva: los premium usan la app a diario mucho más que el resto. En concreto, $P(\text{diario} \mid \text{premium}) = 0.25/0.3 \approx 0.83$, frente al 40 % general.

</details>

**4.** Una tienda online divide a sus clientes en tres segmentos: nuevos (50 % de los clientes), habituales (30 %) y frecuentes (20 %). Tras un email promocional compra el 3 % de los nuevos, el 8 % de los habituales y el 15 % de los frecuentes. ¿Qué proporción de clientes compra? Si entra una compra, ¿qué probabilidad hay de que sea de un cliente frecuente?

<details>
<summary>✅ <b>Solución</b></summary>

Los tres segmentos son incompatibles y cubren a todos los clientes, así que por la ley de la probabilidad total:

$$P(C) = 0.03 \cdot 0.5 + 0.08 \cdot 0.3 + 0.15 \cdot 0.2 = 0.015 + 0.024 + 0.030 = 0.069$$

Por Bayes, $P(\text{frecuente} \mid C) = 0.030 / 0.069 \approx 0.43$. Los clientes frecuentes son solo el 20 % de la base, pero generan el 43 % de las compras de la campaña. Con frecuencias naturales sobre 1000 clientes: 15 compras de nuevos, 24 de habituales y 30 de frecuentes; $30/69 \approx 0.43$.

</details>

**5.** Una prueba de control de calidad revisa piezas de una línea de producción en la que el 2 % de las piezas sale defectuosa. La prueba marca como defectuosas el 90 % de las piezas que lo son y, por error, el 5 % de las piezas correctas. Si una pieza sale marcada, ¿qué probabilidad hay de que sea defectuosa? Hazlo con frecuencias naturales y con la fórmula.

<details>
<summary>✅ <b>Solución</b></summary>

**Frecuencias naturales** sobre 10 000 piezas:

- 200 son defectuosas y la prueba marca el 90 %: **180 marcas correctas**.
- 9 800 son correctas y la prueba marca el 5 %: **490 marcas erróneas**.
- $P(\text{defectuosa} \mid \text{marcada}) = 180 / (180 + 490) \approx 0.27$.

**Fórmula:**

$$\frac{0.9 \cdot 0.02}{0.9 \cdot 0.02 + 0.05 \cdot 0.98} \approx 0.27$$

Tres de cada cuatro piezas marcadas son en realidad correctas, porque los defectos son poco frecuentes. Por eso, antes de desechar una pieza marcada, conviene una segunda revisión.

</details>

**6.** En el ejemplo de la alerta de fraude, el banco añade un segundo sistema de alertas independiente del primero y con las mismas tasas. Si saltan las dos alertas, ¿cuál es ahora la probabilidad de fraude? Calcúlalo de dos formas: con las dos alertas a la vez y actualizando dos veces con Bayes.

<details>
<summary>✅ <b>Solución</b></summary>

**Las dos alertas a la vez.** Por independencia (dentro de cada grupo), $P(\text{dos alertas} \mid F) = 0.95^2 = 0.9025$ y $P(\text{dos alertas} \mid \bar F) = 0.05^2 = 0.0025$. Aplicando Bayes:

$$P(F \mid \text{dos alertas}) = \frac{0.9025 \cdot 0.01}{0.9025 \cdot 0.01 + 0.0025 \cdot 0.99} \approx 0.78$$

**Actualizando dos veces.** Tras la primera alerta, la probabilidad de fraude es 0.161. La usamos como probabilidad a priori para la segunda:

$$\frac{0.95 \cdot 0.161}{0.95 \cdot 0.161 + 0.05 \cdot 0.839} \approx 0.78$$

Los dos caminos coinciden. Combinar evidencias independientes cambia muchísimo la conclusión: del 16 % pasamos al 78 %. Muchos modelos de clasificación funcionan precisamente combinando señales.

</details>

**7.** Un analista observa que, entre los clientes que se dieron de baja el último año, el 60 % había llamado al servicio de atención al cliente, y concluye que "un cliente que llama tiene un 60 % de probabilidad de darse de baja". Si el 10 % de los clientes se da de baja y el 20 % llama al servicio de atención, ¿cuál es la probabilidad correcta?

<details>
<summary>✅ <b>Solución</b></summary>

El 60 % es $P(\text{llama} \mid \text{baja})$, y el analista lo ha leído como $P(\text{baja} \mid \text{llama})$, el error de invertir la condicionada. Por Bayes:

$$P(\text{baja} \mid \text{llama}) = \frac{P(\text{llama} \mid \text{baja})\,P(\text{baja})}{P(\text{llama})} = \frac{0.6 \cdot 0.1}{0.2} = 0.3$$

Un cliente que llama tiene un 30 % de probabilidad de darse de baja: la mitad de lo que decía el analista, aunque sigue siendo el triple de la tasa general del 10 %. Llamar es una señal de riesgo útil, pero no tan fuerte como parecía.

</details>

[← Sucesos y reglas de la probabilidad](probabilidad.md) [Siguiente tema: Variables aleatorias →](../02-variables-aleatorias/index.md)
