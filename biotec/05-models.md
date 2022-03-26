# Modelos estadísticos {#modelstats}


De forma habitual, cuando el investigador (o investigadores) se plantea un diseño experimental y comienza con la recogida de datos es porque persigue el estudio de o verificación de un objetivo planteado sobre la población bajo estudio. Estos objetivos se suelen establecer en base a teorías o hipótesis que se desean verificar sobre le funcionamiento de la población bajo ciertas condiciones experimentales. Por ejemplo:

* Teorías que establezcan la posible relación entre dos características de la población.
* Teorías que plateen la idea de comportamientos distintas para una característica de la población
en función de una variable que clasifica a los sujetos bajo estudio en diferentes grupos.

Es entonces cuando la modelización estadística interviene y el analista busca el mejor modelo que ajusta los datos disponibles y proporciona predicciones fiables. El objetivo de la modelización estadística es el planteamiento de una expresión matemática que representa el comportamiento general de la población bajo estudio, teniendo en cuenta el diseño experimental establecido y el objetivo u objetivos que se desean verificar 


## Componentes del modelo

Un primer paso en la modelización estadística es el planteamiento de una expresión matemática que represente el comportamiento general de la población bajo estudio teniendo en cuenta el diseño experimental establecido y el objetivo u objetivos que se desean verificar. Esto es lo que se conoce como componente sistemática del modelo y se basa únicamente en la parte controlada del diseño experimental. Por ejemplo, si nos planteamos como objetivo conocer la suma de dos números $a$ y $b$, la función matemática (sistemática) que permite expresar la suma de forma única es $a+b$. Esta componente sistemática es una función determinista, pues siempre proporciona el mismo resultado si los valores de entrada son iguales. Al proponer la parte sistemática (o determinista) de un modelo será siempre necesario concretar la variable que se asocia al objetivo o hipótesis planteada sobre la población (representada por $Y$) y la variable o variables $(X_1, X_2,…)$ relacionadas o supuestamente relacionadas con ella a través de la función matemática especificada.

Supongamos un diseño experimental en el que tenemos una variable $Y$ que está ligada directamente con el objetivo de la investigación, y un conjunto de variables $X_1, X_2,…,X_k$, que se supone que pueden influir en el comportamiento de $Y$. Habitualmente a $Y$ se la denomina *variable respuesta* o *variable dependiente* y a las $X’s$ *variables predictoras*, *variables explicativas* e incluso *covariables* cuando se trata de variables de tipo numérico continuo. Cuando las variables $X$ son de tipo categórico se suelen denominar factores explicativos o de clasificación. A las variables $X$ se las suele denominar también variables independientes, asumiendo independencia entre ellas, aunque esta acepción puede estar algo alejada de la realidad como discutiremos más adelante; en adelante no utilizaremos esta denominación y optaremos por cualquiera de las anteriormente presentadas. En la situación más sencilla donde la respuesta puede venir influenciada de forma directa por las posibles predictoras, la respuesta media ($\hat{Y}$) se puede modelizar a través de una función $f$ que describe la **componente sistemática** del modelo:

\begin{equation}
  \hat{Y} = f(X_1,X_2,...,X_k)
  (\#eq:sistcomp)
\end{equation}

Si nuestro modelo es adecuado, esta función debe reflejar el comportamiento medio esperado de la variable respuesta. Dado que sujetos distintos con los mismos valores de las $X’s$ producirán generalmente valores distintos en la respuesta, se hace necesaria la introducción de una componente variable en el modelo. Esta componente se denomina **componente aleatoria** y está relacionada directamente con la variabilidad de los sujetos en la respuesta para una misma combinación de valores de las variables predictoras. La denotaremos habitualmente por:

\begin{equation}
  \epsilon
  (\#eq:aletcomp)
\end{equation}

que es una variable aleatoria con distribución de probabilidad $F$.
Asumiendo que \@ref(eq:sistcomp) y \@ref(eq:aletcomp) tienen un efecto aditivo sobre la respuesta, nuestro modelo base de partida vendrá dado por la expresión:

\begin{equation}
  Y = \hat{Y} + \epsilon = f(X_1,X_2,...,X_k) + \epsilon 
  (\#eq:model)
\end{equation}

En función del tipo de variable respuesta, las predictoras, de la relación que se pueden establecer entre ellas a través de $f$, y del establecimiento de las estructuras aleatorias $F$ para los errores tendremos diferentes tipos de modelos. A lo largo de esta materia veremos las diferentes posibilidades de modelización.

## Tipos de modelos

En función del tipo de variable respuesta, las predictoras, de la relación que se pueden establecer entre ellas a través de $f$, y del establecimiento de las estructuras aleatorias $F$ para los errores tendremos diferentes tipos de modelos. A lo largo de esta materia veremos las diferentes posibilidades de modelización. A lo largo de las unidades siguientes iremos estudiando las características de los diferentes modelos, pero estos se pueden agrupar en dos grandes apartados:

* **Modelos Lineales (LM)**, que engloban los modelos de regresión, los modelos ANOVA y los modelos ANCOVA. 
* **Modelos Lineales Generalizados (GLM)**, que engloba los modelos de respuesta binomial (modelos de regresión logística), modelos de respuesta poisson, modelos para tablas de contingencia (modelos log-lineales), y modelos de supervivencia.

Introduciremos además los modelos de suavizado y una breve introducción a los modelos de efectos aleatorios, que pueden ser utilizados en conjunción con los LM y los GLM.

## Fases en la construcción de un modelo

El proceso de modelización y análisis estadístico de un banco de datos se puede estructurar según las siguientes pautas de actuación:

1.	Contextualización del problema. Definición de objetivos y variables.
2.	Diseño del experimento y recogida de información.
3.	Registro y procesado previo de la información disponible.
4.	Inspección gráfica e identificación de tendencias.
5.	Consideración de hipótesis distribucionales y relacionales. Propuesta de modelización.
6.	Ajuste del modelo. Comparación y selección del mejor modelo.
7.	Diagnóstico y validación del modelo ajustado. 
8.	Valoración de la capacidad predictiva del modelo y predicción.
9.	Interpretación y conclusiones.

Si la revisión/validación del modelo nos lleva a descartarlo (punto 7), será preciso una nueva propuesta, de modo que entraremos en un bucle entre los puntos (5)-(7) que culminará cuando quedemos satisfechos con el diagnóstico y la validación del modelo.

A la hora de representar gráficamente la información de cada banco de datos tendremos en cuenta esta serie de principios básicos:

*	La información asociada con la variable respuesta que identifica el objetivo del estudio debe situarse siempre en el eje Y o eje de ordenadas.
*	El tipo de las variables que pueden influir en nuestra variable objetivo condiciona el tipo de gráfico. Así si estas son de tipo numérico debemos realizar un gráfico de dispersión, situando cada una de las variables predictoras $X$ en el eje de abcisas. Si las predictoras son de tipo categórico deberemos realizar un gráfico de cajas, visualizando las distintas categorías en el eje X (si bien siempre podremos invertir los ejes para mostrar las cajas en sentido horizontal y no vertical).
*	Si combinamos variables de tipo numérico y categórico debemos realizar gráficos múltiples de dispersión donde mostremos la relación $Y$ versus $X$ para las variables numéricas en cada uno de los niveles de las variables $X$ categóricas.
