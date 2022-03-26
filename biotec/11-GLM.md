# Modelos Lineales Generalizados {#glm}

Todos los modelos estudiados hasta ahora comparten los mismos supuestos teóricos, dado que el tipo de variable respuesta es siempre de tipo numérico. Aunque este tipo de modelos abarcan un gran conjunto de situaciones experimentales, también es cierto que en algunos casos resulta difícil cumplir las hipótesis establecidas. De hecho, este tipo de modelos se asocian habitualmente con variables respuesta de tipo Normal, pero ¿qué ocurre cuando la variable no puede considerarse Normal? ¿Cómo podemos modelizar situaciones experimentales donde la variable respuesta es de tipo Binomial o Poisson?

Son este tipo de situaciones las que motivan la introducción de un nuevo tipo de modelos que como caso particular engloban todos los vistos en los temas anteriores. Concretamente estudiaremos situaciones donde:

* la variable respuesta es de tipo Binomial, es decir, hemos observado si se cumple o no cierta condición experimental como resultado de un conjunto de variables predictoras que pueden ser de tipo numérico o categórico. 
* la variable respuesta es de tipo Poisson, es decir, hemos observado el número de ocasiones que ha ocurrido un evento en un tiempo o espacio determinado como resultado de un conjunto de variables predictoras que pueden ser de tipo numérico o categórico. 
* la variable respuesta es de tipo Exponencial, es decir, hemos observado el tiempo transcurrido hasta que ocurre un evento de interés como resultado de un conjunto de variables predictoras que pueden ser de tipo numérico o categórico.
* la variable respuesta es de tipo numérico, pero sólo puede tomar valores positivos de forma asimétrica, es decir, se encuentra concentrada en un conjunto de valores y su frecuencia disminuye cuando aumenta el valor de la respuesta. Este tipo de variable se conoce como Gamma.

La característica común a todas estas situaciones es que la respuesta se puede englobar dentro de un conjunto de variables aleatorias cuya función de densidad puede ser escrita a través de una misma expresión. Todo este conjunto de variables se conocen con el nombre de familia exponencial.


## Modelo Teórico

Supongamos que hemos observado una situación experimental que involucra $n$ sujetos cuyo conjunto de datos dispone de una variable respuesta $Y$ y $p$ variables predictoras $X_1,...,X_p$. Denotamos por $Y_i$ al valor de la respuesta para el sujeto $i$, y $x_{i1},...,x_{ip}$ al valor de las predictoras para el mismo sujeto. Como ya vimos anteriormente el objetivo de cualquier modelo es estudiar el comportamiento de la respuesta a partir de la información contenida en las variables predictoras, es decir:

$$E(Y_i|x_{i1},...,x_{ip}) = \mu_i$$

Los supuestos del GLM son:

* $Y_i$ son observaciones aleatorias e independientes cuya distribución de probabilidad corresponde a la familia exponencial con $E(Y_i) = \mu_i$.
* Las variables predictoras proporcionan un conjunto de predictores lineales $\eta_i$:

$$\eta_i = \beta_0 + \beta_1 x_{i1} + \beta_2 x_{21} + ... + \beta_p x_{ip}$$

* Existe una función $g()$ denominada función link (enlace) que establece que 

$$g(\mu_i) = \eta_i = \beta_0 + \beta_1 x_{i1} + \beta_2 x_{21} + ... + \beta_p x_{ip}$$

Es decir, tenemos un modelo lineal donde modelizamos la media de la respuesta sino una función de dicha media. A continuación se muestran las funciones link más habituales para los diferentes tipos de variable respuesta.

### Distribución Binomial (logit link)

En esta situación el valor esperado de la respuesta viene dado por $E(Y_i) = n \pi_i$, donde $\pi_i$ es la probabilidad de éxito, y la función link más habitual es:

$$g(\mu_i) = log\left(\frac{\mu_i}{n - \mu_i}\right)$$
Si sustituimos el valor de $\mu_i$ y simplificamos tenemos que:

$$g(\mu_i) = log\left(\frac{\pi_i}{1 - \pi_i}\right)$$
donde podemos ver que el objeto de nuestra modelización es una función de la probabilidad de éxito, que en realidad es nuestro parámetro de interés en este tipo de situaciones. 

#### Ejemplo

En un experimento se sometió a cierto número de cucarachas a cinco horas de exposición a disulfato de carbono gaseoso a varias concentraciones. Se pretendía investigar la relación existente entre la dosis de disulfato administrada y la resistencia de los insectos. La variable respuesta es $Y_i$ = número de escarabajos muertos en un total de ni sometidos a una misma dosis de pesticida $x_i$. La distribución habitual es binomial, $Y_i ~  Bi(n_i; \pi_i)$ para $i = 1,...,n$, con $\pi_i$ = probabilidad de muerte a dosis $x_i$. 

### Distribución Poisson (log link)

En esta situación el valor esperado de la respuesta viene dado por $E(Y_i) = \mu_i$, donde $\mu_i$ es la tasa de ocurrencia, y la función link más habitual es:

$$g(\mu_i) = log(\mu_i)$$

donde podemos ver que el objeto de nuestra modelización es una función de la tasa de ocurrencia, que en realidad es nuestro parámetro de interés en este tipo de situaciones. 

#### Ejemplo

Se realiza un estudio en pacientes con una forma de cáncer de piel llamado melanoma maligno. En dicho estudio se recogió información sobre la localización del tumor y su tipo histológico. Los datos son el número de pacientes en cada combinación de tipo de tumor y localización. El interés básico del análisis es investigar la relación entre el tipo de tumor y su localización.

### Distribución Normal (link identidad)

En esta situación el valor esperado de la respuesta viene dado por $E(Y_i) = \mu_i$, donde $\mu_i$ es la media de la población, y la función link más habitual es:

$$g(\mu_i) = \mu_i$$

que corresponde exactamente con la expresión de los modelos lineales que hemos trabajo en temas anteriores.

### Distribución Gamma (link recíproco)

En esta situación la función link es:

$$g(\mu_i) = \frac{1}{\mu_i}$$

#### Ejemplo

Se realiza un ensayo clínico donde se registra el tiempo de supervivencia (en semanas) para pacientes de leucemia y su correspondiente conteo inicial de células blancas en la sangre (en escala log10). El interés del análisis es intentar predecir el tiempo de supervivencia $Y$ en función del número inicial de células blancas $x_i$. Una distribución usual en la modelización de tiempos de superveniencia es la exponencial, que es un caso particular de la distribución Gamma.

## Estimación de los GLM

Es evidente que debemos modificar los procedimientos de estimación de los parámetros ya que la estructura lineal se asume sobre la transformación de la media de la respuesta y no sobre los valores de la respuesta directamente como ocurría en los modelos lineales. Los métodos de máxima verosimilitud para la estimación de los parámetros se basan en el *Método de Scoring* o el *Método de Newton-Raphson*. Se trata de métodos de estimación iterativos, y no directos como en el caso de los modelos lineales, que finalizan cuando se alcanzan las condiciones de convergencia del método. En la mayoría de situaciones experimentales dichos métodos convergen en pocas iteracciones y proporcionan las estimaciones del parámetro del modelo. Haciendo uso del Teorema Central del Límite resulta posible obtener además intervalos de confianza para los parámetros del modelo.

Para la estimación de este tipo de modelos utilizamos la función `glm()` cuya estructura viene dada por:


```r
glm(modelo,family = distri(link = tipo),data_set)
```

donde `modelo` representa la ecuación del modelo en la forma $respuesta \sim predictoras$, `distri` representa la distribución de probabilidad asociada con la respuesta, `tipo` la función link utilizada y `data_set` el conjunto de dato sobre el que se ajusta el modelo. En temas sucesivos veremos como especificar esta función en cada una de las situaciones experimentales planteadas.


Para estudiar en profundidad los procedimientos de estimación se puede leer el capítulo 2 de MayMor01.

## Bondad del ajuste

Un aspecto importante en el ajuste de un modelo es determinar si describe adecuadamente o no los datos observados. Cuando ajustamos un modelo lineal generalizado, juzgamos la adecuación del modelo comparando la verosimilitud del modelo ajustado con la verosimilitud del modelo saturado.

El modelo saturado es un modelo de forma similar al modelo propuesto que describe de modo perfecto los datos. Por tanto, tiene poca utilidad desde el punto de vista de ajuste de un modelo. Sin embargo, es útil para medir cómo un ajuste concreto se parece a un ajuste “perfecto”. 

**Ejmplo.** *Considerando $Y_1,...,Y_n$ v.a. normales, con $E(Y_i) = \mu_i$ y varianza común $\sigma^2$, se propone un modelo en el que todas las medias $\mu_i$ son iguales,
$$\mu_1 = ... = \mu_n = \mu.$$* 

*En el modelo saturado se usa la misma distribución (normal), el mismo link (identidad), y hay un parámetro a estimar por cada dato $(\mu_i \to  y_i)$:*

$$\text{Modelo propuesto } E(Y_i) = \mu, i=1,...,n \to \widehat{\mu}_i = \bar{y}$$
$$\text{Modelo saturado } E(Y_i) = \mu_i, i=1,...,n \to \widehat{\mu}_i = y_i$$

En los GLM el estadístico utilizado para valorar la bondad del ajuste obtenido se denomina `D = Deviance`. Dicho estadístico mide como de grande es la desviación del modelo ajustado respecto de los datos (modelo saturado). Si $D$ es grande, el modelo ajustado proporciona un ajuste pobre. Un valor pequeño de $D$ es indicador de un buen ajuste. Para decidir qué se considera “D grande” y qué “D pequeño”, es preciso utilizar la distribución en el muestreo del estadístico.

*Cuando el modelo propuesto proporciona un buen ajuste de los datos, entonces*
$$D \text{ } \dot{\sim} \text{ } \chi^2_{n-p}, \text{ asintóticamente}$$
*donde $n$ es el número de datos a ajustar y $p$ el número de parámetros del modelo.*

En la práctica comparamos el valor de la deviance con el cuantil 0.95 de una $Chi^2$ con $n-p$ grados de libertad. Si $D$ es menor que el cuantil diremos que tenemos un buen ajuste. En la práctica obtendremos el p-valor asociado con dicha deviance y lo comparemos con el valor de referencia 0.05. Si el p-valor obtenido es superior a 0.05 diremos que tenemos un buen ajuste.

## Comparación de modelos

Aunque un modelo con más parámetros proporcione un ajuste mejor que otro con menos parámetros, cabe plantearse si en realidad todos los parámetros estimados son necesarios. Esta cuestión se puede resolver con tests basados en la deviance y en el estadístico de Wald, siempre y cuando se trate de modelos anidados. Otros estadísticos como el AIC permiten la comparación más general de modelos no necesariamente anidados.

Dos modelos en competencia pueden ser comparados mediante la deviance cuando tienen la misma distribución y función link y sólo difieren en el número de parámetros, es decir, cuando se trata de modelos anidados. Supongamos dos modelos $M1$ con $p_1$ parámetros y $M2$ con $p_2$ parámetros, de forma que el primero está  anidado en el segundo, es decir, $p_1 < p_2$. En esta situación el estadístico para valorar si ambos modelos pueden considerarse iguales, es decir, debemos elegir $M1$ ya que es más simple, sigue una distribución $\chi^2_{p_2-p_1}$. Para resolver la comparación debemos obtener el cuantil 0.95 de la distribución chi cuadrado y compararlo con el valor del estadístico. Si el pvalor asociado es inferior a 0.05 rechazaremos el modelo $M1$ en favor del modelo $M2$, mientras que si es superior a 0.05 elegiremos el modelo $M1$.

Para la selección automática de efectos en la construcción de un modelo se pueden utilizar los estadísticos AIC o el BIC. En ambos casos seleccionaremos el modelo con menor valor en dichos estadísticos. Procedemos de igual forma a los que hacíamos en los modelos lineales que involucran más de una variable predictora.

## Diagnóstico de los GLM

Los residuos se pueden utilizar para explorar la adecuación del ajuste de un modelo respecto a la elección de la función de varianza, la función link y los términos a incluir en el predictor lineal. Los residuos pueden también ser útiles para detectar observaciones influyentes o valores anómalos que requieran una investigación más intensa. Para modelos lineales generalizados, requerimos una definición algo más amplia de residuos, que sea aplicable a todas las distribuciones que pueden reemplazar a la Normal.

Los gráficos de residuos pueden ser muy útiles a la hora de detectar fallos sistemáticos en un modelo para ajustar unos datos. Detectados los fallos, la labor siguiente consiste en solventar las deficiencias y reajustar el modelo. Sin embargo, cuando la respuesta toma sólo unos pocos valores, como ocurre en pruebas Bernoulli, la utilidad de los residuos es limitada.

La especificación incorrecta de un modelo se puede deber a una serie de factores:

* elección incorrecta de la distribución de probabilidad,
* especificación incorrecta de la forma en que la respuesta media cambia con las variables explicativas, ya sea porque:
    * la componente sistemática $\eta()$ está mal especificada,
    * o la función link $g()$ no es apropiada,
  
* variables explicativas no incorporadas en el modelo,
* funciones incorrectas de las variables explicativas en el modelo, incluyendo interacciones desconocidas entre ellas, o no disponibilidad de suficientes funciones diferentes,
* dependencia entre las observaciones, por ejemplo, a lo largo del tiempo (autocorrelación).

Se definen en este caso los *residuos deviance estandarizados* que son los utilizados para diagnosticar el modelo, obtenidos como los residuos asociados con el predictor lineal. Sin embargo, dada la estructura de los GLM podemos definir también los residuos de la respuesta que son los obtenidos entre el valor de la respuesta y el proporcionado al deshacer el cambio involucrado en la función link.

Utilizaremos los procedimientos gráficos vistos en temas anteriores para proceder con el diagnóstico de este tipo de modelos.

## Predicción de los GLM

La predicción en este tipo de modelos se divide en dos fases:

* predicción del predictor lineal
* predicción de la respuesta

Dado un GLM con predictor lineal dado por $\eta_i = X_i\beta$ y función link $g(\mu_i)$, el proceso de estimación del modelo nos proporciona los valores estimados de $\beta$, de forma que obtendríamos el valor ajustado del predictor lineal como:
$$\widehat{\eta}_i = X_i\widehat{\beta}$$

y el valor ajustado de como

$$\mu_i = g^{-1}(\widehat{\eta}_i) = g^{-1}(X_i\widehat{\beta})$$



