# Estimación e inferencia

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/inferencia/>

En el tema de [variables aleatorias](../variables-aleatorias/index.md) trabajamos con distribuciones conocidas: "entran de media 4 clientes por hora", "el 2 % de las tarjetas tiene premio". En la práctica nadie nos da esos números. Tenemos **datos**, una muestra de unos cientos o miles de casos, y con ella queremos decir algo sobre todos los clientes, todos los pedidos o todas las visitas. Este tema responde a dos preguntas: **¿cómo estimamos lo que no conocemos?** y **¿cuánto podemos fiarnos de esa estimación?**

## El hilo del tema

1. **[Muestras y estimación](estimacion.md).** Distinguimos la **población**, que no conocemos, de la **muestra**, que sí podemos calcular. Con el **método de los momentos** estimamos los parámetros de una distribución a partir de los datos.
2. **[Intervalos de confianza](intervalos.md).** Una estimación hecha con una muestra varía de una muestra a otra. El **error estándar** y el **teorema del límite central** cuantifican cuánto, y los **intervalos de confianza** lo convierten en un rango de valores plausibles.
3. **[Práctica guiada](practica.md).** Aplicamos todo, junto con los dos temas anteriores, a un caso real paso a paso. Después viene la entrega.

Cada página tiene ejemplos resueltos, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Distinguir los parámetros de una población de los estadísticos de una muestra, y calcular estos con la convención correcta ($n$ o $n - 1$).
- [ ] Estimar los parámetros de un modelo (Poisson, normal, Bernoulli…) con el método de los momentos y comprobar si el modelo encaja.
- [ ] Explicar por qué una estimación hecha con una muestra tiene un margen de error, y calcularlo con el error estándar.
- [ ] Construir e interpretar correctamente un intervalo de confianza para una media o una proporción, y planificar el tamaño de muestra.

## Antes de empezar

Necesitas los dos temas anteriores: [probabilidad](../probabilidad/index.md) y [variables aleatorias](../variables-aleatorias/index.md), en especial la [distribución normal](../variables-aleatorias/distribuciones.md#normal-la-campana-de-gauss) y las [propiedades de la media y la varianza](../variables-aleatorias/estadisticos.md#combinar-variables-propiedades-de-la-media-y-la-varianza).

## Notación

| Símbolo | Significado |
|---|---|
| $\mu$, $\sigma^2$, $p$ (letras griegas) | parámetros de la **población**, desconocidos |
| $x_1, \ldots, x_n$ | una muestra de tamaño $n$ |
| $\bar{x}$, $s^2$, $\hat{p}$ | media, varianza y proporción de la **muestra** |
| $\hat{\mu}$, $\hat{\lambda}$ (con acento circunflejo) | estimaciones de un parámetro hechas con la muestra |

## Material complementario

El **cuaderno de la sesión** simula las ventas de una campaña de email para estimar una probabilidad de compra, calcular probabilidades de negocio con la binomial y ver en acción la ley de los grandes números y el teorema del límite central. Los datos se generan sin semilla: cada ejecución da resultados distintos.

**Cuaderno de la sesión: ventas de una campaña de email** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/inferencia/notebook.ipynb) &nbsp; [descargar .ipynb](notebook.ipynb)

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
