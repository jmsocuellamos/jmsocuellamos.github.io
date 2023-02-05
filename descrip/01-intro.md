# Comenzamos {#introlibro}

La importancia de la estadística dentro del campo experimental siempre ha sido muy relevante, ya que para poder extraer conclusiones de un conjunto de datos experimentales se hace necesaria la utilización de procedimientos estadísticos más o menos sofisticados. Con la irrupción de los ordenadores personales y de los programas estadísticos para legos en la materia, así como la explosión tecnológica que estamos viviendo en los últimos años, la importancia de un correcto estudio estadístico de los datos experimentales se hace más necesaria que nunca. Se siguen publicando trabajos de investigación basados en datos experimentales donde el tratamiento estadístico de la información allí recogida puede considerarse como decepcionante. Con esta materia pretendemos guiar al estudiante en un correcto uso y análisis de las técnicas estadísticas más habituales en los diseños experimentales.

El tratamiento estadístico de datos experimentales se puede caracterizar en dos grandes áreas: **estudios descriptivos** y **análisis y modelización**. Los estudios descriptivos se centran en el procesado de los datos experimentales obtenidos con el objetivo de establecer o reflejar posibles patrones o tendencias en su comportamiento. Se engloban dentro de este ámbito todas la técnicas estadísticas que permiten los resúmenes numéricos y gráficos de la información observada, así como la detección de observaciones anómalas, la transformación y el filtrado de los datos experimentales. Sin embargo, los estudios descriptivos tienen la gran limitación de que sus resultados están circunscritos a los datos observados, y por tanto no se pueden generalizar a la población más general de la que se han obtenido. En el análisis y modelización se pretende generalizar los posibles patrones de comportamiento observados, en la fase descriptiva, mediante la construcción de modelos que nos permiten aproximar el comportamiento de datos experimentales no observados. Evidentemente la construcción de dichos modelos estadísticos no es una tarea rutinaria que debe tomarse a la ligera. La propia naturaleza de los datos observados puede dar una idea de los posibles modelos que se pueden utilizar, pero el modelo final obtenido es el resultados de un proceso iterativo de construcción, verificación y validación que puede resultar costoso en algunas situaciones.

La modelización estadística resulta relevante para representar el comportamiento de los datos experimentales de la forma más sencilla posible mediante modelos matemáticos donde se introduce de forma natural la incertidumbre de cualquier diseño experimental. Esta asignatura se centrará en la fase de modelización pero para poder llegar a comprender su naturaleza es necesario introducir primero los conceptos básicos de cualquier estudio estadístico, así como los procedimientos de estadística descriptiva y el estudio de la aleatoriedad en los diseños experimentales.

Este tema establece las definiciones básicas de cualquier estudio estadístico sobre diferentes ejemplos e introduce la nomenclatura básica de los modelos estadísticos que estudiaremos más adelante.

> *Usar la estadística no necesariamente es sinónimo de utilizar palabras raras o de hacer cálculos complicados. Significa que deseamos ver la realidad de forma objetiva, a través de datos que reflejen de la mejor manera posible qué es lo que está ocurriendo. Una vez se tienen los datos hay que saber sacarles la información y saberla plasmar de forma clara y convincente.*

## Conceptos básicos del diseño experimental

En esta sección presentamos los conceptos básicos que utilizaremos a lo largo de la materia. Se trata únicamente de un resumen muy esquemática, pero nos sirve para sentar las bases de los temas siguientes.

### Objetivo del diseño experimental

El objetivo de cualquier diseño experimental es aquellos que pretendemos estudiar en función del tipo de información que se ha recogido y del tipo de premisas establecidas antes de la recolección de los datos. Además es importante establecer el número de repeticiones del experimento que vamos a realizar, ya que eso condicionará el análisis de dichos datos. Si nuestro diseño experimental es muy complejo puede ocurrir que plantemos más de un objetivo.

**Ejemplo 1 (Degradación compuesto orgánico).** Se va a realizar un experimento para conocer el tiempo que tarda en degradarse un compuesto orgánico. En este caso nuestro objetivo es el tiempo hasta la degradación. Si el experimneto considera diferentes tipos de compuestos nuestro objetivo podría ser comparar el tiempo de degradación en función del tipo de compuesto.

### Población y muestra

Se define la población como el conjunto de sujetos u objetos que son de interés para el objetivo u objetivos planteados en nuestro diseño experimental. EL problema principal es que la población de sujetos u objetos suele ser demasiado grande para poder analizarla de forma completa, y por tanto debemos acudir a un subconjunto de dicha población para llevar a cabo nuestro diseño experimental.

Se define la muestra como el subconjunto de la población a la que accedemos para obtener la información necesaria de cara a responder de la forma más precisa posible al objetivo u objetivos planteados.

### Medidas y escalas de medida

Una medida es un número o atributo que se puede calcular para cada uno de los miembros de la población que está relacionado directamente con el objetivo de interés de la investigación. El conjunto de medidas obtenidas para cada uno de los elementos muestrales se denominan datos muestrales.

EL conjunto de medidas que se pueden observar y registrar para un conjunto de sujetos u objetos bajo investigación se denominan variables. Por tanto, una variable es el conjunto de valores que puede tomar cierta característica de la población sobre la que se realiza el estudio estadístico. Se distinguen dos tipos que pasamos a describir a continuación.

#### Variables cualitativas

Son el tipo de variables que como su nombre lo indica expresan distintas cualidades, características o modalidad. Cada modalidad que se presenta se denomina atributo o categoría, y la medición consiste en una clasificación de dichos atributos. Las variables cualitativas pueden ser dicotómicas cuando sólo pueden tomar dos valores posibles, como sí y no, hombre y mujer o ser politómicas cuando pueden adquirir tres o más valores. Dentro de ellas podemos distinguir:

i)  **Variable cualitativa ordinal**: La variable puede tomar distintos valores ordenados siguiendo una escala establecida, aunque no es necesario que el intervalo entre mediciones sea uniforme, por ejemplo: leve, moderado, fuerte.
ii) **Variable cualitativa nominal**: En esta variable los valores no pueden ser sometidos a un criterio de orden, como por ejemplo los colores.

#### Variables cuantitativas

Son las variables que toman como argumento cantidades numéricas. Las variables cuantitativas además pueden ser:

i)  **Variable discreta**: Es la variable que presenta separaciones o interrupciones en la escala de valores que puede tomar. Estas separaciones o interrupciones indican la ausencia de valores entre los distintos valores específicos que la variable pueda asumir. Ejemplo: El número de hijos (1, 2, 3, 4, 5). En muchas ocasiones una variable cualitativa ordinal puede ser interpretada como una variable discreta asociando a las categorías de la variable valores numéricos respetando el orden o escala establecida. Por ejemplo a la escala leve, moderado y fuerte le podríamos asociar la escala 1, 2 y 3 para mantener el orden.\
ii) **Variable continua**: Es la variable que puede adquirir cualquier valor dentro de un intervalo especificado de valores. Por ejemplo el peso (2,3 kg, 2,4 kg, 2,5 kg,...), la altura (1,64 m, 1,65 m, 1,66 m,...), o el salario. Solamente se está limitado por la precisión del aparato medidor, en teoría permiten que existan valores infinitos entre dos valores observados.

De forma habitual, la estructura de cualquier banco de datos (asociado a un diseño experimental) tiene una estructura matricial donde en las filas se colocan los sujetos bajo estudio y en las columnas se sitúan las variables medidas para cada uno de ellos.

Asociada a cada variable de nuestro banco de datos se puede establecer lo que conocemos como parámetro o parámetros de interés de la variable.

**Ejemplo 2 (Variable de interés).** Para el diseño experimental del estudio de la degradación del compuesto orgánico presentado en el ejemplo \@ref(exm:ejemplo1), la variable de interés es de tipo continuo y viene dada por el tiempo de degradación asociado a cada repetición del experimento. Sin embargo, a la hora de extraer conclusiones no podemos presentar todo el conjunto de datos sino que recurrimos a un resumen de dichos datos.

### Parámetros poblacionales y estadísticos

Asociado a cada variable se puede establecer lo que conocemos como **parámetro o parámetros** de interés de la variable. En el ejemplo anterior el parámetro de interés es el tiempo medio de degradación. Dado que generalmente no es posible examinar toda la población y debemos recurrir a una muestra de dicha población, es imposible conocer el verdadero valor del parámetro asociado con dicha variable. Para sortear este problema definimos el **estadístico** como una realización del parámetro para los datos muestrales observados. Por tanto el valor del estadístico (denominado estimación) varia entre dos muestras de las misma población. Cuanto mayor es la muestra más se parecerá el valor del estadístico al del parámetro.

En ocasiones ocurrirá que el número de parámetros asociado con una variable no es único, ya que se pueden establecer varios parámetros para estudiar el comportamiento de una variable. En el caso de variables de tipo cuantitativo siempre existen dos parámetros de interés: la media y la desviación típica. El primero nos indica como se sitúan los datos mientras que el segundo nos indica como se reparten los datos muestrales alrededor de la media.

**Ejemplo 3 (Parámetro de interés).** Para el diseño experimental del estudio de la degradación del compuesto orgánico presentado en el ejemplo \@ref(exm:ejemplo1), el parámetro poblacional de interés es el tiempo medio de degradación, mientras que el estadístico es la media del tiempo de degradación observado para los sujetos de la muestra. Distinguimos entonces entre media poblacional (parámetro) y media muestral (estadístico).

## Primeros pasos con `R` y `RStudio`

Para poder utilizar el código expuesto en estos materiales es necesario la instalación del programa `R`, del programa `RStudio` y de diferentes librerías de `R`. En los puntos siguientes se describe brevemente cada uno de estos puntos. Se recomienda además la consulta de los materiales electrónicos siguientes para complmentar la formación.

-   Childs, D. Z. (2017). APS 135: Introduction to Exploratory Data Analysis with R. [Versión electrónica](https://dzchilds.github.io/eda-for-bio/).
-   Grosser, M. 2017. Tidyverse Cookbook. [Versión electrónica incompleta](https://bookdown.org/Tazinho/Tidyverse-Cookbook/).
-   Wickham, H. 2015. Advanced R. CRC Press. [Versión electrónica resumida](https://adv-r.hadley.nz/).
-   Wickham, H. 2010. ggplot2. Third Edition. Springer. [Recursos electrónicos](http://ggplot2.org/).
-   Wickham, H. & Grolemund, G. 2016. R for Data Science. O'Reilly. [Versión electrónica resumida](http://r4ds.had.co.nz/).

### Instalación y puesta en marcha

-   **Instalación de R y RStudio**. Leer el capítulo de introducción del libro de Childs (2017) e instalar ambos programas descargándolos de sus correspondientes webs:

    -   Para instalar el programa `R`: <https://cran.r-project.org/>
    -   Para instalar RStudio: <https://rstudio.com/>

-   **Primeros pasos en R y RStudio**. Leer los capítulos 1, 2, y 3 de Childs (2017), el capítulo 4 de Wickham (2015), y los capítulos 4 y 6 de Wickham (2016) para un desarrollo más amplio. Realizar los ejercicios que van a apareciendo a lo largo de los capítulos.

-   **Estructuras de datos**. Leer los capítulos 4, 5, 6 y 9 de Childs (2017) para una breve introducción y los capítulos 2 y 3 de Wickham (2015) para completar la información. Realiza los ejercicios que van apareciendo.

-   **Instalación y uso de librerías en RStudio**. Leer el capítulo 8 de Childs (2017) e instala las librerías tidyverse, stringr, forcats, lubridate, magrittr, broom y datasets mediante la consola de RStudio. Carga las librerías con el comando library para comprobar que se han instalando de forma correcta.

-   **Creación de proyectos y entornos de trabajo**. Leer el capítulo 8 de Wickham (2016). Crea un proyecto para esta unidad, selecciona el directorio de trabajo donde se encuentra situado el proyecto, y guarda el entorno de trabajo.

## Informes prediseñados

En los últimos tiempos se ha puesto de modo la creación de informes directos apartir del código utilizado en Rstudio mediante la creación de documentos específicos. Su puede consulyar una guía sencilla de uso en [enlace](http://www.unavarra.es/personal/tgoicoa/ESTADISTICA_RMarkdown_tomas/basicRmarkdown/index.html). Un desaroollo más completo se puede ver en este [enlace](https://bookdown.org/yihui/rmarkdown/). También se puede consultar este [vídeo](https://www.youtube.com/watch?v=V0eJE55aTrY).

## Librerías de `R` necesarías

Para poder utilizar el código expuesto en estos materiales es necesario la instalación de diferentes librerías de `R`. A continuación se encuentra el código para cargar dichas librerías. Para algún análisis especifíco se utilizará alguna librería accesoria que será cargada con el comando `require()`.


```r
library(tidyverse)
library(readr)
library(tidymodels)
library(stringr)
library(forcats)
library(lubridate)
library(magrittr)
library(broom)
library(pubh)
library(datasets)
library(lmtest)
library(MASS)
library(kableExtra)
library(mosaic)
library(latex2exp)
library(moonBook)
library(sjlabelled)
library(sjPlot)
library(reshape2)
library(olsrr)
library(ggfortify)
library(mgcv)
library(modelr)
library(alr4)
library(equatiomatic) 
library(survival)
library(survminer)
library(knitr)
```

Configuramos además el tema de los gráficos para que tengan un aspecto más limpio y más fácil de exportar en formato pdf o word. Para ellos utilizamos la función `theme_set()`.


```r
theme_set(theme_sjplot2())
```
