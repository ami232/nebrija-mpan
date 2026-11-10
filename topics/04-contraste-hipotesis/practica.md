# 4.5 Práctica guiada: contrastes en escenarios de negocio

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/04-contraste-hipotesis/practica/>

Esta práctica aplica los contrastes del tema a cuatro situaciones de negocio, cada una con un contraste distinto: una media frente a un valor fijo, dos proporciones, dos medias independientes y dos medias pareadas. En todas el razonamiento es el mismo, el de los [cinco pasos](logica.md#el-procedimiento-en-cinco-pasos); lo que hay que aprender es a elegir el contraste adecuado y a traducir el resultado a una decisión.

**Cómo trabajar.** Abre el notebook y resuelve cada apartado **antes** de desplegar el resultado. Leer las soluciones sin intentarlo da una falsa sensación de haber entendido.

**Notebook de la práctica** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/04-contraste-hipotesis/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

> 💡 **Antes de empezar**
>
> - En Colab, haz *Archivo → Guardar una copia en Drive* para no perder tus cambios.
> - Ten a mano la tabla de [qué contraste usar](dos-muestras.md#qué-contraste-usar) y las funciones `cdf`, `sf` y `ppf` de `stats.norm` y `stats.t`.
> - Los datos de los ejercicios 3 y 4 se simulan con una semilla fija, así que tus números deben coincidir con los de esta página.

## Ejercicio 1. Tiempo de entrega de un e-commerce

Una empresa afirma que el tiempo medio de entrega es de **3 días**. El departamento de calidad sospecha que es mayor. En 40 entregas recientes, el tiempo medio fue de 3.4 días, con una desviación típica muestral de 0.8 días.

1. Formula las hipótesis. ¿Bilateral o unilateral?
2. ¿Test Z o test t? ¿Por qué?
3. Calcula el estadístico, el valor crítico y el p-valor con $\alpha = 0.05$, y decide.
4. Repite el cálculo con la normal y compara.

<details>
<summary>💡 <b>Pistas</b></summary>

- La sospecha de calidad tiene una dirección. Repasa [contrastes bilaterales y unilaterales](logica.md#contrastes-bilaterales-y-unilaterales).
- ¿Conocemos la desviación típica de la **población** o solo la de la muestra? Repasa [la t de Student](una-muestra.md#la-t-de-student).
- Para un contraste unilateral por la derecha, el valor crítico es `stats.t.ppf(1 - alpha, gl)` y el p-valor, `stats.t.sf(t, gl)`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

1. $H_0: \mu = 3$ frente a $H_1: \mu > 3$. **Unilateral por la derecha**, porque calidad sospecha que el tiempo es **mayor**.
2. **Test t**: solo conocemos la desviación típica de la muestra. Con $n = 40$ la diferencia con el test Z será pequeña, pero la t es la distribución correcta.
3. $\text{SE} = 0.8/\sqrt{40} = 0.126$ y $t = 0.4/0.126 = 3.16$, con 39 grados de libertad. El valor crítico es 1.685 y el p-valor, 0.0015. Como $3.16 > 1.685$ y $0.0015 < 0.05$, **rechazamos** $H_0$.
4. Con la normal, el valor crítico es 1.645 y el p-valor, 0.0008. La decisión es la misma; la t es algo más exigente.

**Interpretación.** Hay evidencia clara de que el tiempo medio de entrega supera los 3 días. El intervalo de confianza al 95 % es $[3.14,\ 3.66]$ días: la empresa debería investigar la causa del retraso o anunciar un plazo más realista.

</details>

## Ejercicio 2. Comparación de dos campañas de marketing

Una empresa ha probado dos campañas con 1000 clientes cada una. La campaña A consigue 85 conversiones (8.5 %) y la B, 105 (10.5 %). ¿Es diferente la tasa de conversión de las dos campañas?

1. Representa las dos tasas en un gráfico de barras.
2. Formula las hipótesis.
3. Haz el contraste Z para dos proporciones con $\alpha = 0.05$: proporción conjunta, error estándar, estadístico, valor crítico y p-valor.
4. Calcula el intervalo de confianza al 95 % de la diferencia.
5. Si la campaña B cuesta un 20 % más que la A, ¿recomendarías cambiar?

<details>
<summary>💡 <b>Pistas</b></summary>

- La pregunta es si las tasas son **diferentes**, sin dirección.
- Repasa el [contraste para dos proporciones](dos-muestras.md#dos-proporciones). La proporción conjunta es el total de conversiones entre el total de clientes.
- Antes de decidir, compara el p-valor con $\alpha$ con cuidado: una diferencia de 2 puntos puede parecer grande.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

1. El gráfico muestra un 8.5 % frente a un 10.5 %. A simple vista B parece mejor, pero el gráfico no dice si la diferencia es mayor de lo que produciría el azar: para eso está el contraste.
2. $H_0: p_A = p_B$ frente a $H_1: p_A \ne p_B$. Bilateral.
3. Proporción conjunta: $190/2000 = 0.095$. Error estándar: $\sqrt{0.095 \cdot 0.905 \cdot (1/1000 + 1/1000)} = 0.0131$. Estadístico: $z = 0.02/0.0131 = 1.53$. Valor crítico: $\pm 1.96$. p-valor: 0.127. Como $1.53 < 1.96$ y $0.127 > 0.05$, **no rechazamos** $H_0$.
4. La diferencia es de 2 puntos, con un intervalo al 95 % de $[-0.6,\ 4.6]$ puntos. Incluye el 0: los datos son compatibles con que las dos campañas conviertan igual, y también con que B sea bastante mejor.
5. Con estos datos **no** hay evidencia suficiente para pagar un 20 % más por la campaña B. Una diferencia de 2 puntos con 1000 clientes por grupo cabe dentro del azar. Si a la empresa le importa detectar una mejora de ese tamaño, el siguiente paso es repetir la prueba con un tamaño de muestra calculado como en [planificar el tamaño de muestra](errores-potencia.md#planificar-el-tamaño-de-muestra): con `n_dos_proporciones(0.085, 0.105)` salen unos 3 400 clientes por campaña.

</details>

## Ejercicio 3. Test A/B en un sitio web

Una empresa compara el tiempo que los usuarios pasan en la página de producto con el diseño actual (A) y con uno nuevo (B), con 50 usuarios en cada versión. Quiere saber si el nuevo diseño **aumenta** el tiempo en la página.

1. Compara las dos distribuciones con un boxplot y un histograma.
2. Formula las hipótesis.
3. Haz el contraste t para dos muestras independientes a mano: medias, desviaciones típicas, desviación típica conjunta, estadístico, grados de libertad, valor crítico y p-valor.
4. Compara con `stats.ttest_ind`.
5. ¿Recomendarías el nuevo diseño? ¿Qué más mirarías?

<details>
<summary>💡 <b>Pistas</b></summary>

- Son usuarios distintos en cada versión: muestras **independientes**. Repasa [dos medias con muestras independientes](dos-muestras.md#dos-medias-con-muestras-independientes).
- Usa `ddof=1` para las desviaciones típicas.
- `stats.ttest_ind(b, a)` da por defecto el p-valor **bilateral**. Para el unilateral, usa `alternative="greater"` o, si el estadístico es positivo, divide el bilateral entre 2.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

1. Las dos distribuciones tienen una dispersión parecida y se solapan mucho, aunque la de B está algo desplazada hacia arriba. Con desviaciones típicas tan parecidas, la versión con desviación típica conjunta es adecuada.
2. $H_0: \mu_B = \mu_A$ frente a $H_1: \mu_B > \mu_A$. Unilateral por la derecha.
3. Versión A: media 120.4 s, desviación típica 36.1 s. Versión B: media 136.4 s, desviación típica 37.6 s. Desviación típica conjunta: 36.8 s. Estadístico: $t = 2.18$ con 98 grados de libertad. Valor crítico: 1.661. p-valor unilateral: 0.016. **Rechazamos** $H_0$.
4. `stats.ttest_ind` da el mismo $t = 2.18$ y un p-valor bilateral de 0.032, cuya mitad es 0.016.
5. El nuevo diseño aumenta el tiempo en la página unos 16 segundos de media. Pero más tiempo no siempre es mejor: puede significar que los usuarios encuentran el producto más interesante o que les cuesta más encontrar lo que buscan. Antes de decidir habría que mirar la conversión, el importe de los pedidos y la tasa de rebote.

</details>

## Ejercicio 4. Satisfacción de clientes antes y después (test pareado)

Un hotel encuesta a los mismos 20 clientes antes y después de un programa de mejora del servicio, con una escala de satisfacción de 1 a 10.

1. Representa el cambio de cada cliente con un gráfico de líneas.
2. ¿Por qué hay que usar un test pareado y no uno para dos muestras independientes?
3. Formula las hipótesis.
4. Haz el test t pareado a mano y compara con `stats.ttest_rel`.
5. Interpreta el resultado y señala las limitaciones del estudio.

<details>
<summary>💡 <b>Pistas</b></summary>

- Repasa [muestras pareadas](dos-muestras.md#muestras-pareadas). El contraste es un test t de una media sobre la columna `Diferencia`.
- Para ver qué se pierde al no emparejar, prueba también `stats.ttest_ind(despues, antes)`.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

1. Todas las líneas suben o se quedan planas: ningún cliente empeora.
2. Son los **mismos** clientes medidos dos veces. Cada cliente tiene su propio nivel de exigencia, y lo que interesa es su cambio. El test pareado trabaja con la diferencia de cada cliente y elimina la variación entre clientes.
3. $H_0: \mu_d = 0$ frente a $H_1: \mu_d > 0$, con $d = $ después $-$ antes. Unilateral por la derecha.
4. Satisfacción media antes: 6.5; después: 7.3. Media de las diferencias: $\bar{d} = 0.80$, desviación típica de las diferencias: $s_d = 0.616$. Estadístico: $t = 0.80/(0.616/\sqrt{20}) = 5.81$ con 19 grados de libertad. Valor crítico: 1.729. p-valor unilateral: 0.000007. **Rechazamos** $H_0$. `stats.ttest_rel(despues, antes)` da el mismo estadístico.

    Si se tratan los datos como independientes, el estadístico baja a 1.80 y el p-valor bilateral sube a 0.08: con el mismo cambio medio, el contraste no lo detectaría con $\alpha = 0.05$ bilateral.
5. La satisfacción subió 0.8 puntos de media: 14 clientes mejoraron y 6 se quedaron igual, y ninguno empeoró. Las limitaciones: no hay grupo de control (la satisfacción podría haber subido de todas formas, por la temporada o por saber que se les encuesta), 20 clientes son pocos y quizá no representativos, y solo se mide justo después del programa.

</details>

## Entrega 2: lanzamiento de producto

Ahora te toca a ti. Una empresa va a lanzar un producto y ha hecho un estudio de mercado con 200 personas: si tienen intención de comprarlo, cuánto pagarían y su segmento de edad. Con contrastes de hipótesis, tendrás que decidir si el producto es viable por demanda y por precio, y en qué segmento conviene centrar el marketing.

**Entrega 2: lanzamiento de producto** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/04-contraste-hipotesis/assignment.ipynb) &nbsp; [descargar .ipynb](assignment.ipynb)

Para cada pregunta, sigue los cinco pasos: hipótesis (¿bilateral o unilateral?), nivel de significación, contraste adecuado y por qué, p-valor y decisión traducida al negocio. Acompaña cada decisión con la estimación y su intervalo de confianza, y termina con una recomendación que entendería alguien que no sabe estadística.

La entrega se sube en **Blackboard**: descarga tu notebook desde Colab (*Archivo → Descargar → Descargar .ipynb*), con todas las celdas ejecutadas, y súbelo a la tarea "Entrega 2".

[← Errores y potencia](errores-potencia.md) [Volver al tema ↑](index.md)
