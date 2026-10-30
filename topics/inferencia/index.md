# Estimación e inferencia

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/inferencia/>

En el tema de [variables aleatorias](../variables-aleatorias/index.md) trabajamos con distribuciones conocidas: "entran de media 4 clientes por hora", "el 2 % de las tarjetas tiene premio". En la práctica nadie nos da esos números. Tenemos **datos**, una muestra de unos cientos o miles de casos, y con ella queremos decir algo sobre todos los clientes, todos los pedidos o todas las visitas. Este tema responde a dos preguntas: **¿cómo estimamos lo que no conocemos?** y **¿cuánto podemos fiarnos de esa estimación?**

## El hilo del tema

1. **[Estimación](estimacion.md).** Distinguimos la **población**, que no conocemos, de la **muestra**, que sí podemos calcular. Con los **estimadores de máxima verosimilitud** estimamos los parámetros de un modelo a partir de los datos, y usamos el modelo estimado para tomar decisiones.
2. **[Cuánto se equivoca una estimación](variabilidad.md).** Una estimación hecha con una muestra varía de una muestra a otra. El **error estándar** mide cuánto, la **ley de los grandes números** dice hacia dónde va y el **teorema del límite central**, qué forma tiene su distribución.
3. **[Intervalos de confianza](intervalos.md).** Convertimos todo lo anterior en un rango de valores plausibles para el parámetro, y planificamos cuántos datos hacen falta.
4. **[Práctica guiada](practica.md).** Aplicamos todo, junto con los dos temas anteriores, a un caso real paso a paso. Después viene la entrega.

Cada página tiene ejemplos resueltos, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Distinguir los parámetros de una población de las estimaciones calculadas con una muestra, y calcular estas con la convención correcta ($n$ o $n - 1$).
- [ ] Estimar los parámetros de un modelo (Bernoulli, binomial, Poisson, normal, uniforme) con su estimador de máxima verosimilitud, usarlo para responder preguntas de negocio y comprobar si el modelo encaja.
- [ ] Explicar por qué una estimación hecha con una muestra tiene un margen de error, calcularlo con el error estándar y relacionarlo con la ley de los grandes números y el teorema del límite central.
- [ ] Construir e interpretar correctamente un intervalo de confianza para una media o una proporción, y planificar el tamaño de muestra.

## Antes de empezar

Necesitas los dos temas anteriores: [probabilidad](../probabilidad/index.md) y [variables aleatorias](../variables-aleatorias/index.md), en especial la [media y la varianza](../variables-aleatorias/variables.md#media-y-varianza-de-una-distribución) de una distribución, la [binomial](../variables-aleatorias/distribuciones.md#binomial-contar-éxitos) y la [distribución normal](../variables-aleatorias/distribuciones.md#normal-la-campana-de-gauss).

## Notación

| Símbolo | Significado |
|---|---|
| $\mu$, $\sigma^2$, $p$ (letras griegas) | parámetros de la **población**, desconocidos |
| $x_1, \ldots, x_n$ | una muestra de tamaño $n$ |
| $\bar{x}$, $s^2$, $\hat{p}$ | media, varianza y proporción de la **muestra** |
| $\hat{\mu}$, $\hat{\lambda}$ (con acento circunflejo) | estimaciones de un parámetro hechas con la muestra |

## Material complementario

El **cuaderno de la sesión** es la base de las páginas 1 y 2: simula las ventas de una campaña de email para estimar una probabilidad de compra, calcular probabilidades de negocio con la binomial y ver en acción la ley de los grandes números y el teorema del límite central. Los datos se generan sin semilla: cada ejecución da resultados distintos.

**Cuaderno de la sesión: ventas de una campaña de email** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/inferencia/notebook.ipynb) &nbsp; [descargar .ipynb](notebook.ipynb)

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
