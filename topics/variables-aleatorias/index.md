# Variables aleatorias y distribuciones

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/variables-aleatorias/>

En el [tema de probabilidad](../probabilidad/index.md) aprendimos a hablar de sucesos inciertos: "el cliente compra", "la visita llega desde el móvil". Pero muchas preguntas de negocio son **numéricas**: cuántos artículos tendrá un pedido, cuántas llamadas entrarán en una hora, cuánto costará una promoción. Este tema traduce la incertidumbre a números, describe su comportamiento con una **distribución** y la resume con unas pocas cifras (media, varianza, cuantiles) que sirven para decidir.

## El hilo del tema

Las dos páginas siguen un razonamiento que va de la idea a la herramienta:

1. **[Variables aleatorias](variables.md).** Para calcular necesitamos números, así que traducimos los resultados inciertos a **variables aleatorias**. Su comportamiento se describe con una **distribución**, que se resume con su media, su varianza y sus cuantiles. Con datos observamos versiones empíricas de ella.
2. **[Distribuciones](distribuciones.md).** Ciertas situaciones se repiten constantemente: contar éxitos, contar llegadas, medir algo que fluctúa. Cada una tiene una distribución "con nombre" (uniforme, Bernoulli, binomial, Poisson, normal) que podemos usar como modelo.

Cada página tiene ejemplos resueltos, una demo interactiva, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Describir una variable aleatoria con su función de probabilidad y su función de distribución, y estimarlas con datos.
- [ ] Calcular e interpretar la media, la varianza, la mediana y los cuantiles de una distribución, y saber cuándo conviene la media y cuándo la mediana.
- [ ] Elegir un modelo razonable (uniforme, Bernoulli, binomial, Poisson, normal) para una situación de negocio, justificar sus suposiciones y calcular probabilidades con `scipy.stats`.

## Antes de empezar

Necesitas el [tema de probabilidad](../probabilidad/index.md): sucesos, reglas básicas e independencia. No hace falta saber integrar: las probabilidades de una variable continua son áreas bajo una curva, y las calcula Python. Quien conozca el cálculo integral encontrará las fórmulas en recuadros opcionales "Para saber más". Para el código, si nunca has usado NumPy, pandas o Matplotlib, haz antes el [tutorial de Python](../probabilidad/index.md#antes-de-empezar).

## Notación

| Símbolo | Significado |
|---|---|
| $X$, $Y$ (mayúsculas) | variables aleatorias |
| $x$ (minúscula) | un valor concreto que puede tomar la variable |
| $f(x)$, $F(x)$ | función de probabilidad o densidad; función de distribución $P(X \le x)$ |
| $X \sim \text{Binomial}(n, p)$ | "$X$ sigue una distribución binomial de parámetros $n$ y $p$" |
| $\mu = \mathbb{E}[X]$, $\sigma^2 = \mathbb{V}[X]$ | media y varianza de la variable |

## Material complementario

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
