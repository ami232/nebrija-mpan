# 6.5 Práctica guiada: regresión múltiple de principio a fin

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/06-regresion-lineal-multiple/practica/>

Esta práctica aplica todo el tema a dos conjuntos de datos con el mismo procedimiento: explorar los datos, ajustar el modelo, leer el `summary`, interpretar cada coeficiente con las demás variables constantes, detectar la multicolinealidad, comparar modelos y revisar los residuos. El primero son 220 viviendas simuladas, con variables continuas, una categórica (la zona) y una variable redundante (las habitaciones). El segundo son datos reales de California, con variables continuas, una categórica y una ordinal.

**Cómo trabajar.** Abre los notebooks y resuelve cada paso **antes** de desplegar el resultado. Los resultados están para que compruebes los tuyos, no para leerlos sin intentarlo.

**Parte 1: viviendas simuladas** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/06-regresion-lineal-multiple/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

**Parte 2: viviendas de California** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/06-regresion-lineal-multiple/exercises_applied.ipynb) &nbsp; [descargar .ipynb](exercises_applied.ipynb)

> 💡 **Antes de empezar**
>
> - En Colab, haz *Archivo → Guardar una copia en Drive* para no perder tus cambios.
> - Los datos de la parte 1 se simulan con una semilla fija (`np.random.seed(42)`) en la primera celda de código. Ejecuta las celdas **en orden** desde el principio: si vuelves a ejecutar solo la celda que genera los datos, la semilla ya ha avanzado y obtendrás otras viviendas y otros números.
> - Ten a mano [el modelo y su interpretación](modelo.md), [multicolinealidad](multicolinealidad.md), [selección de variables](seleccion.md) y [diagnóstico del modelo](diagnostico.md).

## Parte 1. Viviendas simuladas

El notebook genera 220 viviendas con su precio (miles de €), superficie (m²), antigüedad (años), distancia al centro (km), zona (periferia, semicentro o centro) y número de habitaciones. Como los datos son simulados, conocemos los efectos reales con los que se generaron: 2.8 mil € por m², $-1.1$ por año de antigüedad, $-7.5$ por km, 15 mil € más en el semicentro y 35 mil € más en el centro que en la periferia, y **ningún efecto** de las habitaciones. Las habitaciones se generan a partir de la superficie, así que están muy relacionadas con ella. Eso permite comprobar qué recupera cada modelo.

### Paso 1. Explorar los datos

Ejecuta las secciones 1 y 2 del notebook. Mira los descriptivos, cuenta cuántas viviendas hay en cada zona y lee la matriz de correlaciones. ¿Qué variable está más relacionada con el precio? ¿Qué par de variables explicativas puede dar problemas de multicolinealidad?

<details>
<summary>💡 <b>Pista</b></summary>

`df["zona"].value_counts()` cuenta las viviendas por zona. En la matriz de correlaciones, mira primero la fila del precio y después las correlaciones entre las variables explicativas.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

- **Descriptivos:** precio medio de 278.6 mil € (de 156.8 a 475.2), superficie media de 90.1 m², antigüedad media de 12.4 años y distancia media de 2.93 km.
- **Zonas:** 108 viviendas en la periferia, 71 en el semicentro y 41 en el centro.
- **Correlaciones con el precio:** superficie 0.92, habitaciones 0.74, distancia $-0.24$ y antigüedad $-0.16$.
- **Entre explicativas:** superficie y habitaciones tienen una correlación de **0.83**: es el par que puede dar problemas. La antigüedad y la distancia apenas están correlacionadas con las demás (entre $-0.08$ y $-0.01$).

Que la antigüedad y la distancia tengan correlaciones pequeñas con el precio no significa que no importen: la superficie explica tanta variabilidad del precio que las tapa. Para ver su efecto hace falta el modelo múltiple.

</details>

### Paso 2. El modelo base y su `summary`

Ajusta `precio ~ superficie + antiguedad + distancia` (sección 3) y lee el `summary` bloque a bloque: ¿el modelo explica el precio en conjunto?, ¿qué variables son significativas?, ¿qué dice cada coeficiente? Compara los coeficientes con los efectos reales de la simulación.

<details>
<summary>💡 <b>Pista</b></summary>

El contraste F global está arriba a la derecha (`F-statistic`, `Prob (F-statistic)`). Cada coeficiente se interpreta como el cambio medio del precio cuando su variable sube una unidad **y las otras dos no cambian**. Repasa [cómo se lee el `summary`](modelo.md).

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

$R^2 = 0.879$, $R^2$ ajustado $= 0.877$, $F = 521.3$ con p-valor $1.3 \times 10^{-98}$, AIC $= 1938.8$ y BIC $= 1952.4$. Las tres variables son significativas (p-valor $< 0.001$):

| Variable | Coeficiente | IC 95 % | Efecto real |
|---|---|---|---|
| superficie | 2.649 | [2.510, 2.788] | 2.8 |
| antigüedad | $-0.888$ | [$-1.277$, $-0.499$] | $-1.1$ |
| distancia | $-7.841$ | [$-10.017$, $-5.665$] | $-7.5$ |

- **Superficie:** con la misma antigüedad y distancia, cada m² más se asocia, en promedio, con unos 2 650 € más de precio.
- **Antigüedad:** con la misma superficie y distancia, cada año más se asocia con unos 890 € menos.
- **Distancia:** con la misma superficie y antigüedad, cada km más lejos del centro se asocia con unos 7 840 € menos.

El intervalo de la superficie **no contiene** el efecto real de 2.8: al modelo le falta la zona, y la zona influye en el precio. El paso siguiente lo corrige. El número de condición (562) no dispara el aviso de multicolinealidad.

</details>

### Paso 3. Añadir la zona con variables dummy

Ajusta el modelo con `C(zona)` (sección 4). ¿Qué categoría es la de referencia? Interpreta los dos coeficientes de zona y compara el $R^2$ ajustado y el AIC con los del modelo base.

<details>
<summary>💡 <b>Pista</b></summary>

La categoría de referencia es la que **no** aparece en la tabla de coeficientes. statsmodels elige la primera en orden alfabético. Cada coeficiente de zona es la diferencia de precio medio con la referencia, a igualdad de superficie, antigüedad y distancia.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

La referencia es **centro** (la primera en orden alfabético). Los coeficientes de zona son $-32.84$ (periferia) y $-20.73$ (semicentro), los dos con p-valor $< 0.001$: con la misma superficie, antigüedad y distancia, una vivienda en la periferia vale de media unos 32 800 € menos que una en el centro, y una en el semicentro, unos 20 700 € menos. Los efectos reales eran $-35$ y $-20$.

El modelo mejora mucho: $R^2$ ajustado de 0.877 a **0.923**, AIC de 1938.8 a **1839.0** y BIC de 1952.4 a **1859.4**. Los demás coeficientes se acercan a los reales: superficie 2.748 (IC [2.636, 2.860], que ya contiene el 2.8), antigüedad $-1.024$ y distancia $-7.070$.

</details>

### Paso 4. Una variable redundante: las habitaciones

Añade `habitaciones` al modelo base y calcula los VIF (sección 5). ¿Qué les pasa al coeficiente de la superficie y a su error estándar? ¿Es significativa la variable habitaciones? Compara con su coeficiente en una regresión simple, `precio ~ habitaciones`.

<details>
<summary>💡 <b>Pista</b></summary>

Recuerda que $\sqrt{\text{VIF}}$ es cuántas veces se multiplica el error estándar de un coeficiente por la multicolinealidad. Compara el `std err` de la superficie en los dos modelos. Repasa [multicolinealidad](multicolinealidad.md).

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

- **VIF:** superficie 3.16, habitaciones 3.16, antigüedad 1.00 y distancia 1.01. Están por debajo de 5, el umbral habitual de alarma.
- **Error estándar de la superficie:** pasa de 0.070 a **0.124**, 1.77 veces mayor ($\sqrt{3.16} = 1.78$). Su coeficiente apenas cambia (2.787).
- **Habitaciones:** coeficiente de $-2.92$ mil € con p-valor 0.181 e intervalo [$-7.20$, $1.37$]: no es significativa. Con la misma superficie, una habitación más no se asocia con un precio distinto, que es justo como se simularon los datos.
- **Regresión simple:** `precio ~ habitaciones` da una pendiente de **+38.3** mil € por habitación. Esa pendiente recoge el efecto de la superficie: las viviendas con más habitaciones son más grandes.
- **Comparación:** el $R^2$ ajustado no cambia (0.877) y el AIC sube un poco (1939.0 frente a 1938.8). La variable no aporta nada.

Aunque un VIF de 3 no se considere alto, ya duplica casi el error estándar. Una variable que no aporta información nueva solo añade imprecisión.

</details>

### Paso 5. Interacción entre superficie y zona

Ajusta `precio ~ superficie * C(zona) + antiguedad + distancia` (sección 6). ¿Cambia la pendiente de la superficie según la zona? ¿Qué les pasa a los coeficientes de zona? Contrasta la interacción con un contraste F de modelos anidados, comparando con el modelo del paso 3.

<details>
<summary>💡 <b>Pista</b></summary>

`superficie * C(zona)` incluye la superficie, la zona y su interacción. El contraste F se calcula con `from statsmodels.stats.anova import anova_lm` y `anova_lm(modelo_zona, modelo_inter)`. Con la interacción, el coeficiente de una zona es la diferencia con el centro **para una vivienda de 0 m²**.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

Los términos de interacción valen $-0.197$ (periferia, p-valor 0.271) y $-0.140$ (semicentro, p-valor 0.457): no hay evidencia de que el precio por m² cambie según la zona. El contraste F conjunto da $F = 0.62$ y p-valor **0.54**.

Los coeficientes de zona pasan a $-15.6$ y $-8.7$ y dejan de ser significativos, porque ahora miden la diferencia con el centro en una vivienda de 0 m², muy lejos de los datos. El número de condición sube a 2 660 y statsmodels avisa de multicolinealidad: la interacción está muy correlacionada con la dummy de su zona. El $R^2$ ajustado baja a 0.922 y el AIC sube a 1841.7: la interacción no compensa.

</details>

### Paso 6. Diagnóstico de los residuos

Ejecuta la sección 7 con el modelo con zona. Mira los dos gráficos y lee los contrastes de Anderson-Darling (normalidad) y Breusch-Pagan (varianza constante).

<details>
<summary>💡 <b>Pista</b></summary>

En los dos contrastes, $H_0$ es que el supuesto se cumple, así que un p-valor alto es una buena noticia. Repasa [diagnóstico del modelo](diagnostico.md).

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

El gráfico de residuos frente a valores ajustados no muestra curva ni embudo, y el Q-Q sigue la diagonal.

- **Anderson-Darling:** estadístico 0.167, p-valor **0.938**. No hay evidencia contra la normalidad.
- **Breusch-Pagan:** estadístico 10.62, p-valor **0.059**. Está cerca de 0.05, pero no lo supera, y el gráfico no muestra embudo.

Los datos se simularon con varianza constante, así que el 0.059 es fruto del azar: un p-valor cercano al umbral no basta para concluir que hay un problema. Se decide mirando también los gráficos.

</details>

### Paso 7. Evaluación con datos nuevos

Ejecuta la sección 8: el modelo se ajusta con el 75 % de las viviendas y se evalúa con el 25 % restante. Compara el RMSE del modelo con zona y sin ella.

<details>
<summary>💡 <b>Pista</b></summary>

El RMSE es la raíz del error cuadrático medio, en las unidades del precio. Para valorarlo, compáralo con el ruido de la simulación: los precios se generaron con un error aleatorio de desviación típica 15 mil €.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

RMSE en test de **17.42** mil € con zona y de **21.94** mil € sin zona. La zona mejora las predicciones sobre viviendas que el modelo no ha visto, no solo el ajuste. El error con zona se acerca al ruido de 15 mil € que ningún modelo puede eliminar.

</details>

### Paso 8. Comparar los cuatro modelos

Ejecuta la sección 9 y elige un modelo.

<details>
<summary>✅ <b>Resultado</b></summary>

| Modelo | AIC | BIC | $R^2$ ajustado |
|---|---|---|---|
| con zona | **1839.0** | **1859.4** | **0.923** |
| con interacción | 1841.7 | 1868.9 | 0.922 |
| base | 1938.8 | 1952.4 | 0.877 |
| base + habitaciones | 1939.0 | 1955.9 | 0.877 |

Los tres criterios eligen el **modelo con zona**: superficie, antigüedad, distancia y zona. Es exactamente la estructura con la que se simularon los datos. Las habitaciones y la interacción empeoran el AIC y el BIC.

</details>

### Paso 9. Ejercicios propuestos

Resuelve los seis ejercicios de la sección 11 del notebook. En el ejercicio 6, usa **periferia** como referencia.

<details>
<summary>💡 <b>Pistas</b></summary>

- **Ejercicio 2:** con `np.log(precio)` como variable respuesta, $100 \cdot \hat{\beta}$ es aproximadamente el cambio **porcentual** del precio por unidad de la variable. El $R^2$ y el AIC no se pueden comparar con los del modelo en euros, porque la variable respuesta es otra.
- **Ejercicio 3:** para calcular el VIF con la zona en el modelo, crea las dummies con `pd.get_dummies(df[[...]], drop_first=True, dtype=float)` antes de `sm.add_constant`.
- **Ejercicio 4:** usa `anova_lm` como en el paso 5.
- **Ejercicio 5:** cada `random_state` da una partición distinta, así que tus números dependerán del que elijas.
- **Ejercicio 6:** `C(zona, Treatment(reference="periferia"))`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

**1. Sin la distancia.** El $R^2$ ajustado baja de 0.923 a 0.900, el AIC sube de 1839.0 a 1895.1 y el BIC de 1859.4 a 1912.1: la distancia aporta. Los demás coeficientes casi no cambian (superficie 2.790, antigüedad $-1.017$), porque la distancia apenas está correlacionada con las otras variables. Lo que empeora es la precisión: el error de los residuos aumenta.

**2. Logaritmo del precio.** Coeficientes: superficie 0.0100 (cada m² más, un 1.0 % más de precio), antigüedad $-0.0039$ ($-0.39$ % por año), distancia $-0.0267$ ($-2.6$ % por km), periferia $-0.123$ y semicentro $-0.080$ (un 11.5 % y un 7.7 % menos que el centro). $R^2$ ajustado de 0.908. Los residuos están bien en los dos modelos (Anderson-Darling: p-valor 0.43 con logaritmo, 0.94 sin él; Breusch-Pagan: 0.60 y 0.059). Como los datos se simularon con efectos que se suman en euros, la transformación no es necesaria; cambia la forma de leer los coeficientes, de euros a porcentajes.

**3. Habitaciones junto a la superficie, con la zona en el modelo.** Habitaciones: coeficiente $-0.13$ con p-valor 0.94. VIF de 3.17 (superficie) y 3.22 (habitaciones); las dummies de zona, alrededor de 1.9. El error estándar de la superficie pasa de 0.057 a 0.099. $R^2$ ajustado de 0.922 (frente a 0.923) y AIC de 1841.0 (frente a 1839.0). Es la misma conclusión que en el paso 4: la variable sobra.

**4. Interacción entre antigüedad y zona.** Términos de interacción de $-0.07$ (periferia, p-valor 0.87) y $-0.22$ (semicentro, p-valor 0.64). Contraste F frente al modelo con zona: $F = 0.12$, p-valor 0.88. AIC de 1842.8. No hay evidencia de que el efecto de la antigüedad dependa de la zona.

**5. Otra partición.** Por ejemplo, con `random_state=0` el RMSE en test es 15.74 sin interacción y 15.88 con ella, y con `random_state=1`, 15.85 y 16.09. En las particiones que hemos probado, el modelo con interacción predice algo peor: añade parámetros que no corresponden a nada real.

**6. Periferia como referencia.** Centro $+32.84$ y semicentro $+12.11$ mil € respecto a la periferia, los dos con p-valor $< 0.001$. El intercepto cambia (54.5) y los demás coeficientes no. Es el mismo modelo, con las mismas predicciones: solo cambia la comparación que muestra la tabla.

Compara con los precios medios sin ajustar: centro 289.9, periferia 277.2 y semicentro **274.2** mil €. Sin ajustar, el semicentro parece más barato que la periferia; con la misma superficie, antigüedad y distancia, es 12 100 € más caro. Las comparaciones brutas mezclan la zona con las demás características de las viviendas de cada zona.

</details>

## Parte 2. Datos reales: viviendas de California

El notebook carga el conjunto *California Housing* de scikit-learn: 20 640 distritos de California del censo de 1990. Cada fila es un **distrito**, no una vivienda. La variable respuesta, `MedHouseVal`, es el valor mediano de las viviendas del distrito, en cientos de miles de dólares (2.5 son 250 000 $). Entre las explicativas, `MedInc` es la renta mediana del distrito en decenas de miles de dólares, y `AveRooms` y `AveBedrms`, el número medio de habitaciones y de dormitorios por vivienda.

El notebook crea dos variables nuevas para practicar la codificación: `Region` (categórica: Norte si la latitud es mayor que 36, Sur si no) y `Antiguedad` (ordinal: Nueva hasta 15 años, Media de 16 a 35 y Antigua más de 35).

### Paso 1. Tipos de variables y exploración

Ejecuta las secciones 1 a 4. Cuenta los distritos de cada región y de cada nivel de antigüedad, calcula el valor medio por grupo y mira el histograma de `MedHouseVal`. ¿Ves algo raro en el extremo derecho?

<details>
<summary>💡 <b>Pista</b></summary>

`df["Region"].value_counts()` y `df.groupby("Region")["MedHouseVal"].mean()`. En el histograma, fíjate en la última barra.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

- **Región:** 11 816 distritos en el Sur y 8 824 en el Norte. Valor medio: 2.166 en el Sur y 1.938 en el Norte.
- **Antigüedad:** 3 287 distritos nuevos, 11 009 de antigüedad media y 6 344 antiguos. Valor medio: 1.922, 2.037 y 2.199: los distritos antiguos son **más caros**, como las viviendas antiguas y céntricas del caso de las quince viviendas.
- **No hay valores nulos.**
- **Histograma:** hay una barra alta en el extremo derecho. 992 distritos tienen exactamente el valor máximo, 5.00001: los valores por encima de 500 000 $ se registraron como 500 000. El modelo no puede predecir bien ese grupo, porque su valor real es desconocido.
- **Correlaciones:** `MedInc` es la variable más relacionada con el valor (0.69). Entre explicativas, `AveRooms` y `AveBedrms` tienen una correlación de 0.85, y la latitud y la longitud, de $-0.92$.

</details>

### Paso 2. Codificar las variables categórica y ordinal

Ejecuta la sección 5. ¿Cuál es la categoría de referencia de la región? ¿Qué supone codificar la antigüedad como 0, 1 y 2?

<details>
<summary>💡 <b>Pista</b></summary>

`drop_first=True` elimina la primera categoría en orden alfabético. Una codificación 0, 1, 2 supone que el salto de Nueva a Media vale lo mismo que el de Media a Antigua.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

La región queda como una sola dummy, `Region_Sur`, con **Norte** como referencia. La antigüedad queda como una sola columna numérica, `Antiguedad_ord`, con un único coeficiente para cada salto de nivel. En total hay 9 variables explicativas para los 20 640 distritos.

</details>

### Paso 3. Regresión simple frente a múltiple

Ejecuta las secciones 6 y 7: primero con `MedInc` sola y después con las 9 variables. Compara el $R^2$ y el RMSE.

<details>
<summary>💡 <b>Pista</b></summary>

El RMSE está en las unidades de `MedHouseVal`: multiplícalo por 100 000 para tenerlo en dólares. Aunque el texto del notebook menciona train y test, los dos modelos se ajustan y se evalúan con **todos** los datos.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

| Modelo | $R^2$ | RMSE |
|---|---|---|
| solo `MedInc` | 0.4734 | 0.84 (84 000 $) |
| 9 variables | 0.6046 | 0.73 (73 000 $) |

Las 8 variables añadidas suben el $R^2$ en 13 puntos y bajan el error típico de predicción en unos 11 000 $. Como se evalúa con los mismos datos del ajuste, es una medida optimista; con 20 640 filas y 9 variables, la diferencia con datos nuevos sería pequeña.

</details>

### Paso 4. Diagnóstico

Ejecuta la sección 8 y describe los tres gráficos.

<details>
<summary>💡 <b>Pista</b></summary>

En el gráfico de residuos, busca una línea diagonal de puntos y relaciónala con lo que viste en el histograma del paso 1.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

- **Residuos frente a predicción:** hay una línea diagonal de puntos que baja hacia la derecha. Son los 992 distritos con el valor recortado en 5.00001: su valor observado es siempre el mismo, así que su residuo baja a medida que sube la predicción.
- **Q-Q e histograma:** los residuos tienen colas más largas que una normal y son asimétricos (en el `summary`: asimetría 1.09, curtosis 6.3 y Jarque-Bera de 13 645 con p-valor 0).
- **Durbin-Watson** (en el `summary`): 0.87, lejos de 2. Las filas están ordenadas geográficamente y los residuos de distritos vecinos se parecen, así que los errores no son independientes.

Con tantos datos, los coeficientes siguen estimándose bien, pero los errores estándar y los p-valores del `summary` son demasiado optimistas. Es un modelo útil para describir, no para fiarse de cada p-valor.

</details>

### Paso 5. Multicolinealidad

Ejecuta la sección 9 y ordena las variables por VIF. ¿Por qué tiene `Region_Sur` un VIF tan alto?

<details>
<summary>💡 <b>Pista</b></summary>

Mira cómo se construyó `Region` a partir de otra variable del modelo.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

| Variable | VIF |
|---|---|
| Latitude | 12.97 |
| Longitude | 9.55 |
| Region_Sur | 8.59 |
| AveRooms | 8.35 |
| AveBedrms | 7.01 |
| MedInc | 2.50 |
| Antiguedad_ord | 1.21 |
| Population | 1.13 |
| AveOccup | 1.01 |

`Region_Sur` se calcula a partir de la latitud (vale 1 si la latitud es menor o igual que 36), así que la latitud y la longitud la explican casi por completo (correlación de $-0.93$ con la latitud). `AveRooms` y `AveBedrms` miden casi lo mismo (correlación de 0.85). Sin `Region_Sur`, los VIF de la latitud y la longitud bajan a 9.20 y 8.84.

</details>

### Paso 6. Interpretar los coeficientes

Ejecuta la sección 10. Interpreta los coeficientes de `MedInc`, `Antiguedad_ord` y `Region_Sur`. Después compara los signos de `AveRooms` y `AveBedrms` con lo que esperarías, y con la pendiente de la regresión simple `MedHouseVal ~ AveRooms`.

<details>
<summary>💡 <b>Pista</b></summary>

Cada coeficiente es el cambio de `MedHouseVal` (en cientos de miles de dólares) por unidad de su variable, con las demás constantes. Para `AveRooms`, "las demás constantes" incluye el número de dormitorios.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

$R^2$ ajustado de 0.604 y número de condición de $2.84 \times 10^5$, con el aviso de multicolinealidad.

- **`MedInc` (0.434):** con todo lo demás igual, cada 10 000 $ más de renta mediana se asocian con unos 43 400 $ más de valor mediano.
- **`Antiguedad_ord` (0.135):** cada nivel más de antigüedad se asocia con unos 13 500 $ más, con las demás variables constantes. Con dummies para cada nivel (Nueva como referencia), los coeficientes son 0.117 (Media) y 0.264 (Antigua): saltos de 0.117 y 0.147, parecidos, así que la codificación 0, 1, 2 es razonable.
- **`Region_Sur` (0.263):** con la misma latitud, longitud y demás variables, los distritos del Sur valen unos 26 300 $ más. Como `Region_Sur` depende de la latitud, este coeficiente mide un salto en la latitud 36 por encima de la tendencia lineal de la latitud: es difícil de interpretar.
- **`AveRooms` ($-0.107$) y `AveBedrms` ($+0.640$):** en la regresión simple, `AveRooms` tiene pendiente **positiva** (0.071). En la múltiple, su coeficiente es el efecto de una habitación más **con el mismo número de dormitorios**, es decir, de una habitación que no es dormitorio; y el de `AveBedrms`, el de convertir una habitación en dormitorio sin cambiar el total. Con una correlación de 0.85, estos coeficientes son poco estables y no se deben leer por separado.

</details>

### Paso 7. Comparar modelos

Ajusta con statsmodels dos variantes del modelo de la sección 10 y compáralas con él por $R^2$ ajustado, AIC y BIC: (a) sin `Region_Sur`; (b) con la antigüedad como categórica (una dummy por nivel) en lugar de ordinal.

<details>
<summary>💡 <b>Pista</b></summary>

Con la interfaz de fórmulas es más cómodo: `smf.ols("MedHouseVal ~ MedInc + ... + Antiguedad_ord + Region_Sur", data=df)`. Para la variante (b), sustituye `Antiguedad_ord` por `C(Antiguedad, Treatment("Nueva"))`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

| Modelo | $R^2$ ajustado | AIC | BIC |
|---|---|---|---|
| sección 10 (9 variables) | 0.6044 | 45 354.7 | **45 434.1** |
| (a) sin `Region_Sur` | 0.6029 | 45 429.8 | 45 501.2 |
| (b) antigüedad con dummies | 0.6044 | 45 354.7 | 45 442.0 |

- **(a)** Quitar `Region_Sur` empeora los tres criterios, a pesar de su VIF alto: la dummy recoge un cambio en la latitud 36 que la tendencia lineal de la latitud no capta. Un VIF alto no es, por sí solo, motivo para quitar una variable.
- **(b)** Las dummies de antigüedad ajustan igual (mismo $R^2$ ajustado y AIC) y usan un parámetro más, así que el BIC prefiere la codificación ordinal, más sencilla. Es coherente con los saltos parecidos del paso 6.

</details>

## Lo que deberías llevarte

- Cada coeficiente de la regresión múltiple se interpreta **con las demás variables constantes**, y puede ser muy distinto del de la regresión simple: las habitaciones pasan de $+38$ mil € a no tener efecto cuando la superficie está en el modelo.
- Las comparaciones brutas entre grupos mezclan efectos; el modelo múltiple los separa, siempre que las variables relevantes estén dentro.
- Una variable redundante (VIF alto) infla los errores estándar de las variables con las que está relacionada; si además no mejora el AIC, el BIC ni la predicción con datos nuevos, sobra.
- Para elegir modelo se miran juntos el $R^2$ ajustado, el AIC, el BIC, el contraste F de modelos anidados y la evaluación con datos nuevos, además de lo que tiene sentido en el problema.
- El diagnóstico de residuos dice hasta dónde fiarse del `summary`: con valores recortados o errores dependientes, los p-valores son demasiado optimistas.

[← Diagnóstico del modelo](diagnostico.md) [Volver al tema ↑](index.md)
