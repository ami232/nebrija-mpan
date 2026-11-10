# 4. Contraste de hipótesis

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/04-contraste-hipotesis/>

En el tema de [estimación e inferencia](../03-inferencia/index.md) aprendimos a estimar un parámetro con una muestra y a acompañarlo de un intervalo de confianza. Muchas preguntas de negocio, sin embargo, piden una **decisión**: ¿es cierta la cifra que nos dio un proveedor? ¿Funciona mejor la nueva versión de la web que la antigua? ¿Ha reducido el nuevo sistema el tiempo de preparación de los pedidos? El **contraste de hipótesis** es el procedimiento para responder a estas preguntas con datos, sabiendo con qué probabilidad nos podemos equivocar.

## El hilo del tema

Seguimos con la campaña de email del tema anterior: una tienda online envía cada día un email promocional a 500 clientes, y tenemos los datos de ocho semanas de campaña.

1. **[La lógica del contraste](logica.md).** La agencia que diseñó el email prometió que compraría el 8 % de los clientes. Con esa afirmación construimos la lógica completa: hipótesis nula y alternativa, estadístico de contraste, nivel de significación, valor crítico, **p-valor** y contrastes bilaterales y unilaterales.
2. **[Contrastes para una muestra](una-muestra.md).** Contrastes para una **proporción** y para una **media**, la **t de Student** para cuando la desviación típica se estima con los datos, y la relación entre contrastes e **intervalos de confianza**.
3. **[Comparar dos grupos](dos-muestras.md).** Dos proporciones (¿compran más los lunes?), dos medias independientes (un test A/B) y muestras **pareadas** (los mismos empleados antes y después de un cambio).
4. **[Errores y potencia](errores-potencia.md).** Los errores de tipo I y II, la **potencia** de un contraste, cómo calcular el tamaño de muestra necesario y los usos incorrectos más frecuentes: confundir significativo con importante y hacer muchos contrastes a la vez.
5. **[Práctica guiada](practica.md).** Cuatro escenarios de negocio paso a paso, cada uno con un contraste distinto. Después viene la entrega.

Cada página tiene ejemplos resueltos, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Traducir una pregunta de negocio a una hipótesis nula y una alternativa, y decidir si el contraste es bilateral o unilateral.
- [ ] Calcular e interpretar correctamente un estadístico de contraste, un valor crítico y un p-valor, y tomar una decisión con un nivel de significación dado.
- [ ] Elegir y aplicar el contraste adecuado para una proporción, una media (Z o t), dos proporciones, dos medias independientes y dos medias pareadas, con `scipy.stats`.
- [ ] Relacionar un contraste con el intervalo de confianza correspondiente y usar los dos para comunicar un resultado.
- [ ] Explicar los errores de tipo I y II y la potencia, planificar el tamaño de muestra de un test A/B y distinguir la significación estadística de la relevancia práctica.

## Antes de empezar

Necesitas el tema de [estimación e inferencia](../03-inferencia/index.md), sobre todo el [error estándar](../03-inferencia/variabilidad.md), el teorema del límite central y los [intervalos de confianza](../03-inferencia/intervalos.md). También la [distribución normal](../02-variables-aleatorias/distribuciones.md#normal-la-campana-de-gauss) y las funciones `cdf`, `sf` y `ppf` de [`scipy.stats`](../02-variables-aleatorias/distribuciones.md#en-python-scipystats).

## Notación

| Símbolo | Significado |
|---|---|
| $H_0$, $H_1$ | hipótesis nula y alternativa |
| $p_0$, $\mu_0$ | valor del parámetro según la hipótesis nula |
| $\alpha$ | nivel de significación: probabilidad de rechazar $H_0$ si es cierta (error de tipo I) |
| $\beta$, $1 - \beta$ | probabilidad del error de tipo II y potencia del contraste |
| $z$, $t$ | estadísticos de contraste que siguen una normal estándar o una t de Student si $H_0$ es cierta |
| $t_{n-1}$ | distribución t de Student con $n - 1$ grados de libertad |
| $z_{\alpha/2}$, $t_{\alpha/2,\,n-1}$ | valores críticos: dejan una probabilidad $\alpha/2$ en la cola derecha |

## Material complementario

Los **ejercicios del tema** son la base de la [práctica guiada](practica.md): cuatro escenarios de negocio (tiempo de entrega, dos campañas de marketing, un test A/B y una encuesta de satisfacción antes y después).

**Ejercicios: contraste de hipótesis en escenarios de negocio** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/04-contraste-hipotesis/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

La **entrega 2** aplica el tema a un estudio de mercado para el lanzamiento de un producto.

**Entrega 2: lanzamiento de producto** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/04-contraste-hipotesis/assignment.ipynb) &nbsp; [descargar .ipynb](assignment.ipynb)

La entrega se sube en Blackboard: descarga tu copia desde Colab (*Archivo → Descargar → .ipynb*).

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
