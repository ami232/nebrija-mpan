# 6.2 Multicolinealidad

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/06-regresion-lineal-multiple/multicolinealidad/>

> ℹ️ **Lo que necesitas antes**
>
> El modelo de regresión múltiple, la interpretación de sus coeficientes y la lectura del `summary()` de la [página anterior](modelo.md). La [correlación](../05-regresion-lineal-simple/correlacion.md) entre dos variables y el [$R^2$](../05-regresion-lineal-simple/ajuste.md). Seguimos con las 15 viviendas: superficie (m²), antigüedad (años), distancia al centro (km) y precio (miles de €).

## Un modelo que explica mucho y no sabe a quién atribuirlo

Al final de la página anterior, el modelo del precio con la superficie, la antigüedad y la distancia daba un resultado desconcertante. En conjunto, el modelo es excelente: $R^2 = 0.987$ y un contraste F con p-valor $1.4 \times 10^{-10}$. Pero, de las tres variables, solo la superficie es significativa. La antigüedad (p-valor 0.31) y la distancia (p-valor 0.50) parecen no aportar nada, aunque cada una por separado tiene una correlación con el precio de 0.86 en valor absoluto. Y el coeficiente de la distancia, que en el modelo sin antigüedad era $-12.59$ con un error estándar de 1.40, ahora es $-5.01$ con un error estándar de 7.25.

La explicación está en la relación entre las dos variables: en esta ciudad, las viviendas antiguas están en el centro y las nuevas en la periferia, y la correlación entre antigüedad y distancia es $-0.989$. Piensa en dos comerciales que siempre visitan juntos a los clientes. Si las ventas suben cuando van, sabemos que el equipo funciona, pero no podemos saber cuál de los dos es el bueno: para separarlos necesitaríamos visitas en las que fuera uno solo. Con la antigüedad y la distancia pasa lo mismo. El modelo sabe que "antiguo y céntrico" sube el precio, y lo predice bien, pero apenas hay viviendas antiguas lejos del centro, o nuevas en el centro, que le permitan repartir el efecto entre las dos. Cualquier reparto parecido explica los datos casi igual de bien, y por eso los coeficientes individuales son tan imprecisos.

## Qué es la multicolinealidad

> 📘 **Definición: multicolinealidad**
>
> Hay **multicolinealidad** cuando uno de los predictores de un modelo de regresión se puede predecir con mucha precisión a partir de los demás, es decir, cuando existe una relación lineal fuerte entre los predictores.
>
> - **Colinealidad perfecta:** un predictor es una combinación lineal **exacta** de otros. La matriz $\mathbf{X}^\top\mathbf{X}$ no tiene inversa y los coeficientes no se pueden estimar.
> - **Multicolinealidad alta (o imperfecta):** la relación es fuerte pero no exacta. Los coeficientes se pueden estimar, pero con errores estándar muy grandes.

La colinealidad perfecta aparece casi siempre por un descuido: incluir la superficie en m² y en pies cuadrados (que es la misma variable multiplicada por 10.764), incluir los gastos por categoría y también el gasto total, o caer en la trampa de las variables indicadoras de la [página anterior](modelo.md#predictores-categóricos-variables-indicadoras). Es fácil de detectar, porque el programa da un error o descarta una variable, y se arregla quitando la variable redundante.

La multicolinealidad alta es el caso interesante, porque el programa no avisa (o avisa de forma vaga) y los resultados parecen normales. Ocurre con naturalidad en datos de negocio: la renta y el nivel de estudios, el tamaño de una tienda y su número de empleados, el gasto en publicidad en televisión y en redes cuando se deciden juntos.

Fíjate en que la multicolinealidad es una relación **entre los predictores**, no entre un predictor y la respuesta. Que la distancia esté muy relacionada con el precio es bueno; que esté muy relacionada con la antigüedad es lo que causa el problema.

## Consecuencias

### Errores estándar grandes

Es la consecuencia principal, y de ella salen las demás. Cuando un predictor está muy relacionado con los otros, apenas tiene variación **propia**: casi toda su variación se puede predecir con los demás. El modelo estima su coeficiente con esa variación propia, que es poca, y la estimación es imprecisa. Es la misma idea que en la regresión simple, donde la pendiente se estimaba peor cuando los valores de $x$ estaban [poco repartidos](../05-regresion-lineal-simple/ajuste.md#es-real-la-relación-inferencia-sobre-la-pendiente).

En las viviendas, el error estándar del coeficiente de la distancia se multiplica por más de cinco al añadir la antigüedad: pasa de 1.40 a 7.25. El de la superficie, que no está muy relacionada con las otras dos, casi no cambia (0.189 frente a 0.188).

### Coeficientes inestables

Con errores estándar grandes, los coeficientes cambian mucho con pequeños cambios en los datos. Una forma de verlo es repetir el ajuste quitando cada vez una vivienda (quince ajustes con catorce viviendas). En el modelo con las tres variables, el coeficiente de la antigüedad va de 0.92 a 2.93 y el de la distancia, de $-9.04$ a $-1.36$: según qué vivienda falte, el efecto de alejarse un kilómetro del centro es casi siete veces mayor o menor. En el modelo sin antigüedad, el coeficiente de la distancia solo se mueve entre $-13.32$ y $-12.22$.

### Signos que contradicen la intuición

Si un coeficiente es muy impreciso, puede salir con cualquier signo. En el modelo completo, el intervalo de confianza de la antigüedad va de $-2.2$ a $6.2$, y el de la distancia, de $-21.0$ a $10.9$: los datos son compatibles con que las viviendas más alejadas del centro valgan **más**, a igual antigüedad. El coeficiente positivo de la antigüedad (2.02 mil € por año) es de este tipo: no dice que los pisos viejos valgan más, sino que el modelo no consigue separar la antigüedad de la ubicación.

### Variables no significativas que, juntas, lo son

Cada p-valor de la tabla contrasta si una variable aporta algo **además de las demás**. Si la antigüedad y la distancia cuentan casi lo mismo, cada una aporta poco una vez que está la otra, y las dos salen no significativas. Pero si se contrasta si **las dos a la vez** son cero ($H_0: \beta_\text{antigüedad} = \beta_\text{distancia} = 0$), el resultado es $F = 41.6$ con un p-valor de $7 \times 10^{-6}$: la ubicación, medida con una variable u otra, importa muchísimo. Quitar las dos variables porque ninguna es significativa por separado sería un error grave.

### Lo que no se estropea: el ajuste y las predicciones habituales

La multicolinealidad no afecta a la capacidad del modelo para predecir viviendas **parecidas a las de los datos**. El $R^2$ del modelo completo (0.987) es prácticamente el del modelo sin antigüedad (0.985). Para una vivienda típica de 90 m², 17 años y 3 km, los tres modelos dan predicciones parecidas:

| Modelo | 90 m², 17 años, 3 km | 90 m², 5 años, 1.5 km |
|---|---|---|
| Superficie + antigüedad + distancia | 243.2 | 226.5 |
| Superficie + distancia | 240.8 | 259.7 |
| Superficie + antigüedad | 244.7 | 205.0 |

La segunda columna es otra historia. Una vivienda nueva en el centro rompe la pauta de los datos, en los que las viviendas céntricas son antiguas. Cada modelo reparte de forma distinta el efecto de la ubicación entre antigüedad y distancia, y como esa vivienda separa las dos variables, las predicciones se separan: de 205 a 260 mil €. El modelo completo da 226.5, pero con un error estándar de 31.3 para la media, frente a 2.8 en la vivienda típica. Es la **extrapolación escondida** de la que avisaba la página anterior: cada valor está dentro del rango de los datos, pero la combinación no.

Lo esencial es que la multicolinealidad **no introduce sesgo**: si el modelo es correcto, los coeficientes siguen acertando en promedio. Lo que hace es aumentar su varianza. Por eso es un problema serio si queremos **interpretar** coeficientes concretos y un problema menor si solo queremos **predecir** casos parecidos a los observados.

## Cómo detectarla

### La matriz de correlaciones

El primer paso es mirar las correlaciones entre los predictores. En las viviendas:

| | Superficie | Antigüedad | Distancia |
|---|---|---|---|
| **Superficie** | 1 | 0.655 | $-0.662$ |
| **Antigüedad** | 0.655 | 1 | $-0.989$ |
| **Distancia** | $-0.662$ | $-0.989$ | 1 |

La correlación de $-0.989$ entre antigüedad y distancia salta a la vista. Una regla práctica es sospechar a partir de $|r| > 0.8$. La matriz tiene una limitación: solo mira las variables de dos en dos. Puede haber una variable que sea casi una combinación de **otras dos o más**, sin que ninguna correlación por parejas sea alarmante (el ejercicio 4 muestra un caso). Para eso está el VIF.

### El factor de inflación de la varianza (VIF)

La idea es medir directamente lo que causa el problema: hasta qué punto cada predictor se puede predecir con los demás. Para ello se hace una regresión auxiliar de cada predictor sobre todos los otros, y se mira su $R^2$.

> 📘 **Definición: factor de inflación de la varianza (VIF)**
>
> Sea $R^2_j$ el coeficiente de determinación de la regresión del predictor $X_j$ sobre los demás predictores del modelo. El **factor de inflación de la varianza** de $X_j$ es
> $$\text{VIF}_j = \frac{1}{1 - R^2_j}.$$
> La varianza de $\hat{\beta}_j$ es $\text{VIF}_j$ veces la que tendría si $X_j$ no estuviera relacionado con los demás predictores, y su error estándar, $\sqrt{\text{VIF}_j}$ veces.
>
> - $\text{VIF}_j = 1$: $X_j$ no está relacionado linealmente con los demás ($R^2_j = 0$).
> - $\text{VIF}_j > 5$: multicolinealidad a vigilar ($R^2_j > 0.8$).
> - $\text{VIF}_j > 10$: multicolinealidad alta ($R^2_j > 0.9$); los coeficientes afectados no son fiables.

La fórmula tiene una lectura directa. $1 - R^2_j$ es la proporción de la variación de $X_j$ que es **propia**, que los demás predictores no explican. Si es pequeña, el VIF es grande. Con un $R^2_j$ de 0.9, solo el 10 % de la variación es propia, la varianza del coeficiente se multiplica por 10 y su error estándar por $\sqrt{10} \approx 3.2$. Los umbrales de 5 y 10 son convenciones, no leyes: con pocos datos, incluso un VIF de 3 o 4 puede hacer que un coeficiente sea muy impreciso.

Con solo dos predictores, el $R^2$ de la regresión de uno sobre el otro es el cuadrado de su correlación, y el VIF de los dos es $1 / (1 - r^2)$.

> ✏️ **Ejemplo resuelto: VIF de las viviendas**
>
> Para cada predictor del modelo completo se hace la regresión auxiliar sobre los otros dos:
>
> - **Antigüedad** sobre superficie y distancia: $R^2_j = 0.979$, así que $\text{VIF} = 1 / (1 - 0.979) = 47.7$. El 97.9 % de la variación de la antigüedad se explica con las otras dos variables; solo el 2.1 % es propia.
> - **Distancia** sobre superficie y antigüedad: $R^2_j = 0.979$ y $\text{VIF} = 48.4$.
> - **Superficie** sobre antigüedad y distancia: $R^2_j = 0.438$ y $\text{VIF} = 1.78$.
>
> La antigüedad y la distancia tienen un VIF muy por encima de 10. Su error estándar es unas $\sqrt{48} \approx 7$ veces el que tendría sin multicolinealidad. La superficie está bien.
>
> En el modelo sin antigüedad, el VIF de la distancia es $1 / (1 - 0.662^2) = 1.78$. El cociente de las raíces, $\sqrt{48.4 / 1.78} = 5.2$, es el factor por el que se multiplicó el error estándar de la distancia al añadir la antigüedad: $7.25 / 1.40 = 5.2$.

El `summary()` de statsmodels avisa a veces de un **número de condición** grande (en el modelo completo, $3.33 \times 10^{3}$). Es otro indicador de multicolinealidad, pero depende de las unidades de las variables: el modelo con superficie y distancia, que no tiene ningún problema (VIF de 1.78), da también un aviso, con $1.19 \times 10^{3}$, solo porque las variables tienen escalas muy distintas (la superficie ronda los 90 m² y la distancia, los 3 km). Usa el VIF para decidir.

## Qué hacer

No hay una solución única. Depende de para qué sirve el modelo y de qué variables están implicadas.

1. **No hacer nada, si el objetivo es predecir casos parecidos a los de los datos.** El ajuste y esas predicciones no se estropean. Si las variables implicadas son solo de control y los coeficientes que importan tienen VIF bajos, tampoco hay que hacer nada: en las viviendas, el coeficiente de la superficie (2.67, VIF 1.78) es fiable en cualquiera de los modelos.
2. **Quitar una de las variables redundantes**, con un criterio de negocio y no solo estadístico. Entre antigüedad y distancia, lo razonable es quedarse con la distancia: es lo que esperamos que influya de verdad en el precio (la ubicación), y la antigüedad solo la acompaña. El modelo con superficie y distancia tiene $R^2 = 0.985$, un coeficiente de la distancia de $-12.59$ con error estándar 1.40 y VIF de 1.78. A cambio, el coeficiente de la distancia recoge ahora también lo que tenga que ver con la antigüedad: hay que interpretarlo como "ubicación, y lo que va con ella".
3. **Combinar las variables en un índice.** Si las dos miden en el fondo lo mismo, se pueden resumir en una: por ejemplo, un índice de "centralidad" que promedie la antigüedad y la distancia (con el signo cambiado) una vez estandarizadas. En las viviendas, el modelo con superficie y ese índice tiene el mismo $R^2 = 0.987$ que el completo, con un coeficiente del índice estimado con precisión. La contrapartida es que el índice es más difícil de explicar que una variable original.
4. **Conseguir más datos que rompan la pauta.** Viviendas antiguas en la periferia y nuevas en el centro son justo lo que el modelo necesita para separar los dos efectos. Es la solución de fondo, pero no siempre es posible: si en la ciudad no existen esas viviendas, el efecto propio de la antigüedad no se puede estimar con estos datos, y hay que decirlo.

Lo que **no** se debe hacer es quitar automáticamente todas las variables no significativas de un modelo con multicolinealidad: en las viviendas, eso eliminaría la antigüedad y la distancia, las dos a la vez, y perderíamos la ubicación por completo. Cómo elegir las variables de un modelo con criterio se trata en [selección de variables](seleccion.md).

> ⚠️ **Errores frecuentes**
>
> - **Interpretar el signo de un coeficiente con VIF alto.** El coeficiente positivo de la antigüedad no dice que los pisos viejos valgan más; dice que el modelo no la separa de la ubicación.
> - **Concluir que una variable no influye porque no es significativa** en un modelo con multicolinealidad. Contrasta conjuntamente las variables relacionadas o quita una y vuelve a mirar.
> - **Creer que la multicolinealidad sesga los coeficientes o empeora el $R^2$.** No los sesga ni empeora el ajuste: los hace imprecisos.
> - **Mirar solo la matriz de correlaciones.** Una variable puede ser combinación de varias sin ninguna correlación alta por parejas. Calcula el VIF.
> - **Confundir multicolinealidad con relación entre predictor y respuesta.** Que un predictor esté muy correlacionado con $Y$ es bueno; el problema es la relación entre predictores.
> - **Predecir combinaciones que no aparecen en los datos** sin avisar, como una vivienda nueva en el centro. Con multicolinealidad, esas predicciones son muy inciertas y dependen del modelo elegido.

## En Python

La matriz de correlaciones, el VIF (con statsmodels y a mano), la inestabilidad de los coeficientes y el contraste conjunto:

```python
import numpy as np
import pandas as pd
import statsmodels.api as sm
import statsmodels.formula.api as smf
from statsmodels.stats.outliers_influence import variance_inflation_factor

viviendas = pd.DataFrame({
    "superficie": [75, 90, 100, 85, 95, 70, 110, 80, 88, 92, 78, 105, 82, 96, 89],   # m²
    "antiguedad": [19, 17, 22, 17, 8, 5, 22, 12, 15, 19, 5, 21, 11, 15, 12],          # años
    "distancia": [2.0, 3.0, 1.5, 2.5, 5.0, 6.0, 1.0, 4.0, 3.5, 2.2, 5.5, 1.8, 4.5, 3.2, 3.8],  # km
    "precio": [211, 250, 293, 238, 229, 157, 320, 197, 234, 247, 176, 295, 194, 251, 223],    # miles €
})
predictores = ["superficie", "antiguedad", "distancia"]

# 1. Correlaciones entre predictores
print(viviendas[predictores].corr().round(3))
```

```text
            superficie  antiguedad  distancia
superficie       1.000       0.655     -0.662
antiguedad       0.655       1.000     -0.989
distancia       -0.662      -0.989      1.000
```

```python
# 2. VIF con statsmodels (la matriz debe incluir la constante)
X = sm.add_constant(viviendas[predictores])
for j, nombre in enumerate(predictores, start=1):
    print(f"VIF {nombre}: {variance_inflation_factor(X.values, j):.2f}")
```

```text
VIF superficie: 1.78
VIF antiguedad: 47.67
VIF distancia: 48.37
```

```python
# 3. VIF a mano: 1 / (1 - R²_j) de la regresión auxiliar
for nombre in predictores:
    otros = " + ".join(p for p in predictores if p != nombre)
    r2_j = smf.ols(f"{nombre} ~ {otros}", data=viviendas).fit().rsquared
    print(f"{nombre}: R²_j = {r2_j:.3f}, VIF = {1 / (1 - r2_j):.2f}")
```

```text
superficie: R²_j = 0.438, VIF = 1.78
antiguedad: R²_j = 0.979, VIF = 47.67
distancia: R²_j = 0.979, VIF = 48.37
```

Los dos modelos, lado a lado, y el contraste conjunto de antigüedad y distancia:

```python
completo = smf.ols("precio ~ superficie + antiguedad + distancia", data=viviendas).fit()
reducido = smf.ols("precio ~ superficie + distancia", data=viviendas).fit()
print(pd.DataFrame({
    "coef. completo": completo.params, "SE completo": completo.bse,
    "coef. reducido": reducido.params, "SE reducido": reducido.bse,
}).round(3))
```

```text
            coef. completo  SE completo  coef. reducido  SE reducido
Intercept          -16.326       54.995          38.199       20.197
antiguedad           2.021        1.897             NaN          NaN
distancia           -5.012        7.248         -12.588        1.398
superficie           2.669        0.188           2.671        0.189
```

```python
print(f"R²: completo {completo.rsquared:.3f}, reducido {reducido.rsquared:.3f}")
```

```text
R²: completo 0.987, reducido 0.985
```

```python
# Contraste conjunto H0: antigüedad = distancia = 0
conjunto = completo.f_test("antiguedad = 0, distancia = 0")
print(f"F = {float(conjunto.fvalue):.1f}, p-valor = {float(conjunto.pvalue):.1e}")
```

```text
F = 41.6, p-valor = 7.4e-06
```

La inestabilidad: se repite el ajuste quitando cada vez una vivienda, y las predicciones de los dos modelos para una vivienda típica y para una que rompe la pauta:

```python
def sin_una(formula):
    """Coeficientes del modelo ajustado 15 veces, quitando cada vez una vivienda."""
    return pd.DataFrame([smf.ols(formula, data=viviendas.drop(i)).fit().params
                         for i in viviendas.index])

print("Completo:\n", sin_una("precio ~ superficie + antiguedad + distancia").agg(["min", "max"]).round(2))
```

```text
Completo:
      Intercept  superficie  antiguedad  distancia
min     -39.61        2.60        0.92      -9.04
max      12.13        2.73        2.93      -1.36
```

```python
print("Reducido:\n", sin_una("precio ~ superficie + distancia").agg(["min", "max"]).round(2))
```

```text
Reducido:
      Intercept  superficie  distancia
min      33.15        2.56     -13.32
max      50.36        2.74     -12.22
```

```python
nuevas = pd.DataFrame({"superficie": [90, 90], "antiguedad": [17, 5], "distancia": [3.0, 1.5]},
                      index=["típica", "nueva en el centro"])
for nombre, modelo in [("completo", completo), ("reducido", reducido)]:
    pred = modelo.get_prediction(nuevas).summary_frame()
    print(nombre, pred[["mean", "mean_se"]].round(1).to_dict("index"))
```

```text
completo {'típica': {'mean': 243.2, 'mean_se': 2.8}, 'nueva en el centro': {'mean': 226.5, 'mean_se': 31.3}}
reducido {'típica': {'mean': 240.8, 'mean_se': 1.6}, 'nueva en el centro': {'mean': 259.7, 'mean_se': 2.8}}
```

> ✅ **Lo esencial**
>
> - Hay **multicolinealidad** cuando un predictor se puede predecir bien con los demás. Si la relación es exacta (colinealidad perfecta), el modelo no se puede estimar; si es fuerte, se estima con coeficientes imprecisos.
> - Sus consecuencias son errores estándar grandes, coeficientes inestables, signos contrarios a la intuición y variables que no son significativas por separado pero sí juntas. No sesga los coeficientes ni empeora el ajuste ni las predicciones de casos parecidos a los observados.
> - Se detecta con la matriz de correlaciones y, sobre todo, con el **VIF**, $1 / (1 - R^2_j)$: por encima de 5 hay que vigilar, y por encima de 10 los coeficientes afectados no son fiables.
> - En las viviendas, antigüedad y distancia tienen $r = -0.989$ y VIF de unos 48: el modelo no puede separar sus efectos, y la antigüedad sale con coeficiente positivo porque acompaña a la ubicación.
> - Remedios: no hacer nada si solo se quiere predecir, quitar una variable redundante con criterio de negocio, combinarlas en un índice o conseguir datos que rompan la pauta.

## Ejercicios

**1.** Calcula el VIF y el factor por el que se multiplica el error estándar de un coeficiente cuando la regresión auxiliar de su predictor sobre los demás tiene (a) $R^2_j = 0.5$, (b) $R^2_j = 0.9$ y (c) $R^2_j = 0.99$.

<details>
<summary>✅ <b>Solución</b></summary>

- (a) $\text{VIF} = 1 / 0.5 = 2$; el error estándar se multiplica por $\sqrt{2} = 1.41$.
- (b) $\text{VIF} = 1 / 0.1 = 10$; el error estándar se multiplica por $\sqrt{10} = 3.16$.
- (c) $\text{VIF} = 1 / 0.01 = 100$; el error estándar se multiplica por 10.

El VIF crece muy deprisa cuando $R^2_j$ se acerca a 1: pasar de 0.9 a 0.99 multiplica la varianza por 10 más.

</details>

**2.** Un modelo de las ventas de una tienda tiene dos predictores: la superficie de la tienda y su número de empleados, con una correlación de 0.95 entre ellos. ¿Cuál es el VIF de cada uno? ¿Qué esperas ver en el `summary()`?

<details>
<summary>✅ <b>Solución</b></summary>

Con dos predictores, $R^2_j = r^2 = 0.9025$ y $\text{VIF} = 1 / (1 - 0.9025) = 10.3$ para los dos. Los errores estándar son unas $\sqrt{10.3} = 3.2$ veces los que tendrían sin multicolinealidad.

En el `summary()` es probable ver un $R^2$ alto y un contraste F muy significativo, pero coeficientes con intervalos anchos y quizá alguno no significativo o con un signo extraño (por ejemplo, más empleados asociados con menos ventas a igual superficie). Antes de concluir nada, habría que contrastar las dos variables conjuntamente y pensar si interesa separarlas.

</details>

**3.** Un analista ajusta un modelo de la nota final de un curso con las horas de estudio, el número de ejercicios entregados y la asistencia. Obtiene $R^2 = 0.72$, un contraste F con p-valor de 0.0001, y ninguna de las tres variables es significativa por separado (p-valores de 0.12, 0.21 y 0.35). Concluye que ninguna de las tres influye en la nota y que el $R^2$ se debe a la casualidad. ¿Qué le dirías y qué haría a continuación?

<details>
<summary>✅ <b>Solución</b></summary>

La conclusión es contradictoria: el contraste F dice que el modelo en conjunto sí explica la nota. Lo que ocurre, casi seguro, es que las tres variables están muy relacionadas (quien estudia más también entrega más ejercicios y asiste más), y el modelo no sabe a cuál atribuir el efecto.

A continuación: (1) mirar la matriz de correlaciones y calcular los VIF; (2) contrastar conjuntamente las variables relacionadas; (3) si se confirma la multicolinealidad, quedarse con una variable con criterio (por ejemplo, la que la escuela puede cambiar) o combinarlas en un índice de "dedicación". Lo que no hay que hacer es quitar las tres.

</details>

**4.** Con Python, genera 50 observaciones de dos variables independientes $X_1$ y $X_2$, normales estándar, y una tercera $X_3 = X_1 + X_2 + \text{ruido}$, con el ruido normal de desviación típica 0.1 (usa `np.random.default_rng(1)`). Calcula la matriz de correlaciones y los VIF. ¿Habrías detectado el problema con la matriz?

<details>
<summary>✅ <b>Solución</b></summary>

```python
rng = np.random.default_rng(1)
x1 = rng.normal(0, 1, 50)
x2 = rng.normal(0, 1, 50)
x3 = x1 + x2 + rng.normal(0, 0.1, 50)
d = pd.DataFrame({"x1": x1, "x2": x2, "x3": x3})
print(d.corr().round(2))
X = sm.add_constant(d)
print([round(variance_inflation_factor(X.values, j), 1) for j in range(1, 4)])
```
Las correlaciones por parejas son 0.71 ($X_1$ con $X_3$), 0.67 ($X_2$ con $X_3$) y $-0.05$ ($X_1$ con $X_2$): ninguna pasa de 0.8, y con la regla práctica no habríamos sospechado. Pero los VIF son 90.6, 81.4 y 162.8: $X_3$ es casi exactamente la suma de las otras dos. La matriz de correlaciones solo ve relaciones de dos en dos; el VIF ve cualquier combinación lineal.

</details>

**5.** El director de la agencia lee el modelo con superficie y antigüedad, $\widehat{\text{Precio}} = -53.1 + 2.685 \times \text{Superficie} + 3.31 \times \text{Antigüedad}$, y propone subir 3300 € la tasación de los pisos por cada año de antigüedad. ¿Qué le responderías?

<details>
<summary>✅ <b>Solución</b></summary>

Que el coeficiente de la antigüedad no mide el efecto de envejecer, sino el de la ubicación: en los datos, las viviendas antiguas son las céntricas ($r = -0.989$ entre antigüedad y distancia), y la antigüedad hace de sustituta de la distancia, que no está en el modelo. Para una vivienda antigua **en la periferia**, la regla daría una tasación demasiado alta. Si se añade la distancia, el coeficiente de la antigüedad baja a 2.02 y deja de ser significativo, con un intervalo de $[-2.2,\ 6.2]$ que no permite ni saber su signo. Para tasar es mejor usar la distancia directamente, que es la variable que de verdad describe la ubicación.

</details>

**6.** La agencia tiene que tasar una vivienda de 90 m², construida hace 5 años y situada a 1.5 km del centro. El modelo con las tres variables predice 226.5 mil €, el de superficie y distancia 259.7 y el de superficie y antigüedad 205.0. Para una vivienda típica de 90 m², 17 años y 3 km, los tres predicen entre 240.8 y 244.7. Explica por qué en un caso los modelos coinciden y en el otro no, y qué le dirías a la agencia.

<details>
<summary>✅ <b>Solución</b></summary>

La vivienda típica sigue la pauta de los datos (antigüedad y distancia acordes), y en esa pauta los tres modelos explican el precio casi igual de bien: da igual cómo repartan el efecto de la ubicación entre las dos variables. La vivienda nueva en el centro rompe la pauta: no hay ninguna parecida en los datos. Cada modelo extrapola de una forma distinta según el reparto que haya hecho, y las predicciones se separan unos 55 mil €. El error estándar de la media en el modelo completo, 31.3 frente a 2.8 en la típica, cuantifica esa incertidumbre.

A la agencia le diría que los datos no permiten tasar esa vivienda con fiabilidad, porque no se sabe cuánto vale ser nueva a igual ubicación. Si cree que lo que cuenta es la ubicación, el modelo de superficie y distancia (259.7) es el más razonable, pero habría que decir que es una extrapolación y, si es posible, buscar ventas de viviendas nuevas en el centro.

</details>

[← El modelo y su interpretación](modelo.md) [Siguiente: Selección de variables →](seleccion.md)
