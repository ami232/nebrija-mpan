# Probabilidad y variables aleatorias

> Versión web, con demos interactivas: <https://nebrija-mpan.alberpro.com/temas/variables-aleatorias/>

Una empresa nunca sabe con certeza cuántos clientes entrarán mañana, cuáles abandonarán su suscripción o cuánto gastará el próximo pedido. Y sin embargo tiene que decidir hoy cuánto personal contratar, a quién ofrecer un descuento o cuánto stock pedir. Este tema trata de cómo **razonar y decidir con incertidumbre** usando datos: es la base sobre la que se construye todo lo que viene después en el curso.

## El hilo del tema

Las seis páginas siguen un razonamiento que va de la idea más básica a la herramienta que usarás en la práctica:

1. **[Probabilidad](probabilidad.md).** Empezamos por el lenguaje para hablar de lo incierto: sucesos, probabilidades, cómo cambian cuando sabemos algo más (probabilidad condicionada) y cómo darles la vuelta (Bayes).
2. **[Variables aleatorias](variables.md).** Para calcular necesitamos números, así que traducimos los resultados inciertos a **variables aleatorias**. Su comportamiento se describe con una **distribución**, y con datos observamos versiones empíricas de ella.
3. **[Distribuciones](distribuciones.md).** Ciertas situaciones se repiten constantemente: contar éxitos, contar llegadas, medir algo que fluctúa. Cada una tiene una distribución "con nombre" (binomial, Poisson, normal…) que podemos usar como modelo.
4. **[Estadísticos](estadisticos.md).** Una distribución se resume con unos pocos números (media, varianza, cuantiles). Además distinguimos entre los de la **población**, que no conocemos, y los de la **muestra**, que sí podemos calcular.
5. **[Inferencia](inferencia.md).** Por último, la pregunta clave: con una muestra, ¿qué podemos decir de la población y con cuánta seguridad? El teorema del límite central y los intervalos de confianza responden a esto.
6. **[Práctica guiada](practica.md).** Aplicamos todo a un caso real paso a paso. Después viene la entrega.

Cada página tiene ejemplos resueltos, una demo interactiva, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Calcular probabilidades de sucesos a partir de una tabla de datos, incluidas las probabilidades condicionadas, y aplicar el teorema de Bayes.
- [ ] Describir una variable aleatoria con su función de probabilidad y su función de distribución, y estimarlas con datos.
- [ ] Elegir un modelo razonable (Bernoulli, binomial, Poisson, normal…) para una situación de negocio, justificar sus suposiciones y calcular probabilidades con `scipy.stats`.
- [ ] Calcular e interpretar media, varianza, mediana y cuantiles, y saber cuándo conviene cada uno.
- [ ] Explicar por qué una estimación hecha con una muestra tiene un margen de error, cuantificarlo con un intervalo de confianza e interpretarlo correctamente.

## Antes de empezar

No necesitas conocimientos previos de estadística. Basta con las matemáticas de bachillerato: fracciones, potencias y saber leer una fórmula con sumatorios. Las integrales aparecen en algún momento, pero solo hace falta entenderlas como "área bajo una curva".

Para el código necesitas lo básico de Python. Si nunca has usado NumPy, pandas o Matplotlib, haz antes este tutorial (1–2 h):

**Tutorial: Python para análisis estadístico** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/temas/variables-aleatorias/python.ipynb) &nbsp; [descargar .ipynb](python.ipynb)

## Notación

| Símbolo | Significado |
|---|---|
| $\Omega$, $A$, $B$ | espacio muestral (todos los resultados posibles) y sucesos |
| $P(A)$, $P(A \mid B)$ | probabilidad de $A$; probabilidad de $A$ sabiendo que ha ocurrido $B$ |
| $X$, $Y$ (mayúsculas) | variables aleatorias |
| $x$, $x_1, \ldots, x_n$ (minúsculas) | valores observados, una muestra de tamaño $n$ |
| $f(x)$, $F(x)$ | función de probabilidad o densidad; función de distribución $P(X \le x)$ |
| $X \sim \text{Binomial}(n, p)$ | "$X$ sigue una distribución binomial de parámetros $n$ y $p$" |
| $\mu = \mathbb{E}[X]$, $\sigma^2 = \mathbb{V}[X]$ | media y varianza de la **población** |
| $\bar{x}$, $s^2$ | media y varianza de la **muestra** |
| $\hat{p}$, $\hat{\mu}$ (con acento circunflejo) | estimaciones de un parámetro hechas con la muestra |

## Material complementario

La **[guía de referencia](../../referencia/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
