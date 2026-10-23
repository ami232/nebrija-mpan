# Guía de referencia: modelos predictivos

> Versión web, con demos interactivas: <https://nebrija-mpan.alberpro.com/referencia/modelos-predictivos/>

Resumen de fórmulas y conceptos del curso. También se puede [descargar en PDF](guia-modelos-predictivos.pdf).


## 1. Conceptos Básicos

**Variable aleatoria**: función que asigna valores numéricos a sucesos  
**PDF**: función de densidad de probabilidad  
**CDF**: función de distribución acumulada $F(x) = P(X \leq x)$  
**ECDF**: función de distribución empírica

**Teorema Fundamental de la Estadística**: La distribución empírica converge a la distribución real cuando $n \to \infty$

## 2. Distribuciones

| Distribución | Media | Varianza | Notas |
|--------------|-------|----------|-------|
| $\text{Bernoulli}(p)$ | $p$ | $p(1-p)$ | Un ensayo |
| $\text{Binomial}(n,p)$ | $np$ | $np(1-p)$ | Suma de $n$ Bernoullis |
| $\text{Poisson}(\lambda)$ | $\lambda$ | $\lambda$ | Límite binomial: $n\to\infty, p\to 0, np\to\lambda$ |
| $\text{Normal}(\mu,\sigma)$ | $\mu$ | $\sigma^2$ | Estandarizar: $Z = \frac{X-\mu}{\sigma}$ |
| $t(\text{df})$ | $0$ | $>1$ | Colas pesadas; $\to N(0,1)$ cuando df $>30$ |

**Cuantil** $q_p$: valor tal que $P(X < q_p) = p$

## 3. Estadística Inferencial

**Estadísticos**: media $\bar{x}$, varianza $s^2$, desviación típica $s$, cuantiles, percentiles

**Estadísticos de la media muestral**: si $X_1, \ldots, X_n$ son i.i.d. con media $\mu$ y varianza $\sigma^2$, entonces $E[\bar{X}] = \mu,  \text{Var}(\bar{X}) = \frac{\sigma^2}{n}$

**Teorema del Límite Central** (TLC): Para $n$ grande, $\bar{X} \sim N(\mu, \frac{\sigma^2}{n})$ sin importar la distribución original

**Intervalo de confianza de $100(1-\alpha)$%**: $\bar{x} \pm z_{\alpha/2} \frac{s}{\sqrt{n}}$

$z_{\alpha/2}$ es el cuantil de la distribución normal estándar tal que $P(Z > z_{\alpha/2}) = \frac{\alpha}{2}$


## 4. Contraste de Hipótesis

- **Hipótesis nula** $H_0$: suposición a contrastar
- **Hipótesis alternativa** $H_1$: lo que se acepta si se rechaza $H_0$
- **Nivel de significación** $\alpha$: probabilidad de error tipo I (típico: 0.05)
- **Test unilateral**: $H_1: \mu > \mu_0$ o $\mu < \mu_0$
- **Test bilateral**: $H_1: \mu \neq \mu_0$

| Test | Uso | Estadístico |
|------|-----|-------------|
| z-test | Media con $\sigma$ conocida o TLC | $z = \frac{\bar{x}-\mu_0}{\sigma/\sqrt{n}}$ |
| t-test | Media con $\sigma$ desconocida | $t = \frac{\bar{x}-\mu_0}{s/\sqrt{n}}$ |
| t-test pareado | Diferencias entre observaciones emparejadas | $t = \frac{\bar{d}}{s_d/\sqrt{n}}$ |

- Debido a TLC, podemos aproximar la distribucion de la media muestral de Bernoulli(p) o p de Binomial(n,p) con Normal(p, $p(1-p)/n$) cuando $n$ grande (e.g. $n > 30$) y $p$ suficientemente próxima a 0.5 (e.g. tal que $np > 5$).

**Región de rechazo**: valores del estadístico que llevan a rechazar $H_0$

**p-valor**: probabilidad de obtener un estadístico igual o más extremo que el observado bajo $H_0$. Rechazar hipótesis nula si p-valor $< \alpha$, no rechazar en caso contrario.

## 5. Regresión Lineal

**Modelo**: $y = \beta_0 + \beta_1 X_1 + \cdots + \beta_n X_n + \epsilon$

**Covarianza**: $\text{Cov}(X,Y) = E[(X-\mu_X)(Y-\mu_Y)]$  
**Correlación**: $\rho = \frac{\text{Cov}(X,Y)}{\sigma_X \sigma_Y}$ (entre -1 y 1)

**Interpretación coeficientes**: $\beta_j$ = cambio en $y$ por unidad de $X_j$ (manteniendo resto constante)

**Coeficiente significativo**: p-valor $< \alpha$ (típicamente 0.05)

### Bondad de Ajuste

- **$R^2$**: proporción varianza explicada (0 a 1)
- **$R^2$ ajustado**: penaliza complejidad
- **AIC/BIC**: cuanto más pequeños, mejor

### Requisitos
- Linealidad
- Independencia de errores
- Homocedasticidad (varianza constante)
- Normalidad de residuos
- No multicolinealidad

**Multicolinealidad**: correlación alta entre predictores → coeficientes inestables. Evitar: eliminar variables correlacionadas o VIF $> 10$

**Diagnóstico residuos**: verificar media 0, varianza constante, independencia


## 6. Regresión Logística

**Función sigmoide**: $\sigma(z) = \frac{1}{1+e^{-z}}$ donde $z = \beta_0 + \beta_1 X_1 + \cdots$

**Predicción**: probabilidad entre 0 y 1; aplicar threshold (típico: 0.5)

### Matriz de Confusión

|  | Predicho Positivo | Predicho Negativo |
|--|-------------------|-------------------|
| **Real Positivo** | VP (True Positive) | FN (False Negative) |
| **Real Negativo** | FP (False Positive) | VN (True Negative) |

### Métricas

- **Accuracy**: $\frac{VP+VN}{VP+VN+FP+FN}$
- **Precision**: $\frac{VP}{VP+FP}$
- **Recall/Sensitivity**: $\frac{VP}{VP+FN}$
- **F1 Score**: $\frac{2 \cdot \text{Precision} \cdot \text{Recall}}{\text{Precision} + \text{Recall}}$
- **TPR**: True Positive Rate = Recall
- **FPR**: False Positive Rate = $\frac{FP}{FP+VN}$

**Curva ROC**: TPR vs FPR; **AUC**: área bajo curva (mejor cuanto más cerca de 1)

## 7. Feature Engineering

**Codificación variables categóricas**:
- One-hot encoding
- Label encoding (ordinales)

**Transformaciones no lineales**:
- Potencias: $X^2, X^3, \sqrt{X}$
- Logaritmos: $\log(X)$
- Trigonométricas: $\sin(X), \cos(X)$
- Segmentación: discretizar continuas

**Interacciones**: productos $X_1 \cdot X_2$ u otras combinaciones no lineales de dos o más variables


## 8. Árboles de Decisión

**Entrenamiento**: seleccionar (variable, punto corte) que maximiza pureza en cada nodo; repetir hasta criterios de parada

**Criterios de pureza**:
- Clasificación: Gini, entropía
- Regresión: MSE (error cuadrático medio), MAE (error absoluto medio)

### Hiperparámetros (valores típicos)

- `max_depth`: 3-10
- `min_samples_split`: 2-10
- `min_samples_leaf`: 2-10
- `max_features`: auto, sqrt, log2
- `criterion`: gini, mse, entropy

**Overfitting**: modelo demasiado complejo, mal en test  
**Underfitting**: modelo demasiado simple, mal en train y test

**Importancia de variables**: contribución de cada variable al desempeño

### Ensambles

- **Random Forest**: múltiples árboles construidos paralelamente muestreando observaciones y variables
  - Hiperparámetros: `n_estimators`, + los de árbol
- **Gradient Boosting**: árboles secuenciales corrigiendo errores de los anteriores
  - Hiperparámetros: `n_estimators`, `learning_rate`, + los de árbol

**Trade-off**: flexibilidad vs interpretabilidad

## 9. Validación y Optimización

**Train-test split**: dividir datos (típico: 70-30 o 80-20)  
**Cross-validation**: k-fold para validación robusta

**Optimización hiperparámetros**:
- Grid Search: exhaustivo
- Random Search: aleatorio
- Guided Search: tiene en cuenta búsquedas previas (Optuna)

## 10. Clasificación Multiclase

- **One-vs-Rest (OvR)**: un clasificador por clase vs resto → más rápido
- **One-vs-One (OvO)**: un clasificador por par de clases → más preciso con clases desequilibradas

## 11. Series Temporales
El orden de las observaciones importa y hay que tenerlo en cuenta al definir el modelo, al entrenar, al evaluar y al predecir

**Descomposición aditiva**: $Y_t = \text{Tendencia} + \text{Estacionalidad} + \text{Ruido}$
