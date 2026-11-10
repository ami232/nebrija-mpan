# 5.4 Práctica guiada: regresión con distintos tipos de predictor

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/05-regresion-lineal-simple/practica/>

Esta práctica aplica todo el tema a cuatro casos con el mismo procedimiento: mirar los datos, medir la relación, ajustar la recta, interpretarla, comprobar el modelo y predecir. Los casos cambian el tipo de variable que hace de predictor: primero una cantidad (publicidad, temperatura), después una categoría con dos valores (género) y por último una categoría ordenada (nivel educativo).

**Cómo trabajar.** Abre el notebook y resuelve cada paso **antes** de desplegar el resultado. Los resultados están para que compruebes los tuyos, no para leerlos sin intentarlo.

**Notebook de la práctica** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/05-regresion-lineal-simple/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

> 💡 **Antes de empezar**
>
> - En Colab, haz *Archivo → Guardar una copia en Drive* para no perder tus cambios.
> - Los datos de los casos 2 a 4 se simulan con una semilla fija (`np.random.seed`), así que deberías obtener exactamente los mismos números que aquí.
> - Ten a mano las secciones "En Python" de [correlación](correlacion.md#en-python), [el modelo](modelo.md#en-python) y [residuos e inferencia](ajuste.md#en-python).

## Caso 1. Publicidad y ventas

Es el caso que recorre todo el tema: diez semanas de inversión en publicidad y ventas, en miles de euros.

### Paso 1. Gráfico de dispersión, covarianza y correlación

Dibuja el gráfico de dispersión antes de calcular nada. Después calcula la covarianza y la correlación primero con las fórmulas y luego con las funciones de NumPy o pandas.

<details>
<summary>💡 <b>Pista</b></summary>

La covarianza con divisor $n$ es `((X - X.mean()) * (Y - Y.mean())).sum() / n`. Para la correlación, divide entre `X.std(ddof=0) * Y.std(ddof=0)`: el mismo divisor arriba y abajo. Para comprobarlo, `np.cov(X, Y, ddof=0)[0, 1]` y `np.corrcoef(X, Y)[0, 1]`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

El gráfico muestra una relación creciente y casi en línea recta. La covarianza es **37.2** (miles de €)² con divisor $n$ y la correlación, **$r = 0.994$**: una relación lineal positiva muy fuerte. Las desviaciones típicas con divisor $n$ son 3.49 (publicidad) y 10.71 (ventas).

</details>

### Paso 2. Ajustar e interpretar la recta

Ajusta la regresión con `scipy.stats.linregress` e interpreta el intercepto, la pendiente y $R^2$.

<details>
<summary>💡 <b>Pista</b></summary>

`resultado = stats.linregress(X, Y)` devuelve `slope`, `intercept`, `rvalue`, `pvalue` y `stderr`. El $R^2$ es `resultado.rvalue ** 2`. Al interpretar el intercepto, comprueba si $X = 0$ está dentro del rango de los datos.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

$\hat{\beta}_0 = 14.26$, $\hat{\beta}_1 = 3.05$, $r = 0.9936$, $R^2 = 0.987$, p-valor $= 7.3 \times 10^{-9}$ y error estándar de la pendiente 0.122.

- **Pendiente:** cada 1000 € más de publicidad se asocian, en promedio, con unos 3050 € más de ventas.
- **Intercepto:** 14 260 € serían las ventas con publicidad cero, pero los datos van de 8 a 20 mil €: es una extrapolación sin interpretación práctica.
- **$R^2$:** el 98.7 % de la variabilidad de las ventas se asocia con la publicidad.

</details>

### Paso 3. La recta sobre los datos

Dibuja los puntos y la recta ajustada en el mismo gráfico. Como extra, dibuja los residuos como segmentos verticales entre cada punto y la recta.

<details>
<summary>💡 <b>Pista</b></summary>

Genera una rejilla con `np.linspace(X.min() - 1, X.max() + 1, 100)`, calcula la recta sobre ella y dibújala con `plt.plot`. Los segmentos se dibujan con `plt.plot([x_i, x_i], [y_i, y_pred_i])` para cada punto.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

La recta pasa muy cerca de todos los puntos; los segmentos son cortos y aparecen por encima y por debajo de la recta sin un orden claro.

</details>

### Paso 4. Análisis de residuos

Calcula los residuos, comprueba que suman cero y dibuja los tres gráficos de diagnóstico: residuos frente a valores predichos, histograma de residuos y gráfico Q-Q con `stats.probplot(residuos, dist="norm", plot=ax)`.

<details>
<summary>💡 <b>Pista</b></summary>

Repasa en [Diagnóstico: mirar los residuos](ajuste.md#diagnóstico-mirar-los-residuos) qué patrones hay que buscar. Con diez puntos, el histograma dice poco; el gráfico Q-Q es más informativo.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

La suma de los residuos es cero (salvo errores de redondeo del orden de $10^{-14}$). Van de $-1.91$ a $+1.99$, con desviación típica 1.28 (con `ddof=1`; el error típico de la regresión, que divide entre $n - 2$, es 1.35). El gráfico de residuos no muestra curva ni embudo y el Q-Q sigue razonablemente la diagonal. Con diez observaciones no se puede afirmar mucho sobre la normalidad, pero no hay indicios de problemas graves.

</details>

### Paso 5. Predicciones

Predice las ventas para inversiones de 5 000 €, 22 000 € y 30 000 €. ¿Qué precauciones hay que tener?

<details>
<summary>💡 <b>Pista</b></summary>

Las inversiones van en miles de €: usa 5, 22 y 30. Antes de dar cada predicción, compara con el rango de la publicidad observada.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

Las predicciones son **29.49**, **81.29** y **105.66** miles de €. Las tres están **fuera del rango observado** (8 a 20 mil €), así que son extrapolaciones. La de 22 mil € está cerca del borde y es la más creíble. La de 30 mil € puede sobreestimar las ventas si la publicidad tiene rendimientos decrecientes, y la de 5 mil € puede fallar si por debajo de cierta inversión la publicidad apenas tiene efecto. Al presentarlas, hay que indicar que quedan fuera de los datos.

</details>

## Caso 2. Temperatura y ventas de helados

Una heladería registra la temperatura máxima (°C) y las ventas (€) de 20 días de verano. Ahora haces tú el análisis completo, sin pasos intermedios: estadísticos descriptivos, gráfico de dispersión, correlación, recta, interpretación, $R^2$ y residuos. Termina prediciendo las ventas de un día con 35 °C.

<details>
<summary>💡 <b>Pista</b></summary>

`datos_helados.describe()` da los descriptivos. Para el resto, repite el caso 1. Antes de la predicción, comprueba que 35 °C está dentro del rango de temperaturas observado.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

- **Descriptivos:** temperatura media 28.24 °C (de 20.37 a 37.46 °C) y ventas medias 465.40 €.
- **Correlación:** $r = 0.945$.
- **Recta:** $\widehat{\text{Ventas}} = 66.13 + 14.14 \times \text{Temperatura}$, con $R^2 = 0.893$ y p-valor $3.6 \times 10^{-10}$.
- **Interpretación:** cada grado más de temperatura máxima se asocia con unos 14.14 € más de ventas diarias. El intercepto (66.13 € a 0 °C) no tiene sentido práctico: no hay días de verano a 0 °C. La temperatura explica el 89.3 % de la variabilidad de las ventas.
- **Residuos:** sin patrón visible, y el Q-Q sigue la diagonal. El error típico de la regresión es de unos 27.8 €.
- **Predicción para 35 °C:** **560.99 €**. Está dentro del rango observado, así que es una predicción respaldada por los datos.

</details>

## Caso 3. Un predictor con dos categorías: género y salario

Datos simulados de los salarios de 30 empleados, con su género. Para usar el género como predictor hay que codificarlo con una variable indicadora, como en [Cuando el predictor es una categoría](modelo.md#cuando-el-predictor-es-una-categoría).

### Paso 1. Visualizar los dos grupos

Compara la distribución del salario en los dos grupos con un diagrama de caja (boxplot) o un diagrama de violín.

<details>
<summary>💡 <b>Pista</b></summary>

`datos_salario_genero.boxplot(column="Salario", by="Genero")` o, con seaborn, `sns.boxplot(data=datos_salario_genero, x="Genero", y="Salario")`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

Hay 17 mujeres y 13 hombres. Las cajas se solapan en parte, pero la mediana de los hombres está claramente por encima.

</details>

### Paso 2. Codificar y ajustar

Crea la variable `Genero_Hombre`, que vale 1 para los hombres y 0 para las mujeres, y ajusta la regresión del salario sobre ella. Interpreta $\hat{\beta}_0$ y $\hat{\beta}_1$ y compáralos con las medias de cada grupo.

<details>
<summary>💡 <b>Pista</b></summary>

`(datos_salario_genero["Genero"] == "Hombre").astype(int)` crea la variable. Las medias por grupo salen con `groupby("Genero")["Salario"].mean()`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

$\hat{\beta}_0 = 30\,800$ € y $\hat{\beta}_1 = 5\,153$ €, con $R^2 = 0.50$ y p-valor $1.3 \times 10^{-5}$.

- $\hat{\beta}_0$ es el salario medio del grupo de referencia (mujeres): **30 800 €**.
- $\hat{\beta}_1$ es la diferencia de medias: los hombres de la muestra cobran de media **5 153 €** más, es decir, **35 954 €**.

Coinciden exactamente con las medias de `groupby`. Los datos se simularon con una diferencia real de 3 000 €; la estimada es mayor por la variabilidad de una muestra de solo 30 personas. El intervalo de confianza al 95 % de la pendiente, $5153 \pm 2.048 \cdot 974 = [3158,\ 7149]$ €, es ancho, y en este caso no contiene el valor usado en la simulación. Es uno de los intervalos que, por azar, no aciertan: con un nivel del 95 %, ocurre en una de cada veinte muestras.

Un apunte de interpretación: en datos reales, esta diferencia bruta no mide por sí sola una discriminación salarial, porque puede haber variables de confusión (puesto, antigüedad, jornada). Para separar esos efectos hace falta la regresión múltiple del [tema 6](../06-regresion-lineal-multiple/index.md).

</details>

## Caso 4. Un predictor ordinal: nivel educativo y salario

Datos simulados de 60 salarios, 15 por cada nivel educativo: secundaria, grado, máster y doctorado.

### Paso 1. Visualizar

Dibuja el salario por nivel educativo, con los niveles en su orden natural, y calcula el salario medio de cada nivel.

<details>
<summary>💡 <b>Pista</b></summary>

En seaborn, el parámetro `order=["Secundaria", "Grado", "Máster", "Doctorado"]` fija el orden. Sin él, los niveles salen en orden alfabético.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

Salarios medios: secundaria **24 791 €**, grado **33 436 €**, máster **46 637 €** y doctorado **54 853 €**. El salario crece con el nivel, pero no a saltos iguales.

</details>

### Paso 2. Codificar como 0, 1, 2, 3 y ajustar

Codifica los niveles como 0 (secundaria), 1 (grado), 2 (máster) y 3 (doctorado), ajusta la regresión e interpreta la pendiente. Compara el salario que predice el modelo para cada nivel con la media real. ¿Es razonable suponer que el efecto es lineal?

<details>
<summary>💡 <b>Pista</b></summary>

Usa un diccionario y `.map()`. Para valorar la linealidad, calcula los saltos entre medias consecutivas y compáralos con la pendiente.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

$\hat{\beta}_0 = 24\,421$ € y $\hat{\beta}_1 = 10\,339$ €, con $R^2 = 0.917$. Cada nivel educativo más se asocia, en promedio, con unos 10 339 € más de salario.

| Nivel | Predicción del modelo | Media real |
|---|---|---|
| Secundaria (0) | 24 421 € | 24 791 € |
| Grado (1) | 34 760 € | 33 436 € |
| Máster (2) | 45 099 € | 46 637 € |
| Doctorado (3) | 55 438 € | 54 853 € |

Los saltos reales entre niveles son de 8 645 €, 13 201 € y 8 216 €: el paso de grado a máster es bastante mayor que los otros dos. El modelo lineal los resume en un único salto medio de 10 339 €, así que subestima el máster y sobreestima el grado. Es una buena aproximación global ($R^2$ alto), pero si interesa el efecto de cada nivel por separado, es mejor usar una variable indicadora por nivel (regresión múltiple, [tema 6](../06-regresion-lineal-multiple/index.md)).

</details>

## Lo que deberías llevarte

- El procedimiento es siempre el mismo: **dibujar, medir, ajustar, interpretar, comprobar y predecir**, y cada paso se apoya en el anterior.
- La pendiente se interpreta en las unidades del problema y como una **asociación media**, no como un efecto causal.
- Las predicciones solo están respaldadas **dentro del rango** de los datos.
- Con un predictor binario, la regresión compara **medias de dos grupos**; con uno ordinal codificado como 0, 1, 2…, supone saltos iguales entre niveles, y eso hay que comprobarlo.

[← Residuos, R² e inferencia](ajuste.md) [Volver al tema ↑](index.md)
