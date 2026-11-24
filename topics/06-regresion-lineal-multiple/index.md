# 6. Regresión lineal múltiple

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/06-regresion-lineal-multiple/>

La regresión lineal simple explica una variable con otra: las ventas con la publicidad, el precio de una vivienda con su superficie. En la práctica casi nunca hay una sola causa. El precio de una vivienda depende de su superficie, pero también de su antigüedad y de lo lejos que esté del centro, y esas variables están relacionadas entre sí. Este tema extiende la recta a **varios predictores a la vez**: cómo se estima el modelo, cómo se interpreta cada coeficiente cuando las demás variables se mantienen fijas, qué pasa cuando los predictores están muy correlacionados, cómo se elige qué variables entran en el modelo y cómo se comprueba que el modelo es adecuado.

## El hilo del tema

Un mismo caso recorre todo el tema: una inmobiliaria que quiere predecir el precio de quince viviendas a partir de su superficie, su antigüedad y su distancia al centro.

1. **[El modelo y su interpretación](modelo.md).** El modelo $Y = \beta_0 + \beta_1 X_1 + \cdots + \beta_p X_p + \varepsilon$, cómo se estima por mínimos cuadrados y cómo se lee cada coeficiente "manteniendo constantes las demás variables". Predictores categóricos con variables *dummy*, interacciones y la lectura del `summary()` de statsmodels.
2. **[Multicolinealidad](multicolinealidad.md).** Qué pasa cuando los predictores están correlacionados entre sí: coeficientes inestables, errores estándar grandes y signos que contradicen la intuición. Cómo se detecta (matriz de correlaciones, VIF) y qué se puede hacer.
3. **[Selección de variables](seleccion.md).** Qué variables entran en el modelo: contrastes sobre cada coeficiente, el contraste F global, el $R^2$ ajustado, los criterios AIC y BIC, el conocimiento del negocio y la evaluación con datos nuevos.
4. **[Diagnóstico del modelo](diagnostico.md).** Cómo se comprueban las suposiciones con los residuos (gráficos y contrastes de statsmodels), cómo se detectan los puntos influyentes y qué hacer cuando una suposición no se cumple.
5. **[Práctica guiada](practica.md).** El análisis completo, paso a paso, con los notebooks de ejercicios: variables continuas, categóricas y ordinales, multicolinealidad, comparación de modelos y diagnóstico.

Cada página tiene ejemplos resueltos, código Python, un resumen ("Lo esencial") y ejercicios con la solución desarrollada. Sigue el orden: cada página usa lo anterior.

## Objetivos

Al terminar el tema deberías ser capaz de:

- [ ] Plantear un modelo de regresión lineal múltiple, estimarlo con Python e interpretar cada coeficiente en las unidades del problema, manteniendo constantes las demás variables.
- [ ] Explicar por qué un coeficiente puede cambiar de valor o de signo al añadir otras variables al modelo.
- [ ] Incluir predictores categóricos con variables *dummy* e interpretar sus coeficientes respecto a la categoría de referencia.
- [ ] Detectar la multicolinealidad con la matriz de correlaciones y el VIF, y decidir qué hacer con ella.
- [ ] Comparar modelos con los p-valores, el contraste F, el $R^2$ ajustado, el AIC y el BIC, y elegir uno justificando la decisión.
- [ ] Comprobar las suposiciones del modelo con los gráficos de residuos y los diagnósticos de statsmodels, e identificar observaciones influyentes.

## Antes de empezar

Este tema parte de la [regresión lineal simple](../05-regresion-lineal-simple/index.md): el [modelo y los mínimos cuadrados](../05-regresion-lineal-simple/modelo.md), y los [residuos, el $R^2$ y la inferencia sobre la pendiente](../05-regresion-lineal-simple/ajuste.md). También usa la [correlación](../05-regresion-lineal-simple/correlacion.md) y el [contraste de hipótesis](../04-contraste-hipotesis/index.md).

## Notación

| Símbolo | Significado |
|---|---|
| $n$, $p$ | número de observaciones y número de predictores |
| $x_{ij}$ | valor del predictor $j$ en la observación $i$ |
| $\beta_0, \beta_1, \ldots, \beta_p$ | intercepto y coeficientes reales, desconocidos |
| $\hat{\beta}_0, \hat{\beta}_1, \ldots, \hat{\beta}_p$ | sus estimaciones por mínimos cuadrados |
| $\hat{y}_i$; $e_i = y_i - \hat{y}_i$ | valor predicho y residuo de la observación $i$ |
| $R^2$, $R^2_\text{aj}$ | coeficiente de determinación y $R^2$ ajustado |
| $\text{VIF}_j$ | factor de inflación de la varianza del predictor $j$ |
| AIC, BIC | criterios de información para comparar modelos |

## Material complementario

El **notebook de la sesión** recorre el caso de las viviendas de principio a fin con statsmodels: de la regresión simple a la múltiple, la multicolinealidad, la comparación de modelos, el diagnóstico de residuos y la predicción.

**Notebook de la sesión: regresión múltiple con las viviendas** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/06-regresion-lineal-multiple/notebook.ipynb) &nbsp; [descargar .ipynb](notebook.ipynb)

Los **notebooks de ejercicios** son la base de la [práctica guiada](practica.md): el primero con datos simulados de viviendas (variables categóricas, VIF, interacciones, train/test) y el segundo con variables categóricas, ordinales y continuas.

- **Ejercicios: ajuste, diagnóstico e interpretación** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/06-regresion-lineal-multiple/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)
- **Aplicado: variables categóricas, ordinales y continuas** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/06-regresion-lineal-multiple/exercises_applied.ipynb) &nbsp; [descargar .ipynb](exercises_applied.ipynb)

La **[guía de referencia](../../reference/modelos-predictivos.md)** reúne las fórmulas de todo el curso en una página.

## Slides

Las **slides de clase** son el apoyo que usamos en el aula. Resumen el tema, pero no sustituyen a estas páginas.

📄 [Slides (PDF)](slides.pdf)
