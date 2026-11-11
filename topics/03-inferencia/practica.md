# 3.4 Práctica guiada: las ventas de la campaña de email

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/03-inferencia/practica/>

Esta práctica recorre un análisis completo, de principio a fin, con las herramientas de los tres primeros temas del curso. El objetivo no es solo obtener números, sino aprender **cómo se razona** ante un problema real: qué preguntas hacerse, qué suponer, cómo comprobarlo y cómo comunicar el resultado.

**El caso.** Es la campaña del [cuaderno de la sesión](estimacion.md), ahora con datos reales en lugar de simulados. Una tienda online envía cada día un email promocional a **500 clientes**. Cada venta deja un margen de **15 €** y la campaña cuesta **450 € al día**, así que hacen falta al menos 30 ventas para cubrir costes. El almacén puede preparar **como máximo 45 pedidos al día**. Tenemos las ventas de las 8 primeras semanas de campaña, del lunes 7 de septiembre al domingo 1 de noviembre de 2026. La agencia que diseñó el email aseguró que compraría el **8 %** de los clientes. Marketing pregunta qué tasa de compra podemos dar, si es creíble la cifra de la agencia y con qué frecuencia perderemos dinero o saturaremos el almacén.

**Cómo trabajar.** Abre el notebook y resuelve cada paso **antes** de desplegar el resultado. Leer las soluciones sin intentarlo da una falsa sensación de haber entendido.

**Notebook de la práctica** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/03-inferencia/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

> 💡 **Antes de empezar**
>
> - En Colab, haz *Archivo → Guardar una copia en Drive* para no perder tus cambios.
> - Si nunca has usado pandas o SciPy, haz antes el [tutorial de Python](../01-probabilidad/index.md#antes-de-empezar).
> - Ten a mano la tabla de funciones de `scipy.stats` de la página de [distribuciones](../02-variables-aleatorias/distribuciones.md#en-python-scipystats).

## Paso 1. Conocer los datos

Antes de modelar nada, mira los datos. Es el paso que más se salta y el que más errores evita. Comprueba cuántas filas hay, qué significa cada columna, qué valores toma y si hay algo raro (valores ausentes, días que faltan, cifras imposibles).

<details>
<summary>✅ <b>Resultado</b></summary>

Hay 56 filas, una por día, y cuatro columnas: `fecha`, `dia_semana` (de `lunes` a `domingo`), `emails` (siempre 500) y `ventas`. No hay valores ausentes ni días repetidos: son 8 semanas completas, 8 días de cada día de la semana. Las ventas diarias van de 25 a 58, con una media de 35.1 y una mediana de 34. En total hubo **1968 ventas** de 28 000 emails.

</details>

## Paso 2. Plantear el modelo y sus suposiciones

Queremos un modelo que nos permita **generalizar** de estos 56 días a los días futuros. Cada cliente que recibe el email compra o no compra, y las ventas de un día cuentan cuántos de los 500 compraron. Antes de seguir, escribe el modelo y, sobre todo, qué estamos suponiendo. Esas suposiciones son las que justifican todos los cálculos posteriores, y si fallan, fallan los cálculos.

<details>
<summary>✅ <b>El modelo</b></summary>

- Cada cliente $i$ es una variable $Y_i \sim \text{Bernoulli}(p)$: $Y_i = 1$ si compra, con probabilidad $p$.
- Las ventas de un día son la suma de 500 de ellas: $X \sim \text{Binomial}(500, p)$.
- **Independencia:** la decisión de un cliente no influye en la de otro.
- **Idéntica distribución:** todos los clientes, **todos los días**, tienen la misma probabilidad de compra $p$.

La independencia parece razonable: los clientes reciben el email cada uno en su buzón. La idéntica distribución es más delicada, porque un email no se lee igual un lunes que un sábado. La revisaremos en el paso 6.

</details>

## Paso 3. Estimar la probabilidad de compra

El parámetro que interesa a marketing es $p$. Según la [tabla de estimadores de máxima verosimilitud](estimacion.md#estimar-los-parámetros-máxima-verosimilitud), en una binomial con $n$ conocido el estimador de $p$ es el total de éxitos entre el total de intentos.

<details>
<summary>✅ <b>Resultado</b></summary>

$\hat{p} = 1968/28\,000 = 0.0703$, es decir, `data.ventas.sum() / data.emails.sum()`. Es lo mismo que la media de ventas diarias entre 500: $35.14/500 = 0.0703$.

Ojo: 0.0703 es una **estimación**, no el valor real de $p$. Con otras 8 semanas habríamos obtenido otro número. Cuantificar esa incertidumbre es el objetivo del paso 5.

</details>

## Paso 4. Usar el modelo para hacer previsiones

Un modelo sirve para responder preguntas que los datos por sí solos no contestan bien. Con $X \sim \text{Binomial}(500, \hat{p})$, calcula:

1. Las ventas esperadas en un día y su desviación típica.
2. La probabilidad de que un día haya exactamente 35 ventas.
3. La probabilidad de **perder dinero** (menos de 30 ventas), la de **saturar el almacén** (más de 45) y la de un **día normal** (entre 30 y 45, ambos incluidos). ¿Cuántos días con pérdidas esperas en un mes de 30 días?
4. La probabilidad de perder dinero con la aproximación normal, con y sin corrección de continuidad. ¿Cuál se acerca más al valor exacto?

<details>
<summary>💡 <b>Pistas</b></summary>

- $P(X = k)$ es `stats.binom.pmf(k, n, p)` y $P(X \le k)$ es `stats.binom.cdf(k, n, p)`. "Menos de 30" es "29 o menos".
- La normal que aproxima a la binomial tiene $\mu = n\hat{p}$ y $\sigma = \sqrt{n\hat{p}(1 - \hat{p})}$. Repasa la [aproximación normal de la binomial](variabilidad.md#el-tlc-explica-la-forma-de-la-binomial).

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

1. $\mathbb{E}[X] = 500 \cdot 0.0703 = 35.1$ ventas, con desviación típica $\sqrt{500 \cdot 0.0703 \cdot 0.9297} \approx 5.72$.
2. $P(X = 35) \approx 0.070$. Aunque 35 es el valor más probable, solo ocurre un 7 % de los días.
3. $P(X \le 29) \approx 0.162$, $P(X > 45) \approx 0.039$ y $P(30 \le X \le 45) \approx 0.799$. Las tres suman 1. En un mes esperaríamos unos $30 \cdot 0.162 \approx 4.9$ días con pérdidas. El beneficio medio sería $15 \cdot 35.14 - 450 \approx 77$ € al día.
4. Con $\mu = 35.14$ y $\sigma = 5.72$, la normal sin corrección da `stats.norm.cdf(29, 35.14, 5.72)` $\approx 0.141$, y con corrección, `stats.norm.cdf(29.5, 35.14, 5.72)` $\approx 0.162$. La corrección de continuidad clava el valor exacto. Sin ella, el riesgo se subestima en un 13 %.

</details>

## Paso 5. ¿Es creíble la cifra de la agencia?

La agencia dijo que compraría el 8 % de los clientes, y nosotros hemos estimado un 7.03 %. ¿Puede deberse la diferencia al azar del muestreo, o los datos contradicen a la agencia? Para responder hay que saber **cuánto puede variar** $\hat{p}$ de una muestra a otra, y esa es justo la pregunta que contesta un [intervalo de confianza](intervalos.md#intervalos-de-confianza).

1. Calcula el error estándar de $\hat{p}$ y un intervalo de confianza al 95 %. Repite al 99.99 %.
2. ¿Está 0.08 dentro de los intervalos? ¿Qué concluyes?
3. Imagina que solo tuviéramos la primera semana (3500 emails, 253 ventas). ¿Cambiaría la conclusión?

<details>
<summary>✅ <b>Resultado</b></summary>

1. Hay $N = 28\,000$ clientes observados, así que el error estándar es $\sqrt{0.0703 \cdot 0.9297 / 28\,000} \approx 0.0015$. El intervalo al 95 % es $0.0703 \pm 1.96 \cdot 0.0015 = [0.0673,\ 0.0733]$. Al 99.99 %, con `stats.norm.ppf(0.99995)` $\approx 3.89$ en lugar de 1.96, sale $[0.0643,\ 0.0762]$.
2. El 0.08 queda **fuera** incluso del intervalo al 99.99 %. Si la tasa real fuera del 8 %, sería extraordinariamente improbable observar solo 1968 ventas de 28 000 emails. Los datos contradicen la cifra de la agencia: quizá la midió con otra lista de clientes, en otra época del año o con otra definición de "compra".
3. Con la primera semana, $\hat{p} = 253/3500 = 0.0723$ y el error estándar es $\sqrt{0.0723 \cdot 0.9277/3500} \approx 0.0044$, casi tres veces mayor. El intervalo al 95 % va de $0.0637$ a $0.0809$, así que **no podríamos descartar** el 8 %. Con una semana de datos la cifra de la agencia era creíble; con ocho, ya no. Por eso nunca hay que dar una estimación sin su margen de error. En el [tema 4](../04-contraste-hipotesis/index.md) haremos esta comparación con un contraste de hipótesis.

</details>

## Paso 6. Revisar las suposiciones

Todos los cálculos anteriores suponen que $p$ es **la misma** todos los días. Hay dos formas sencillas de comprobarlo con los datos que tenemos.

1. **Comparar el modelo con lo observado.** ¿En qué proporción de los 56 días hubo pérdidas, día normal y saturación? Compáralo con las probabilidades del paso 4. Compara también la varianza de las ventas diarias con la varianza del modelo, $500\,\hat{p}(1 - \hat{p})$.
2. **Mirar por grupos.** Los datos tienen el día de la semana. Si la suposición es correcta, la tasa de compra de cada día de la semana debería ser parecida, salvo por el ruido del muestreo. Calcula la proporción de compra de cada día de la semana y su intervalo de confianza al 95 %.

<details>
<summary>💡 <b>Pistas</b></summary>

- `data.ventas.between(30, 45).mean()` da la proporción de días entre 30 y 45 ventas.
- `data.groupby("dia_semana", sort=False)[["emails", "ventas"]].sum()` da los totales de cada día de la semana. Para el intervalo, usa la fórmula del paso 5 con los 4000 emails de cada día de la semana.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

**El modelo no reproduce los datos.**

| | Modelo Binomial(500, 0.0703) | Observado en 56 días |
|---|---|---|
| pérdidas (< 30) | 0.162 | 0.286 |
| día normal (30–45) | 0.799 | 0.589 |
| saturación (> 45) | 0.039 | 0.125 |
| varianza de las ventas diarias | 32.7 | 61.1 |

Hubo el doble de días con pérdidas y el triple de días de saturación de lo que predice el modelo, y la varianza observada casi dobla la del modelo. Con 56 días cabe algo de ruido, pero no tanto: los datos varían **más** de lo que permite una binomial con una $p$ común.

**La tasa depende del día de la semana.**

| Día | Ventas (8 días) | $\hat{p}$ | IC 95 % |
|---|---|---|---|
| lunes | 373 | 0.093 | [0.084, 0.102] |
| martes | 271 | 0.068 | [0.060, 0.076] |
| miércoles | 272 | 0.068 | [0.060, 0.076] |
| jueves | 260 | 0.065 | [0.057, 0.073] |
| viernes | 262 | 0.066 | [0.058, 0.073] |
| sábado | 257 | 0.064 | [0.057, 0.072] |
| domingo | 273 | 0.068 | [0.060, 0.076] |

El intervalo del lunes **no se solapa** con el de ningún otro día, mientras que los otros seis son casi idénticos. Los lunes compra en torno al 9.3 % de los clientes, y el resto de días en torno al 6.6 %. La suposición de una **$p$ común** no se sostiene, y el 7.03 % global es una mezcla de dos situaciones distintas que no describe bien ninguna de las dos.

Con dos modelos, uno para los lunes y otro para el resto, las previsiones cambian mucho. Un lunes se esperan 46.6 ventas y el almacén se satura con probabilidad 0.56 (en los datos, 5 de los 8 lunes). El resto de días se esperan 33.2 ventas y se pierde dinero con probabilidad 0.26 (en los datos, el 33 % de esos días). La varianza de las ventas de martes a domingo, 38.9, ya se parece mucho más a la de su binomial, 31.0.

</details>

## Conclusiones: qué le diríamos a marketing

Un buen análisis termina con una respuesta clara a la pregunta de negocio, que diga también lo que **no** sabemos:

> En las 8 semanas observadas compró el 7.0 % de los clientes que recibieron el email (IC 95 %: 6.7–7.3 %). Esta cifra es claramente incompatible con el 8 % que prometió la agencia. Además, la tasa no es la misma todos los días: los lunes compra en torno al 9.3 % y el resto de días en torno al 6.6 %. Eso tiene dos consecuencias prácticas. La mayoría de los lunes se superan los 45 pedidos que el almacén puede preparar, así que conviene reforzar la plantilla ese día. De martes a domingo, en cambio, uno de cada cuatro días la campaña no cubre su coste. Antes de decidir conviene averiguar por qué el lunes es distinto (¿se acumulan las compras del fin de semana?, ¿se abren más emails?) y seguir midiendo.

La lección más importante de la práctica no es ninguna fórmula: **los cálculos solo valen lo que valen sus suposiciones**. Comprobarlas con los datos que tengas a mano (por grupos, por periodos) es parte del análisis, no un extra.

## Para seguir pensando

**Si quisieras estimar p con un margen de error de ±0.5 puntos porcentuales (al 95 %), ¿cuántos emails necesitarías? ¿Cuántos días de campaña son?**

<details>
<summary>✅ <b>Solución</b></summary>

El margen de error es $1.96\sqrt{p(1-p)/N} = 0.005$, así que $N = p(1-p)\,(1.96/0.005)^2$. Con $p \approx 0.0703$ sale $N \approx 10\,000$ emails, es decir, unos 20 días de campaña. Si no tuviéramos ninguna estimación previa de $p$, usaríamos el caso más desfavorable, $p = 0.5$, que da $N \approx 38\,400$: casi cuatro veces más. Con proporciones pequeñas, usar $p = 0.5$ sobreestima mucho el tamaño necesario.

</details>

**El almacén quiere que solo se sature un día de cada 100. ¿Cuántos pedidos al día tendría que poder preparar según el modelo con una $p$ común? ¿Y según los modelos separados, para los lunes y para el resto de días?**

<details>
<summary>✅ <b>Solución</b></summary>

Hay que buscar el percentil 99 de las ventas diarias. Con la $p$ común, `stats.binom(500, 0.0703).ppf(0.99)` da **49 pedidos**. Con los modelos separados, para los lunes `stats.binom(500, 0.0932).ppf(0.99)` da **62** y para el resto de días `stats.binom(500, 0.0665).ppf(0.99)` da **47**. El modelo común reparte mal la capacidad: se queda corto los lunes y sobra un poco el resto de días.

</details>

**¿Se te ocurre algún motivo por el que los clientes podrían NO ser independientes entre sí, o un mismo cliente no ser independiente de un día a otro?**

<details>
<summary>✅ <b>Posibles respuestas</b></summary>

- Si la lista de 500 clientes se repite, un cliente que compró ayer probablemente no vuelva a comprar hoy.
- Clientes que se reenvían la oferta (compañeros de trabajo, familia) y compran juntos.
- Un problema técnico (la web caída unas horas, el email en la carpeta de spam de un proveedor de correo) que afecta a muchos clientes del mismo día a la vez.

Ninguno invalida el análisis por completo, pero la dependencia suele hacer que la variabilidad real sea mayor que la de la binomial y que los intervalos reales sean más anchos que los calculados.

</details>

## Entrega 1: la distribución de Poisson y las conexiones a una web

Ahora te toca a ti hacer un análisis completo, con una distribución nueva: la **Poisson**, que modela cuántas veces ocurre algo en un intervalo de tiempo. Durante una semana se registró, minuto a minuto, cuántos usuarios se conectaban a una web y cuántos de ellos aceptaban las cookies. Hay dos preguntas de negocio:

- **Infraestructura** necesita saber con qué frecuencia se superarán los 40 usuarios por minuto, porque a partir de ahí hay que contratar un servidor más potente.
- **Marketing** quiere saber si los usuarios aceptan las cookies más en unos momentos que en otros (a unas horas, o unos días de la semana).

**Entrega 1: la distribución de Poisson y las conexiones a una web** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/03-inferencia/assignment.ipynb) &nbsp; [descargar .ipynb](assignment.ipynb)

El notebook tiene dos partes:

1. **La distribución de Poisson.** Una explicación con ejemplos ejecutables: qué cuenta, su fórmula, cómo calcular probabilidades con `scipy.stats`, cómo estimar su parámetro $\lambda$ con un intervalo de confianza, cómo comprobar si encaja con unos datos y cómo hacer contrastes sobre $\lambda$.
2. **El ejercicio.** Explorar los datos, proponer un modelo y comprobar sus suposiciones, responder a cada departamento con números y su incertidumbre, y escribir unas conclusiones que entendería alguien que no sabe estadística.

Usa lo que has visto en [variables aleatorias y distribuciones](../02-variables-aleatorias/distribuciones.md), en este tema y en [contraste de hipótesis](../04-contraste-hipotesis/index.md).

La entrega se sube en **Blackboard**. El notebook tiene que ejecutarse de principio a fin sin errores, porque así es como se va a corregir. Antes de entregarlo, haz en Colab *Entorno de ejecución → Reiniciar sesión y ejecutar todo* y comprueba que ninguna celda da error, borra las salidas (*Editar → Borrar todas las salidas*), descárgalo (*Archivo → Descargar → Descargar .ipynb*) y súbelo a la tarea "Entrega 1".

[← Intervalos de confianza](intervalos.md) [Volver al tema ↑](index.md)
