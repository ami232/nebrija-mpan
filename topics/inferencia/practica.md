# 4. Práctica guiada: aceptación de cookies

> Versión web, con demos interactivas: <https://alberpro.com/nebrija-mpan/topics/inferencia/practica/>

Esta práctica recorre un análisis completo, de principio a fin, con las herramientas de los tres primeros temas del curso. El objetivo no es solo obtener números, sino aprender **cómo se razona** ante un problema real: qué preguntas hacerse, qué suponer, cómo comprobarlo y cómo comunicar el resultado.

**El caso.** El equipo de marketing de una web necesita saber qué proporción de usuarios acepta las cookies, porque de ello depende cuántos datos tendrá para sus campañas personalizadas. El departamento de IT asegura que la tasa es del 80 %. Hemos registrado durante tres días lo que hicieron 1000 usuarios: si aceptaron o rechazaron el banner. ¿Qué tasa de aceptación podemos dar a marketing? ¿Es creíble la cifra de IT?

**Cómo trabajar.** Abre el notebook y resuelve cada paso **antes** de desplegar el resultado. Leer las soluciones sin intentarlo da una falsa sensación de haber entendido.

**Notebook de la práctica** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/inferencia/exercises.ipynb) &nbsp; [descargar .ipynb](exercises.ipynb)

> 💡 **Antes de empezar**
>
> - En Colab, haz *Archivo → Guardar una copia en Drive* para no perder tus cambios.
> - Si nunca has usado pandas o SciPy, haz antes el [tutorial de Python](../probabilidad/index.md#antes-de-empezar).
> - Ten a mano la tabla de funciones de `scipy.stats` de la página de [distribuciones](../variables-aleatorias/distribuciones.md#en-python-scipystats).

## Paso 1. Conocer los datos

Antes de modelar nada, mira los datos. Es el paso que más se salta y el que más errores evita. Comprueba cuántas filas hay, qué significa cada columna, qué valores toma y si hay algo raro (valores ausentes, duplicados, códigos inesperados).

<details>
<summary>✅ <b>Resultado</b></summary>

Hay 1000 filas y tres columnas: `user_id`, `day` (`day_1`, `day_2` o `day_3`) y `accepted_cookies` (1 si acepta, 0 si rechaza). No hay valores ausentes. `data.accepted_cookies.value_counts()` muestra **589 aceptaciones** y 411 rechazos.

</details>

## Paso 2. Plantear el modelo y sus suposiciones

Queremos un modelo que nos permita **generalizar** de estos 1000 usuarios a los usuarios futuros. Cada usuario tiene un resultado de sí o no, así que lo natural es modelarlo como una variable de Bernoulli. Antes de seguir, escribe explícitamente qué estamos suponiendo. Esas suposiciones son las que justifican todos los cálculos posteriores, y si fallan, fallan los cálculos.

<details>
<summary>✅ <b>El modelo</b></summary>

- Cada usuario $i$ es una variable $X_i \sim \text{Bernoulli}(p)$: $X_i = 1$ si acepta, con probabilidad $p$.
- **Independencia:** la decisión de un usuario no influye en la de otro.
- **Idéntica distribución:** todos los usuarios, todos los días, tienen **la misma** probabilidad $p$.

La segunda suposición parece razonable: los usuarios no se ven entre sí. La tercera es más delicada, porque podría haber diferencias entre días, dispositivos o tipos de usuario. La revisaremos en el paso 6.

</details>

## Paso 3. Estimar la tasa de aceptación

El parámetro que interesa a marketing es $p$. ¿Cómo lo estimamos? Según la [tabla de estimadores de máxima verosimilitud](estimacion.md#estimar-los-parámetros-máxima-verosimilitud), en una Bernoulli el estimador de $p$ es la media muestral. Para datos de ceros y unos, eso es simplemente la proporción de unos.

<details>
<summary>✅ <b>Resultado</b></summary>

$\hat{p} = 589/1000 = 0.589$, es decir, `data.accepted_cookies.mean()`. La tasa de rechazo es $\hat{q} = 1 - \hat{p} = 0.411$.

Ojo: 0.589 es una **estimación**, no el valor real de $p$. Con otros 1000 usuarios habríamos obtenido otro número. Cuantificar esa incertidumbre es el objetivo del paso 5.

</details>

## Paso 4. Usar el modelo para hacer previsiones

Un modelo sirve para responder preguntas que los datos por sí solos no contestan. Supón que mañana entran 100 usuarios. El número que acepta es la suma de 100 Bernoulli independientes, es decir, $Y \sim \text{Binomial}(100, \hat{p})$. Marketing pregunta:

1. ¿Qué probabilidad hay de que acepten exactamente 80?
2. Para su campaña necesitan que acepten más de 50. ¿Qué probabilidad hay de que **no** lo consigan ($Y \le 50$)? ¿Y de que sí?
3. Calcula lo mismo con la aproximación normal, con y sin corrección de continuidad. ¿Cuál se acerca más al valor exacto?

<details>
<summary>💡 <b>Pistas</b></summary>

- $P(Y = k)$ es `stats.binom.pmf(k, n, p)` y $P(Y \le k)$ es `stats.binom.cdf(k, n, p)`.
- La normal que aproxima a la binomial tiene $\mu = n\hat{p}$ y $\sigma = \sqrt{n\hat{p}\hat{q}}$. Repasa la [aproximación normal de la binomial](variabilidad.md#el-tlc-explica-la-forma-de-la-binomial).

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

1. $P(Y = 80) \approx 4.1 \cdot 10^{-6}$. Con $p \approx 0.59$, que acepten 80 de 100 es prácticamente imposible. Fíjate en este número: será relevante en el paso 5.
2. $P(Y \le 50) \approx 0.045$, así que $P(Y > 50) \approx 0.955$. Hay un 4.5 % de riesgo de no llegar al objetivo. Puedes comprobarlo sumando las probabilidades individuales: `stats.binom.pmf(np.arange(51), 100, p_hat).sum()`.
3. Con $\mu = 58.9$ y $\sigma \approx 4.92$, la normal sin corrección da `stats.norm.cdf(50, 58.9, 4.92)` $\approx 0.035$ y con corrección, `stats.norm.cdf(50.5, 58.9, 4.92)` $\approx 0.044$. La corrección de continuidad casi clava el valor exacto. Sin ella, el riesgo se subestima en más de un 20 %.

</details>

## Paso 5. ¿Es creíble la cifra de IT?

IT dice que $p = 0.8$, y nosotros hemos estimado 0.589. ¿Puede deberse la diferencia al azar del muestreo, o los datos contradicen a IT? Para responder hay que saber **cuánto puede variar** $\hat{p}$ de una muestra a otra, y esa es justo la pregunta que contesta un [intervalo de confianza](intervalos.md#intervalos-de-confianza).

1. Calcula el error estándar de $\hat{p}$ y un intervalo de confianza al 95 %. Repite al 99.99 %.
2. ¿Está 0.8 dentro de los intervalos? ¿Qué concluyes?
3. Imagina que solo hubiéramos observado 10 usuarios con la misma proporción. ¿Cambiaría la conclusión?

<details>
<summary>✅ <b>Resultado</b></summary>

1. El error estándar es $\sqrt{0.589 \cdot 0.411/1000} \approx 0.0156$. El intervalo al 95 % es $0.589 \pm 1.96 \cdot 0.0156 = [0.559,\ 0.619]$. Al 99.99 %, con `stats.norm.ppf` en 0.00005 y 0.99995, sale $[0.528,\ 0.650]$.
2. El 0.8 queda **muy lejos** incluso del intervalo al 99.99 %. Si la tasa real fuera del 80 %, sería extraordinariamente improbable observar 589 aceptaciones de 1000. Los datos contradicen la cifra de IT. Quizá midieron en otro periodo, con otro diseño del banner o con una definición distinta de "aceptar".
3. Con $n = 10$, el error estándar sería $\sqrt{0.589 \cdot 0.411/10} \approx 0.156$, diez veces mayor. El intervalo al 95 % iría de $0.284$ a $0.894$ y el del 99 % de $0.188$ a $0.990$, así que **no podríamos descartar** el 80 %. La misma proporción observada lleva a conclusiones opuestas según la cantidad de datos. Por eso nunca hay que dar una estimación sin su margen de error.

</details>

## Paso 6. Revisar las suposiciones

Todos los cálculos anteriores suponen que $p$ es **la misma** para todos los usuarios. Tenemos una forma sencilla de comprobarlo: los datos vienen de tres días distintos. Si la suposición es correcta, la tasa de cada día debería ser parecida, salvo por el ruido del muestreo. Calcula la proporción de aceptación de cada día y su intervalo de confianza.

<details>
<summary>💡 <b>Pista</b></summary>

`data.groupby("day").accepted_cookies.agg(["size", "mean"])` da el tamaño y la proporción de cada día. Para el intervalo, usa la misma fórmula del paso 5 con el $n$ de cada día.

</details>

<details>
<summary>✅ <b>Resultado</b></summary>

| Día | Usuarios | Aceptan | $\hat{p}$ | IC 95 % |
|---|---|---|---|---|
| day_1 | 346 | 159 | 0.460 | [0.407, 0.512] |
| day_2 | 339 | 196 | 0.578 | [0.526, 0.631] |
| day_3 | 315 | 234 | 0.743 | [0.695, 0.791] |

Los intervalos **ni siquiera se solapan**. La tasa de aceptación sube claramente de un día a otro, de un 46 % a un 74 %, mucho más de lo que explicaría el azar. La suposición de una **$p$ común** no se sostiene, y el 0.589 global es un promedio de tres situaciones distintas que no describe bien ninguna.

</details>

## Conclusiones: qué le diríamos a marketing

Un buen análisis termina con una respuesta clara a la pregunta de negocio, que diga también lo que **no** sabemos:

> En los tres días observados aceptó las cookies el 59 % de los usuarios (IC 95 %: 56–62 %). Esta cifra es claramente incompatible con el 80 % que maneja IT. Sin embargo, la tasa no es estable: pasó del 46 % el primer día al 74 % el tercero. Antes de usar un único número para planificar campañas, conviene averiguar qué cambió entre esos días (diseño del banner, origen del tráfico, dispositivo…) y seguir midiendo. Si la tendencia se mantiene, la tasa actual podría estar más cerca del 74 % que del 59 %.

La lección más importante de la práctica no es ninguna fórmula: **los cálculos solo valen lo que valen sus suposiciones**. Comprobarlas con los datos que tengas a mano (por grupos, por periodos) es parte del análisis, no un extra.

## Para seguir pensando

**Si quisieras estimar p con un margen de error de ±1 punto porcentual (al 95 %), ¿cuántos usuarios necesitarías?**

<details>
<summary>✅ <b>Solución</b></summary>

El margen de error es $1.96\sqrt{p(1-p)/n} = 0.01$, así que $n = p(1-p)\,(1.96/0.01)^2$. Con $p \approx 0.589$ sale $n \approx 9300$ usuarios. Si no tuviéramos ninguna estimación previa de $p$, usaríamos el caso más desfavorable, $p = 0.5$, que da $n \approx 9600$.

</details>

**¿Se te ocurre algún motivo por el que los usuarios podrían NO ser independientes entre sí?**

<details>
<summary>✅ <b>Posibles respuestas</b></summary>

- Usuarios que comparten dispositivo (una familia), donde el navegador recuerda la decisión anterior.
- Una misma persona contada varias veces como usuarios distintos, por borrar cookies o usar varios dispositivos.
- Picos de tráfico desde una misma fuente, por ejemplo un enlace en una red social, con usuarios de perfil parecido que llegan juntos.

Ninguno invalida el análisis por completo, pero la dependencia suele hacer que los intervalos reales sean más anchos que los calculados.

</details>

## Entrega 1: conexiones por hora

Ahora te toca a ti hacer un análisis completo. Durante un día se registró, minuto a minuto, cuántos usuarios se conectaban a la web y cuántos de ellos aceptaban las cookies. Hay dos preguntas de negocio:

- **Marketing** quiere saber si los usuarios aceptan las cookies más a unas horas que a otras.
- **Infraestructura** necesita saber con qué frecuencia se superarán los 40 usuarios por minuto, porque a partir de ahí hay que contratar un servidor más potente.

**Entrega 1: conexiones por hora y aceptación de cookies** &nbsp; [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ami232/nebrija-mpan/blob/main/topics/inferencia/assignment.ipynb) &nbsp; [descargar .ipynb](assignment.ipynb)

**Cómo abordarla.** Sigue la misma estructura que esta práctica:

1. **Explora** los datos, con gráficos de las conexiones y la tasa de aceptación a lo largo del día.
2. **Propón un modelo** para cada variable y justifícalo. ¿Qué distribución describe un *número de eventos por unidad de tiempo*? ¿Y el número de aceptaciones sabiendo cuántos usuarios se conectaron?
3. **Enuncia tus suposiciones** y **compruébalas** con los datos. ¿Es el ritmo de conexiones igual a todas horas?
4. **Responde a cada departamento** con números, su incertidumbre y un párrafo de conclusiones que entendería alguien que no sabe estadística.

La entrega se sube en **Blackboard**: descarga tu notebook desde Colab (*Archivo → Descargar → Descargar .ipynb*) y súbelo a la tarea correspondiente.

[← Intervalos de confianza](intervalos.md) [Volver al tema ↑](index.md)
