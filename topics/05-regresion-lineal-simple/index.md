# 5. Regresión lineal simple

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/05-regresion-lineal-simple/>

Hasta ahora hemos estudiado variables de una en una: su distribución, cómo estimar sus parámetros y cómo contrastar hipótesis sobre ellos. Las preguntas de negocio más habituales, sin embargo, relacionan **dos** variables: ¿cuánto aumentan las ventas por cada euro invertido en publicidad?, ¿cómo afecta la temperatura a la demanda de helados?, ¿qué salario cabe esperar con diez años de experiencia? Este tema enseña a **medir** la relación entre dos variables y a **modelarla** con una recta, para cuantificar cuánto cambia una cuando cambia la otra y usar esa relación para predecir.

## El hilo del tema

Un mismo caso recorre todo el tema: una empresa que ha anotado durante diez semanas su inversión en publicidad y sus ventas.

1. **[Dependencia, covarianza y correlación](correlacion.md).** Qué significa que dos variables sean dependientes y cómo se mide su relación lineal con la **covarianza** y la **correlación**. Por qué hay que dibujar siempre los datos y por qué correlación no implica causalidad.
2. **[El modelo y mínimos cuadrados](modelo.md).** El modelo de **regresión lineal simple**, $Y = \beta_0 + \beta_1 X + \varepsilon$, sus suposiciones y cómo se estima la recta por **mínimos cuadrados**. Cómo se interpretan la pendiente y el intercepto, cómo se predice y qué pasa cuando el predictor es una categoría.
3. **[Residuos, R² e inferencia](ajuste.md).** Cuánto se equivoca la recta (**residuos** y error típico), qué parte de la variabilidad explica (**$R^2$**), cómo se comprueban las suposiciones con los gráficos de residuos y cómo se contrasta si la pendiente es distinta de cero y se calcula su intervalo de confianza.
4. **[Práctica guiada](practica.md).** El análisis completo, paso a paso, con cuatro casos y distintos tipos de predictor: cantidades, categorías con dos valores y categorías ordenadas.

Cada página tiene ejemplos resueltos, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Explicar qué significa que dos variables sean dependientes, calcular e interpretar su covarianza y su correlación, y reconocer los límites de la correlación (relaciones no lineales, valores atípicos, causalidad).
- [ ] Plantear un modelo de regresión lineal simple, enunciar sus suposiciones y estimar la recta por mínimos cuadrados, a mano y con Python.
- [ ] Interpretar la pendiente y el intercepto en las unidades del problema, hacer predicciones y distinguir cuándo una predicción es una extrapolación.
- [ ] Calcular los residuos, el error típico de la regresión y el $R^2$, y usar los gráficos de residuos para comprobar si el modelo es adecuado.
- [ ] Contrastar si la pendiente es distinta de cero y construir e interpretar su intervalo de confianza.
- [ ] Usar como predictor una variable categórica binaria u ordinal e interpretar los coeficientes resultantes.

## Antes de empezar

Necesitas la [media y la varianza](../02-variables-aleatorias/variables.md#media-y-varianza-de-una-distribución) de una variable y la [distribución normal](../02-variables-aleatorias/distribuciones.md#normal-la-campana-de-gauss), del tema 2. Para la última parte de la página 3, el [error estándar](../03-inferencia/variabilidad.md) y los [intervalos de confianza](../03-inferencia/intervalos.md) del tema 3 y el [contraste de hipótesis](../04-contraste-hipotesis/index.md) del tema 4. Para el código, si nunca has usado NumPy, pandas o Matplotlib, haz antes el [tutorial de Python](../01-probabilidad/index.md).

## Notación

| Símbolo | Significado |
|---|---|
| $(x_i, y_i)$, $i = 1, \ldots, n$ | los $n$ pares de datos observados |
| $\bar{x}$, $\bar{y}$; $s_X$, $s_Y$ | medias y desviaciones típicas de la muestra |
| $\operatorname{Cov}(X, Y)$, $\rho$; $\widehat{\operatorname{Cov}}(X, Y)$, $r$ | covarianza y correlación de la población; sus estimaciones con la muestra |
| $\beta_0$, $\beta_1$ | intercepto y pendiente de la recta real, desconocidos |
| $\hat{\beta}_0$, $\hat{\beta}_1$ | sus estimaciones por mínimos cuadrados |
| $\hat{y}_i = \hat{\beta}_0 + \hat{\beta}_1 x_i$ | valor predicho (ajustado) para la observación $i$ |
| $\varepsilon$; $e_i = y_i - \hat{y}_i$ | error aleatorio del modelo; residuo de la observación $i$ |
| $SS_\text{res}$, $SS_\text{tot}$, $R^2$ | suma de residuos al cuadrado, variabilidad total de $Y$, coeficiente de determinación |

## Material complementario

El **notebook de ejercicios** es la base de la [práctica guiada](practica.md): covarianza, correlación y regresión con la publicidad, la temperatura y los helados, y salarios con predictores categóricos y ordinales.

**Ejercicios: covarianza, correlación y regresión simple** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/05-regresion-lineal-simple/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

El **lab de publicidad**, dentro de la página [El modelo y mínimos cuadrados](modelo.md#predecir-dentro-y-fuera-del-rango-de-los-datos), permite cambiar los datos y ver cómo cambian la recta y los residuos, en el navegador.

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
