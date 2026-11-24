# 6.1 El modelo y su interpretación

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/06-regresion-lineal-multiple/modelo/>

> ℹ️ **Lo que necesitas antes**
>
> El [modelo de regresión lineal simple](../05-regresion-lineal-simple/modelo.md) y la [estimación por mínimos cuadrados](../05-regresion-lineal-simple/modelo.md#estimación-por-mínimos-cuadrados), los [residuos y el $R^2$](../05-regresion-lineal-simple/ajuste.md), y el [contraste](../05-regresion-lineal-simple/ajuste.md#el-contraste-de-la-pendiente) y el [intervalo de confianza](../05-regresion-lineal-simple/ajuste.md#el-intervalo-de-confianza-de-la-pendiente) de la pendiente. Para la sección de categorías, cómo se usa [un predictor binario](../05-regresion-lineal-simple/modelo.md#cuando-el-predictor-es-una-categoría).

## Los límites de la regresión simple

Una agencia inmobiliaria quiere un modelo para tasar pisos en una ciudad. Ha reunido los datos de 15 viviendas vendidas: su superficie (m²), su antigüedad (años), su distancia al centro (km) y el precio de venta (miles de €).

| Vivienda | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Superficie (m²) | 75 | 90 | 100 | 85 | 95 | 70 | 110 | 80 | 88 | 92 | 78 | 105 | 82 | 96 | 89 |
| Antigüedad (años) | 19 | 17 | 22 | 17 | 8 | 5 | 22 | 12 | 15 | 19 | 5 | 21 | 11 | 15 | 12 |
| Distancia (km) | 2.0 | 3.0 | 1.5 | 2.5 | 5.0 | 6.0 | 1.0 | 4.0 | 3.5 | 2.2 | 5.5 | 1.8 | 4.5 | 3.2 | 3.8 |
| Precio (miles €) | 211 | 250 | 293 | 238 | 229 | 157 | 320 | 197 | 234 | 247 | 176 | 295 | 194 | 251 | 223 |

Con lo que ya sabemos, lo primero es una regresión simple del precio sobre la superficie. Sale $\widehat{\text{Precio}} = -103.4 + 3.80 \times \text{Superficie}$, con $R^2 = 0.885$ y un error típico de 15.8 miles de €: cada metro cuadrado más se asocia con unos 3800 € más de precio. No está mal, pero tiene dos problemas.

El primero es que **se equivoca demasiado** para tasar. Las viviendas 4 y 13 tienen casi la misma superficie (85 y 82 m²) y sus precios se separan 44 000 € (238 frente a 194). La vivienda 1, de 75 m², se vendió más cara que la 8, de 80 m². La superficie no lo explica, pero la distancia sí: las viviendas 4 y 1 están a 2.5 y 2 km del centro, y las 13 y 8, a 4.5 y 4 km. La ubicación pesa en el precio, y la regresión simple la ignora.

El segundo problema es más sutil. En estos datos, las viviendas grandes tienden a estar más cerca del centro. Cuando comparamos una vivienda grande con una pequeña, también estamos comparando, en promedio, una céntrica con una periférica. La pendiente de 3.80 mezcla las dos cosas: lo que vale el metro cuadrado **y** parte de lo que vale estar en el centro. Si queremos saber cuánto vale un metro cuadrado más **en la misma ubicación**, necesitamos un modelo que tenga en cuenta las dos variables a la vez. Eso es la regresión lineal múltiple.

## El modelo de regresión lineal múltiple

La idea es la misma que en la regresión simple, con más de un predictor: el valor medio de $Y$ es una combinación lineal de varias variables, y cada observación se separa de ese valor medio por un error aleatorio.

> 📘 **Definición: modelo de regresión lineal múltiple**
>
> El modelo de regresión lineal múltiple con $p$ predictores supone que
> $$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \cdots + \beta_p X_p + \varepsilon,$$
> donde
>
> - $Y$ es la variable respuesta (el precio);
> - $X_1, \ldots, X_p$ son los $p$ **predictores** (superficie, antigüedad, distancia…);
> - $\beta_0$ es el **intercepto**: el valor esperado de $Y$ cuando todos los predictores valen 0;
> - $\beta_j$ es el **coeficiente** del predictor $X_j$: el cambio esperado en $Y$ por cada unidad más de $X_j$, **manteniendo constantes los demás predictores**;
> - $\varepsilon$ es el error aleatorio, con las mismas suposiciones que en la regresión simple: media cero, varianza constante $\sigma^2$, distribución normal e independencia entre observaciones.
>
> Con una muestra se estiman $\hat{\beta}_0, \hat{\beta}_1, \ldots, \hat{\beta}_p$, y la predicción es
> $$\hat{Y} = \hat{\beta}_0 + \hat{\beta}_1 X_1 + \cdots + \hat{\beta}_p X_p.$$

Con un predictor, el modelo es una recta. Con dos, es un plano en el espacio de tres dimensiones (superficie, distancia, precio): cada vivienda es un punto, y el plano pasa entre ellos. Con más predictores ya no se puede dibujar, pero la idea es la misma.

Las suposiciones sobre el error son las mismas de la regresión simple, y se comprueban igual, con los residuos; lo explica la página de [diagnóstico](diagnostico.md). Hay una condición nueva: ningún predictor puede ser una combinación lineal exacta de los demás (por ejemplo, la superficie en m² y la superficie en pies cuadrados a la vez). Si lo fuera, el modelo no podría repartir el efecto entre ellos. Cuando la relación entre predictores es fuerte pero no exacta, el modelo se puede estimar, pero con problemas; es la [multicolinealidad](multicolinealidad.md).

### La forma matricial

Con muchos predictores, escribir las fórmulas variable a variable se vuelve incómodo. Se escriben con matrices. Se colocan los datos en una tabla $\mathbf{X}$ con una fila por observación y una columna por predictor, más una primera columna de unos para el intercepto:

$$\mathbf{y} = \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_n \end{pmatrix}, \qquad \mathbf{X} = \begin{pmatrix} 1 & x_{11} & \cdots & x_{1p} \\ 1 & x_{21} & \cdots & x_{2p} \\ \vdots & \vdots & & \vdots \\ 1 & x_{n1} & \cdots & x_{np} \end{pmatrix}, \qquad \boldsymbol{\beta} = \begin{pmatrix} \beta_0 \\ \beta_1 \\ \vdots \\ \beta_p \end{pmatrix}.$$

Aquí $x_{ij}$ es el valor del predictor $j$ en la observación $i$. Las $n$ ecuaciones del modelo, una por observación, se resumen en una sola: $\mathbf{y} = \mathbf{X}\boldsymbol{\beta} + \boldsymbol{\varepsilon}$. En el caso de las viviendas con superficie y distancia, la fila de la vivienda 1 es $(1,\ 75,\ 2.0)$ y su ecuación dice $211 = \beta_0 + 75\,\beta_1 + 2.0\,\beta_2 + \varepsilon_1$.

## Estimación por mínimos cuadrados

El criterio para elegir los coeficientes es el mismo que en la regresión simple: los que hacen más pequeña la suma de los errores al cuadrado.

> 📘 **Definición: mínimos cuadrados en regresión múltiple**
>
> Los estimadores de mínimos cuadrados son los valores $\hat{\beta}_0, \ldots, \hat{\beta}_p$ que minimizan
> $$\sum_{i=1}^{n} \big(y_i - \beta_0 - \beta_1 x_{i1} - \cdots - \beta_p x_{ip}\big)^2.$$
> En forma matricial, la solución es
> $$\hat{\boldsymbol{\beta}} = (\mathbf{X}^\top \mathbf{X})^{-1} \mathbf{X}^\top \mathbf{y},$$
> donde $\mathbf{X}^\top$ es la traspuesta de $\mathbf{X}$ y $(\cdot)^{-1}$ la inversa de una matriz. Los residuos son $e_i = y_i - \hat{y}_i$, y la varianza del error se estima con
> $$s^2 = \frac{SS_\text{res}}{n - p - 1},$$
> porque se han estimado $p + 1$ parámetros.

No hace falta calcular esta fórmula a mano: lo hace cualquier programa. Lo importante es entender qué hace (minimizar la misma suma de cuadrados que en la regresión simple) y una consecuencia: la fórmula necesita que $\mathbf{X}^\top \mathbf{X}$ tenga inversa, y eso falla justo cuando un predictor es una combinación lineal exacta de otros. Los residuos siguen sumando cero, el $R^2$ se define igual, $R^2 = 1 - SS_\text{res} / SS_\text{tot}$, y $s$ sigue midiendo, en unidades de $Y$, cuánto se equivoca típicamente el modelo. Lo único que cambia son los grados de libertad, $n - p - 1$ en lugar de $n - 2$ (con $p = 1$ vuelve a ser $n - 2$).

> ✏️ **Ejemplo resuelto: precio, superficie y distancia**
>
> Con las 15 viviendas, el modelo con superficie y distancia estimado por mínimos cuadrados es
> $$\widehat{\text{Precio}} = 38.20 + 2.671 \times \text{Superficie} - 12.59 \times \text{Distancia}.$$
>
> - **Ajuste:** $SS_\text{res} = 417.3$ y $SS_\text{tot} = 28\,263$, así que $R^2 = 1 - 417.3 / 28\,263 = 0.985$. Con $n - p - 1 = 15 - 2 - 1 = 12$ grados de libertad, $s = \sqrt{417.3 / 12} = 5.90$ miles de €.
> - **Comparación con la regresión simple:** el $R^2$ sube de 0.885 a 0.985 y el error típico baja de 15.8 a 5.9 miles de €. Añadir la distancia ha dividido entre casi tres el error de tasación.
> - **Viviendas 4 y 13:** el modelo predice $38.20 + 2.671 \cdot 85 - 12.59 \cdot 2.5 = 233.7$ para la 4 y $38.20 + 2.671 \cdot 82 - 12.59 \cdot 4.5 = 200.5$ para la 13. Los precios reales son 238 y 194: la distancia explica la mayor parte de la diferencia que la superficie no explicaba.

## Interpretar los coeficientes: "manteniendo constantes las demás"

En el modelo anterior, el coeficiente de la superficie es 2.671: **dos viviendas a la misma distancia del centro** que se diferencien en un metro cuadrado se diferencian, en promedio, en 2671 € de precio. El coeficiente de la distancia es $-12.59$: **dos viviendas de la misma superficie** que se diferencien en un kilómetro de distancia al centro se diferencian, en promedio, en 12 590 €, a favor de la más céntrica.

Esa coletilla, "manteniendo constantes las demás variables" (en latín, *ceteris paribus*), es la diferencia esencial con la regresión simple. Cada coeficiente mide la relación de su variable con $Y$ **descontando** la parte que se puede atribuir a los otros predictores del modelo. Por eso decimos que el modelo **controla** por esas variables.

### Por qué cambia un coeficiente al añadir variables

En la regresión simple, la pendiente de la superficie era 3.80; en la múltiple, 2.671. No es un error: las dos responden a preguntas distintas. La simple responde a "¿cuánto más caras son, en promedio, las viviendas con un metro cuadrado más?", y la múltiple, a "¿cuánto más caras son, en promedio, las viviendas con un metro cuadrado más **a la misma distancia del centro**?".

La relación exacta entre las dos se ve con una regresión auxiliar de la distancia sobre la superficie, que da una pendiente de $-0.0893$: cada metro cuadrado más se asocia con 0.0893 km menos de distancia al centro. Entonces

$$\underbrace{3.795}_{\text{simple}} = \underbrace{2.671}_{\text{efecto de la superficie}} + \underbrace{(-12.59)}_{\text{efecto de la distancia}} \times \underbrace{(-0.0893)}_{\text{relación distancia–superficie}} = 2.671 + 1.124.$$

La pendiente simple suma el efecto propio de la superficie y un efecto **prestado** de la distancia: las viviendas grandes son, en promedio, más céntricas, y parte de su precio se debe a eso. La regresión múltiple separa las dos partes. A la variable que falta en un modelo y distorsiona los coeficientes de las que sí están se le llama **variable omitida**; es la misma idea que la variable de confusión de la [correlación](../05-regresion-lineal-simple/correlacion.md#correlación-no-implica-causalidad).

En general, el coeficiente de $X_1$ en la regresión simple es igual a su coeficiente en la múltiple **más** el coeficiente de la variable omitida multiplicado por la relación entre las dos. El coeficiente cambia poco si la variable omitida apenas influye en $Y$ o apenas se relaciona con $X_1$, y cambia mucho si las dos cosas son fuertes. Incluso puede **cambiar de signo**: si el efecto prestado es mayor que el propio y va en sentido contrario.

> ✏️ **Ejemplo resuelto: ¿las viviendas antiguas valen más?**
>
> Una regresión simple del precio sobre la antigüedad da una pendiente de $+6.75$: cada año más de antigüedad se asocia con 6750 € **más** de precio. Va contra la intuición: un piso viejo suele necesitar reformas y debería valer menos.
>
> La explicación está en la distancia. En esta ciudad, las viviendas antiguas están en el centro y las nuevas en la periferia: la correlación entre antigüedad y distancia es $-0.989$, casi perfecta. Una regresión de la distancia sobre la antigüedad da una pendiente de $-0.261$ km por año, y con el efecto de la distancia de $-12.59$ por km, el efecto prestado es aproximadamente $(-12.59) \cdot (-0.261) = +3.28$ miles de € por año.
>
> - Con superficie y antigüedad (sin distancia), el coeficiente de la antigüedad es $+3.31$, casi exactamente el efecto prestado: la antigüedad está haciendo de **sustituta** de la distancia.
> - Con las tres variables, el coeficiente de la antigüedad baja a $+2.02$, con un intervalo de confianza al 95 % de $[-2.2,\ 6.2]$, que incluye valores negativos.
>
> La conclusión no es "la antigüedad sube el precio", sino "la antigüedad va unida a estar en el centro, y estar en el centro sube el precio". Si el efecto propio de la antigüedad, a igual distancia, fuera negativo, el coeficiente habría cambiado de signo al controlar por la distancia. Con estos datos no podemos saberlo: antigüedad y distancia se mueven tan juntas que el modelo no consigue separar sus efectos. Ese problema es la [multicolinealidad](multicolinealidad.md).

## Predecir

Predecir con un modelo múltiple es sustituir los valores de todos los predictores. Para una vivienda de 90 m² a 3 km del centro,

$$\widehat{\text{Precio}} = 38.20 + 2.671 \cdot 90 - 12.59 \cdot 3 = 240.8 \text{ miles de €}.$$

Como en la regresión simple, esa cifra es una estimación del precio **medio** de las viviendas con esas características. Los programas dan dos intervalos alrededor de ella. El **intervalo de confianza para la media**, aquí $[237.4,\ 244.2]$, dice dónde está el precio medio de todas las viviendas de 90 m² a 3 km. El **intervalo de predicción**, aquí $[227.5,\ 254.1]$, dice dónde esperamos el precio de **una** vivienda concreta; es más ancho porque suma la incertidumbre sobre la media y la variabilidad de cada vivienda alrededor de ella (el error $\varepsilon$). Para tasar un piso, el que interesa es el intervalo de predicción.

La advertencia sobre la extrapolación también cambia. En la regresión simple bastaba con mirar si $x$ estaba dentro del rango observado. Con varios predictores hay que mirar la **combinación**: una vivienda de 90 m², 5 años y 1.5 km del centro tiene cada valor dentro del rango de los datos, pero en los datos no hay ninguna vivienda nueva en el centro. Predecirla es una extrapolación escondida; la página de [multicolinealidad](multicolinealidad.md) muestra lo que pasa en ese caso.

## Predictores categóricos: variables indicadoras

Las agencias suelen clasificar las viviendas por zonas. Supongamos que esta divide la ciudad en tres: **centro** (hasta 2.2 km), **intermedia** (de 2.5 a 3.8 km) y **periferia** (4 km o más), con cinco viviendas en cada zona. ¿Cómo se mete una variable con tres categorías en una regresión?

Con dos categorías bastaba una variable indicadora (0 o 1). Con tres, se usan **dos** indicadoras: $I_\text{intermedia} = 1$ si la vivienda está en la zona intermedia y 0 si no, e $I_\text{periferia} = 1$ si está en la periferia y 0 si no. Una vivienda del centro tiene las dos a 0. El centro es la **categoría de referencia**, y los coeficientes de las indicadoras miden la diferencia de cada zona con ella.

> 📘 **Definición: variables indicadoras (*dummies*)**
>
> Una variable categórica con $k$ categorías se incluye en la regresión con $k - 1$ **variables indicadoras**, una por categoría salvo una, la **categoría de referencia**. La indicadora de la categoría $c$ vale 1 si la observación es de esa categoría y 0 si no. El coeficiente de cada indicadora es la diferencia esperada en $Y$ entre esa categoría y la de referencia, manteniendo constantes los demás predictores.

¿Por qué $k - 1$ y no $k$? Porque si se incluyen las tres indicadoras, en cada vivienda suman exactamente 1, que es la columna de unos del intercepto: una combinación lineal exacta, y $\mathbf{X}^\top\mathbf{X}$ no tiene inversa. Es la llamada **trampa de las variables indicadoras**. La categoría que se deja fuera no se pierde: es la referencia, y su nivel queda recogido en el intercepto.

> ✏️ **Ejemplo resuelto: precio, superficie y zona**
>
> El modelo con la superficie y la zona, con el centro como referencia, es
> $$\widehat{\text{Precio}} = -13.11 + 2.970 \times \text{Superficie} - 13.80 \times I_\text{intermedia} - 36.86 \times I_\text{periferia}.$$
>
> - **Zona intermedia:** a igual superficie, una vivienda de la zona intermedia vale, en promedio, 13 800 € menos que una del centro.
> - **Periferia:** a igual superficie, vale 36 860 € menos que una del centro. La diferencia entre periferia e intermedia, que no aparece directamente, es $-36.86 - (-13.80) = -23.06$.
> - **Predicción** para 90 m²: en el centro, $-13.11 + 2.970 \cdot 90 = 254.2$; en la zona intermedia, $254.2 - 13.8 = 240.4$; en la periferia, $254.2 - 36.9 = 217.3$ miles de €.
>
> Compara con las medias por zona sin tener en cuenta la superficie: 273.2 en el centro, 239.2 en la zona intermedia y 190.6 en la periferia, es decir, la periferia está 82.6 mil € por debajo del centro. En el modelo, la diferencia es de 36.9 mil €. El resto se debe a que las viviendas del centro son más grandes (96.4 m² de media, frente a 81.0 en la periferia). Otra vez, controlar por una variable cambia la lectura.

Si se elige otra categoría de referencia, cambian los coeficientes, pero no las predicciones: el modelo es el mismo, contado desde otro punto de partida. Conviene elegir como referencia la categoría con la que tenga sentido comparar (la más habitual, o la situación de partida).

### Interacciones: cuando el efecto depende de otra variable

El modelo anterior supone que un metro cuadrado vale lo mismo en todas las zonas: las tres zonas tienen la misma pendiente y solo cambia el nivel. Pero podría ser que en el centro el metro cuadrado se pagara más caro. Para permitirlo se añade una **interacción**, el producto de las dos variables. Con una indicadora $I_\text{centro}$ (1 si la vivienda está en el centro):

$$Y = \beta_0 + \beta_1 \,\text{Superficie} + \beta_2 \, I_\text{centro} + \beta_3 \, (\text{Superficie} \times I_\text{centro}) + \varepsilon.$$

Fuera del centro, $I_\text{centro} = 0$ y la pendiente de la superficie es $\beta_1$. En el centro, la pendiente es $\beta_1 + \beta_3$. El coeficiente de la interacción, $\beta_3$, es la **diferencia de pendientes**. Con las 15 viviendas sale $\hat{\beta}_1 = 3.63$ y $\hat{\beta}_3 = -0.50$: en el centro, cada metro cuadrado se asociaría con 3.13 mil €, y fuera, con 3.63. Pero el p-valor de la interacción es 0.52: con cinco viviendas en el centro, los datos no dan ninguna evidencia de que las pendientes sean distintas, y nos quedamos con el modelo sin interacción. Las interacciones son útiles cuando hay motivos para pensar que el efecto de una variable depende de otra y datos suficientes para estimarlo.

## Leer el summary de statsmodels

En la práctica, el modelo se estima con un programa y se lee su resumen. Este es el `summary()` de statsmodels para el modelo con las tres variables numéricas, `precio ~ superficie + antiguedad + distancia` (versión abreviada, que se obtiene con `summary(slim=True)`):

```text
                            OLS Regression Results
==============================================================================
Dep. Variable:                 precio   R-squared:                       0.987
Model:                            OLS   Adj. R-squared:                  0.983
No. Observations:                  15   F-statistic:                     270.3
Covariance Type:            nonrobust   Prob (F-statistic):           1.39e-10
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
Intercept    -16.3259     54.995     -0.297      0.772    -137.368     104.717
superficie     2.6692      0.188     14.228      0.000       2.256       3.082
antiguedad     2.0208      1.897      1.065      0.310      -2.155       6.197
distancia     -5.0115      7.248     -0.691      0.504     -20.965      10.942
==============================================================================

Notes:
[2] The condition number is large, 3.33e+03. This might indicate that there are
strong multicollinearity or other numerical problems.
```

Se lee por partes.

- **Cabecera izquierda.** La variable respuesta (`precio`), el método (mínimos cuadrados ordinarios, OLS) y el número de observaciones (15).
- **`R-squared`.** El $R^2$ de siempre: el modelo explica el 98.7 % de la variabilidad del precio.
- **`Adj. R-squared`.** El $R^2$ ajustado, que penaliza el número de predictores. El $R^2$ nunca baja al añadir una variable, aunque no sirva para nada; el ajustado sí puede bajar. Se usa para comparar modelos con distinto número de variables, y se explica en [selección de variables](seleccion.md).
- **`F-statistic` y `Prob (F-statistic)`.** El contraste global del modelo: $H_0$ es que **todos** los coeficientes salvo el intercepto son cero ($\beta_1 = \beta_2 = \beta_3 = 0$), es decir, que ninguna variable sirve para predecir el precio. Con $F = 270.3$ y un p-valor de $1.4 \times 10^{-10}$, se rechaza con claridad.
- **Tabla de coeficientes.** Una fila por parámetro. `coef` es la estimación $\hat{\beta}_j$; `std err`, su error estándar; `t`, el cociente $\hat{\beta}_j / SE(\hat{\beta}_j)$; `P>|t|`, el p-valor del contraste $H_0: \beta_j = 0$ (que esa variable no aporta nada **una vez que están las demás**), con una $t$ de $n - p - 1 = 11$ grados de libertad; y las dos últimas columnas, el intervalo de confianza al 95 %, $\hat{\beta}_j \pm t_{11,\,0.975} \cdot SE(\hat{\beta}_j)$, con $t_{11,\,0.975} = 2.201$. Por ejemplo, para la superficie, $2.669 \pm 2.201 \cdot 0.188 = [2.256,\ 3.082]$.
- **Notas.** La nota [2] avisa de que el número de condición es grande, un posible síntoma de multicolinealidad.

La lectura de la tabla tiene una sorpresa. La superficie es claramente significativa ($t = 14.2$), pero la antigüedad y la distancia no lo son (p-valores de 0.31 y 0.50), aunque, por separado, las dos tienen una relación fortísima con el precio y el contraste F dice que el modelo en conjunto es muy significativo. Y el coeficiente de la distancia ha pasado de $-12.59$, con un error estándar de 1.40, en el modelo sin antigüedad, a $-5.01$, con un error estándar de 7.25. No es que la distancia haya dejado de importar: es que el modelo no sabe si atribuir el efecto a la distancia o a la antigüedad, que van casi juntas. La [página siguiente](multicolinealidad.md) explica qué está pasando y qué hacer.

> ⚠️ **Errores frecuentes**
>
> - **Olvidar el "manteniendo constantes las demás".** El coeficiente de la superficie en el modelo múltiple (2.67) no es el mismo que en el simple (3.80), y no se interpreta igual.
> - **Comparar coeficientes por su tamaño.** El coeficiente de la distancia ($-12.6$) es mayor en valor absoluto que el de la superficie (2.7), pero uno es por kilómetro y el otro por metro cuadrado. Las magnitudes solo se comparan en las mismas unidades (por ejemplo, estandarizando las variables).
> - **Leer un coeficiente como causal.** El modelo controla por las variables incluidas, no por las que faltan. La antigüedad tiene coeficiente positivo porque va unida a la ubicación, no porque envejecer suba el precio.
> - **Concluir que una variable no influye porque su p-valor es alto** en un modelo con predictores muy relacionados entre sí: puede ser multicolinealidad.
> - **Meter las $k$ indicadoras de una variable con $k$ categorías.** Se incluyen $k - 1$; la que falta es la referencia.
> - **Codificar una variable categórica con números (1, 2, 3) y tratarla como cantidad.** Impone que el salto entre categorías sea siempre el mismo; con indicadoras, cada categoría tiene su propio coeficiente.

## En Python

El modelo con statsmodels, la fórmula matricial a mano y las predicciones con sus intervalos:

```python
import numpy as np
import pandas as pd
import statsmodels.formula.api as smf

viviendas = pd.DataFrame({
    "superficie": [75, 90, 100, 85, 95, 70, 110, 80, 88, 92, 78, 105, 82, 96, 89],   # m²
    "antiguedad": [19, 17, 22, 17, 8, 5, 22, 12, 15, 19, 5, 21, 11, 15, 12],          # años
    "distancia": [2.0, 3.0, 1.5, 2.5, 5.0, 6.0, 1.0, 4.0, 3.5, 2.2, 5.5, 1.8, 4.5, 3.2, 3.8],  # km
    "precio": [211, 250, 293, 238, 229, 157, 320, 197, 234, 247, 176, 295, 194, 251, 223],    # miles €
})

# Regresión simple y múltiple
simple = smf.ols("precio ~ superficie", data=viviendas).fit()
multiple = smf.ols("precio ~ superficie + distancia", data=viviendas).fit()
print("Simple:  ", simple.params.round(3).to_dict(), "R² =", round(simple.rsquared, 3))
```

```text
Simple:   {'Intercept': -103.385, 'superficie': 3.795} R² = 0.885
```

```python
print("Múltiple:", multiple.params.round(3).to_dict(), "R² =", round(multiple.rsquared, 3))
```

```text
Múltiple: {'Intercept': 38.199, 'superficie': 2.671, 'distancia': -12.588} R² = 0.985
```

```python
print("Error típico s:", round(np.sqrt(multiple.scale), 2))                  # 5.9
```

```text
Error típico s: 5.9
```

```python
# La fórmula matricial: (X'X)^(-1) X'y
X = np.column_stack([np.ones(len(viviendas)), viviendas["superficie"], viviendas["distancia"]])
y = viviendas["precio"].to_numpy()
print("A mano:  ", np.linalg.solve(X.T @ X, X.T @ y).round(3))
```

```text
A mano:   [ 38.199   2.671 -12.588]
```

```python
# Por qué cambia el coeficiente de la superficie: 3.795 = 2.671 + (-12.59)(-0.0893)
auxiliar = smf.ols("distancia ~ superficie", data=viviendas).fit().params["superficie"]
print("Descomposición:", round(multiple.params["superficie"] + multiple.params["distancia"] * auxiliar, 3))
```

```text
Descomposición: 3.795
```

```python
# La antigüedad, sola y acompañada
for formula in ["precio ~ antiguedad", "precio ~ superficie + antiguedad",
                "precio ~ superficie + antiguedad + distancia"]:
    m = smf.ols(formula, data=viviendas).fit()
    print(f"{formula:45s} coef. antigüedad = {m.params['antiguedad']:.2f}")
```

```text
precio ~ antiguedad                           coef. antigüedad = 6.75
precio ~ superficie + antiguedad              coef. antigüedad = 3.31
precio ~ superficie + antiguedad + distancia  coef. antigüedad = 2.02
```

```python
# Predicción para 90 m² a 3 km, con sus dos intervalos
nueva = pd.DataFrame({"superficie": [90], "distancia": [3.0]})
pred = multiple.get_prediction(nueva).summary_frame(alpha=0.05)
print(pred[["mean", "mean_ci_lower", "mean_ci_upper", "obs_ci_lower", "obs_ci_upper"]].round(1))
```

```text
    mean  mean_ci_lower  mean_ci_upper  obs_ci_lower  obs_ci_upper
0  240.8          237.4          244.2         227.5         254.1
```

El resumen completo del modelo con las tres variables. Sin `slim=True`, statsmodels añade la fecha, el AIC y el BIC y unos contrastes sobre los residuos:

```python
completo = smf.ols("precio ~ superficie + antiguedad + distancia", data=viviendas).fit()
print(completo.summary(slim=True))
```

```text
                            OLS Regression Results                            
==============================================================================
Dep. Variable:                 precio   R-squared:                       0.987
Model:                            OLS   Adj. R-squared:                  0.983
No. Observations:                  15   F-statistic:                     270.3
Covariance Type:            nonrobust   Prob (F-statistic):           1.39e-10
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
Intercept    -16.3259     54.995     -0.297      0.772    -137.368     104.717
superficie     2.6692      0.188     14.228      0.000       2.256       3.082
antiguedad     2.0208      1.897      1.065      0.310      -2.155       6.197
distancia     -5.0115      7.248     -0.691      0.504     -20.965      10.942
==============================================================================

Notes:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
[2] The condition number is large, 3.33e+03. This might indicate that there are
strong multicollinearity or other numerical problems.
```

Variables categóricas e interacciones. En una fórmula, `C(zona)` crea las indicadoras (la referencia es la primera categoría por orden alfabético; con `C(zona, Treatment("periferia"))` se elige otra) y `superficie * centro` incluye las dos variables y su producto:

```python
viviendas["zona"] = ["centro", "intermedia", "centro", "intermedia", "periferia",
                     "periferia", "centro", "periferia", "intermedia", "centro",
                     "periferia", "centro", "periferia", "intermedia", "intermedia"]
print(viviendas.groupby("zona")[["precio", "superficie"]].mean().round(1))
```

```text
            precio  superficie
zona                          
centro       273.2        96.4
intermedia   239.2        89.6
periferia    190.6        81.0
```

```python
con_zona = smf.ols("precio ~ superficie + C(zona)", data=viviendas).fit()
print(con_zona.params.round(2))
```

```text
Intercept               -13.11
C(zona)[T.intermedia]   -13.80
C(zona)[T.periferia]    -36.86
superficie                2.97
dtype: float64
```

```python
por_zona = pd.DataFrame({"superficie": [90, 90, 90], "zona": ["centro", "intermedia", "periferia"]})
print("Predicciones para 90 m²:", con_zona.predict(por_zona).round(1).tolist())
```

```text
Predicciones para 90 m²: [254.2, 240.4, 217.3]
```

```python
# Interacción: ¿vale distinto el metro cuadrado en el centro?
viviendas["centro"] = (viviendas["zona"] == "centro").astype(int)
interaccion = smf.ols("precio ~ superficie * centro", data=viviendas).fit()
print(pd.DataFrame({"coef": interaccion.params, "p-valor": interaccion.pvalues}).round(3))
```

```text
                     coef  p-valor
Intercept         -94.414    0.076
superficie          3.626    0.000
centro             66.319    0.351
superficie:centro  -0.501    0.518
```

> ✅ **Lo esencial**
>
> - El modelo de regresión lineal múltiple es $Y = \beta_0 + \beta_1 X_1 + \cdots + \beta_p X_p + \varepsilon$, con las mismas suposiciones sobre el error que el simple. Se estima por mínimos cuadrados, $\hat{\boldsymbol{\beta}} = (\mathbf{X}^\top \mathbf{X})^{-1} \mathbf{X}^\top \mathbf{y}$, y $s^2 = SS_\text{res} / (n - p - 1)$.
> - Cada coeficiente $\beta_j$ es el cambio medio de $Y$ por unidad de $X_j$ **manteniendo constantes los demás predictores**. Por eso puede ser muy distinto de la pendiente de la regresión simple, e incluso cambiar de signo, cuando $X_j$ está relacionado con otras variables que influyen en $Y$.
> - En las viviendas, el metro cuadrado pasa de 3.80 mil € (simple) a 2.67 mil € a igual distancia; la antigüedad tiene coeficiente positivo porque hace de sustituta de la ubicación.
> - Una variable categórica con $k$ categorías entra con $k - 1$ indicadoras; cada coeficiente es la diferencia con la categoría de referencia. Una interacción permite que la pendiente de una variable dependa de otra.
> - En el `summary()`, el contraste F dice si el modelo sirve en conjunto, y cada `P>|t|` dice si una variable aporta algo **además** de las demás.

## Ejercicios

**1.** Una cadena de tiendas estima $\widehat{\text{Ventas}} = 120 + 2.5 \times \text{Publicidad} - 8 \times \text{Precio}$, con las ventas y la publicidad en miles de € y el precio en €. Interpreta los dos coeficientes y predice las ventas de una tienda con 20 mil € de publicidad y un precio de 10 €.

<details>
<summary>✅ <b>Solución</b></summary>

- **Publicidad:** con el mismo precio, cada mil € más de publicidad se asocian, en promedio, con 2.5 mil € más de ventas.
- **Precio:** con la misma publicidad, cada euro más de precio se asocia, en promedio, con 8 mil € menos de ventas.
- **Predicción:** $120 + 2.5 \cdot 20 - 8 \cdot 10 = 120 + 50 - 80 = 90$ mil €.

El intercepto (120) sería la venta con publicidad 0 y precio 0, una situación sin sentido práctico.

</details>

**2.** En las viviendas, la regresión simple del precio sobre la distancia da una pendiente de $-25.68$, y la múltiple con superficie y distancia, $-12.59$ para la distancia y $2.671$ para la superficie. La regresión auxiliar de la superficie sobre la distancia tiene pendiente $-4.902$. Comprueba la descomposición del coeficiente simple y explica por qué la pendiente simple es el doble de la múltiple.

<details>
<summary>✅ <b>Solución</b></summary>

$$-12.59 + 2.671 \cdot (-4.902) = -12.59 - 13.09 = -25.68.$$
La pendiente simple suma el efecto propio de la distancia ($-12.59$ mil € por km a igual superficie) y un efecto prestado de la superficie: cada kilómetro más de distancia va con 4.9 m² menos de vivienda, y cada metro cuadrado vale 2.671 mil €, así que $-13.09$ mil € más. En la regresión simple, alejarse del centro "cuesta" el doble porque las viviendas lejanas, además de estar lejos, son más pequeñas.

</details>

**3.** Con el modelo de la zona, $\widehat{\text{Precio}} = -13.11 + 2.970 \times \text{Superficie} - 13.80 \times I_\text{intermedia} - 36.86 \times I_\text{periferia}$, ¿qué precio predice para una vivienda de 80 m² en la periferia? Si se cambia la referencia a la periferia, ¿cuáles serán los coeficientes de las indicadoras del centro y de la zona intermedia?

<details>
<summary>✅ <b>Solución</b></summary>

Predicción: $-13.11 + 2.970 \cdot 80 - 36.86 = -13.11 + 237.60 - 36.86 = 187.6$ mil €.

Con la periferia como referencia, cada coeficiente es la diferencia con la periferia: el centro está $0 - (-36.86) = +36.86$ por encima, y la zona intermedia, $-13.80 - (-36.86) = +23.06$. El coeficiente de la superficie (2.970) no cambia, y el intercepto pasa a ser $-13.11 - 36.86 = -49.97$. Las predicciones son exactamente las mismas.

</details>

**4.** Un modelo de salarios (miles de €) es $\widehat{\text{Salario}} = 22 + 1.2 \times \text{Experiencia} + 3 \times \text{Máster} + 0.8 \times (\text{Experiencia} \times \text{Máster})$, con la experiencia en años y $\text{Máster} = 1$ si la persona tiene un máster. ¿Cuánto aumenta el salario por cada año de experiencia para quien no tiene máster? ¿Y para quien lo tiene? ¿Qué diferencia salarial predice el modelo entre dos personas con 10 años de experiencia, una con máster y otra sin él?

<details>
<summary>✅ <b>Solución</b></summary>

Sin máster ($\text{Máster} = 0$), la pendiente de la experiencia es 1.2 mil € por año. Con máster, $1.2 + 0.8 = 2$ mil € por año: la interacción dice que la experiencia se paga más con máster.

Con 10 años: sin máster, $22 + 12 = 34$; con máster, $22 + 12 + 3 + 8 = 45$. La diferencia es 11 mil €, que es $3 + 0.8 \cdot 10$: con interacción, la diferencia entre los grupos depende de la experiencia. Con 0 años sería solo 3 mil €.

</details>

**5.** En el `summary()` del modelo con las tres variables, el coeficiente de la antigüedad es 2.0208 con un error estándar de 1.897. Calcula el estadístico $t$, di cuántos grados de libertad tiene y calcula el intervalo de confianza al 95 % ($t_{11,\,0.975} = 2.201$). ¿Qué concluyes?

<details>
<summary>✅ <b>Solución</b></summary>

$t = 2.0208 / 1.897 = 1.065$, con $n - p - 1 = 15 - 3 - 1 = 11$ grados de libertad. El intervalo es $2.021 \pm 2.201 \cdot 1.897 = 2.021 \pm 4.175 = [-2.15,\ 6.20]$, como en el `summary()`.

El intervalo incluye el cero, y el p-valor (0.31) es mayor que 0.05: una vez que están la superficie y la distancia, los datos no permiten afirmar que la antigüedad aporte algo. El intervalo es además muy ancho, con valores negativos y positivos: no sabemos ni el signo del efecto. Eso es un síntoma de multicolinealidad, no una prueba de que la antigüedad no influya.

</details>

**6.** Un compañero quiere incluir la zona con tres indicadoras (centro, intermedia y periferia) además del intercepto. ¿Qué pasará? ¿Y si además incluye la distancia en km?

<details>
<summary>✅ <b>Solución</b></summary>

Con las tres indicadoras, en cada vivienda suman 1, que es exactamente la columna del intercepto: $\mathbf{X}^\top\mathbf{X}$ no tiene inversa y no hay una solución única (la trampa de las variables indicadoras). Algunos programas dan error y otros eliminan una columna o dan coeficientes sin sentido. Hay que quitar una indicadora (o el intercepto).

Incluir además la distancia es posible, porque la zona no es una combinación lineal **exacta** de la distancia. Pero la zona se ha construido a partir de la distancia, así que las dos variables cuentan casi lo mismo: estarán muy relacionadas y sus coeficientes serán inestables. Es un caso de multicolinealidad, y lo razonable es usar una de las dos.

</details>

[← Volver al tema](index.md) [Siguiente: Multicolinealidad →](multicolinealidad.md)
