# 1. Probabilidad

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/01-probabilidad/>

Una empresa nunca sabe con certeza cuántos clientes entrarán mañana, cuáles abandonarán su suscripción o cuánto gastará el próximo pedido. Y sin embargo tiene que decidir hoy cuánto personal contratar, a quién ofrecer un descuento o cuánto stock pedir. Con este tema empieza el curso: es el lenguaje para **razonar con incertidumbre**, y todo lo que viene después se apoya en él.

Imagina que gestionas una tienda online. Ayer hubo 1000 visitas y 70 acabaron en compra. ¿Cuál es la probabilidad de que la próxima visita compre? ¿Cambia esa probabilidad si sabemos que el visitante entra desde el móvil? Si una compra acaba de entrar, ¿qué probabilidad hay de que viniera de un ordenador? Estas preguntas, que aparecen a diario en cualquier negocio, son preguntas de **probabilidad**. Este tema te da el lenguaje y las reglas para responderlas con rigor.

## El hilo del tema

Las dos páginas siguen un razonamiento que va de describir la incertidumbre a actualizarla con información:

1. **[Sucesos y reglas de la probabilidad](probabilidad.md).** Primero hay que describir con precisión qué es incierto: experimentos, resultados y **sucesos**, y cómo se combinan. Después, cómo se asigna una probabilidad (contando casos, con datos o con un modelo), las reglas que debe cumplir y por qué las frecuencias observadas se acercan a las probabilidades cuando hay muchos datos.
2. **[Probabilidad condicionada y teorema de Bayes](condicionada.md).** En la práctica casi siempre sabemos algo: el dispositivo de la visita, si el cliente abrió el email, si saltó una alerta. La **probabilidad condicionada** incorpora esa información, la **independencia** describe cuándo no aporta nada, y la **probabilidad total** y el **teorema de Bayes** permiten combinar grupos e invertir las preguntas.

Cada página tiene ejemplos resueltos, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: la segunda página usa lo de la primera.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Describir una situación incierta con sucesos y combinarlos (unión, intersección, complementario).
- [ ] Asignar probabilidades contando casos igual de probables o con frecuencias observadas, y aplicar las reglas del complementario y de la unión.
- [ ] Calcular probabilidades condicionadas a partir de una tabla de datos y distinguir $P(A \mid B)$ de $P(B \mid A)$.
- [ ] Decidir si dos sucesos son independientes y explicar qué significa para el negocio.
- [ ] Aplicar la ley de la probabilidad total y el teorema de Bayes, y reconocer la falacia de la tasa base.

## Antes de empezar

No necesitas conocimientos previos de estadística: basta con las matemáticas de bachillerato. Usaremos algo de notación de conjuntos ($\cup$, $\cap$), que se explica sobre la marcha.

Para el código necesitas lo básico de Python. Si nunca has usado NumPy, pandas o Matplotlib, haz antes este tutorial (1–2 h). Lo usarás en todo el curso:

**Tutorial: Python para análisis estadístico** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/01-probabilidad/python.ipynb) &nbsp; [descargar .ipynb](python.ipynb)

## Notación

| Símbolo | Significado |
|---|---|
| $\Omega$ | espacio muestral: todos los resultados posibles del experimento |
| $A$, $B$, $C$ (mayúsculas) | sucesos |
| $A \cup B$, $A \cap B$, $\bar{A}$ | unión ($A$ o $B$), intersección ($A$ y $B$), complementario (no $A$) |
| $\emptyset$ | suceso imposible (conjunto vacío) |
| $P(A)$ | probabilidad de $A$ |
| $P(A \mid B)$ | probabilidad de $A$ sabiendo que ha ocurrido $B$ ("$A$ dado $B$") |

El tema siguiente, [variables aleatorias](../02-variables-aleatorias/index.md), traduce estos sucesos a números.

## Material complementario

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
