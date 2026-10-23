# Regresión lineal simple

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/regresion-lineal-simple/>

Pasamos de describir variables aisladas a **modelar la relación entre dos variables**: cuánto cambia $Y$ cuando cambia $X$, y cómo usar esa relación para predecir.

## Slides

📄 [Slides (PDF)](slides.pdf)

## Apuntes

### Dependencia, covarianza y correlación

Dos variables son **independientes** si $P(X, Y) = P(X)\,P(Y)$: conocer $X$ no dice nada sobre $Y$. Si no, son **dependientes** y $X$ ayuda a predecir $Y$.

La **covarianza** mide cómo varían conjuntamente:

$$\operatorname{Cov}(X, Y) = \mathbb{E}\big[(X - \mathbb{E}[X])(Y - \mathbb{E}[Y])\big], \qquad \widehat{\operatorname{Cov}}(X, Y) = \frac{1}{n} \sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})$$

Su magnitud depende de las unidades. Por eso usamos la **correlación de Pearson**, normalizada entre $-1$ y $1$:

$$\rho(X, Y) = \frac{\operatorname{Cov}(X, Y)}{\sigma_X \sigma_Y}, \qquad r = \frac{\widehat{\operatorname{Cov}}(X, Y)}{s_X s_Y}$$

> ⚠️ **Dos advertencias**
>
> - **Correlación no implica causalidad**: ver [relaciones espurias](https://www.tylervigen.com/spurious-correlations).
> - **Correlación cero no implica independencia**: $r$ solo mide relación *lineal*. El [Datasaurus Dozen](https://jumpingrivers.github.io/datasauRus/) tiene la misma media, varianza y correlación en conjuntos de datos completamente distintos. Visualiza siempre los datos.

### El modelo

$$Y = \beta_0 + \beta_1 X + \varepsilon \qquad\qquad \hat{Y} = \hat{\beta}_0 + \hat{\beta}_1 X$$

| Término | Nombre | Interpretación |
|---|---|---|
| $Y$ | variable dependiente (respuesta) | lo que queremos explicar o predecir |
| $X$ | variable independiente (predictor) | lo que usamos para explicar |
| $\beta_0$ | intercepto | valor esperado de $Y$ cuando $X = 0$ (no siempre tiene sentido práctico) |
| $\beta_1$ | pendiente | cambio esperado en $Y$ por cada unidad más de $X$ |
| $\varepsilon$ | error aleatorio | variabilidad natural, variables omitidas, errores de medición |

**Supuestos sobre $\varepsilon$:** media cero, varianza constante $\sigma^2$ (homocedasticidad), normalidad $\varepsilon \sim N(0, \sigma^2)$ e independencia.

### Estimación por mínimos cuadrados (OLS)

Elegimos $\hat\beta_0, \hat\beta_1$ que minimizan la suma de residuos al cuadrado (la **función de pérdida**):

$$\min_{\beta_0, \beta_1} \sum_{i=1}^{n} (y_i - \beta_0 - \beta_1 x_i)^2$$

La solución es

$$\hat{\beta}_1 = \frac{\widehat{\operatorname{Cov}}(X, Y)}{s_X^2} = r\,\frac{s_Y}{s_X}, \qquad \hat{\beta}_0 = \bar{y} - \hat{\beta}_1 \bar{x}$$

así que la recta siempre pasa por $(\bar x, \bar y)$ y el signo de la pendiente es el de la correlación.

### Residuos

$$e_i = y_i - \hat{y}_i, \qquad \sum_i e_i = 0, \qquad \sum_i x_i e_i = 0, \qquad s^2 = \frac{\sum_i e_i^2}{n - 2}$$

Los residuos sirven para **diagnosticar** el modelo (no deben mostrar patrones), **estimar** $\sigma^2$ y **detectar outliers**.

### Bondad de ajuste: $R^2$

$$R^2 = 1 - \frac{SS_\text{res}}{SS_\text{tot}} = 1 - \frac{\sum (y_i - \hat{y}_i)^2}{\sum (y_i - \bar{y})^2}$$

Es la proporción de la variabilidad de $Y$ que explica el modelo ($0 \le R^2 \le 1$). En regresión simple, $R^2 = r^2$.

### Ejemplo: publicidad y ventas

Diez semanas de inversión en publicidad ($X$) y ventas ($Y$), en miles de €. El modelo estimado es $\hat{Y} = 14.26 + 3.05\,X$, con $R^2 = 0.99$:

- cada 1000 € más de publicidad se asocian con 3050 € más de ventas, en promedio;
- con 25 000 € de inversión esperamos $\hat Y = 14.26 + 3.05 \times 25 \approx 90.4$ miles de €.

Prueba a cambiar los datos o el código: se ejecuta en tu navegador. Pulsa (abajo a la derecha) para ejecutar todas las celdas.

▶️ [Laboratorio interactivo: publicidad](https://alberpro.com/nebrija-mpan/topics/regresion-lineal-simple/lab_publicidad.html) (en la web)

## Notebooks

- **Ejercicios: covarianza, correlación y regresión simple** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/regresion-lineal-simple/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)
