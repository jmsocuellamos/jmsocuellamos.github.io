# Probabilidad {#prob}

En esta unidad introducimos los conceptos básicos de varaible aleatoria, distribución de probabilidad, y las variables aleatorias más habituales en la investigación científica. No se trata de una unidad sobre probabilidad (para lo que se recomienda acudir a libros con mayores desarrollos), sino una unidad práctica de conocimiento básico de las distribuciones de probabilidad que juegan un papel relevante en los procesos de inferencia y en la cnstruccion de modelos estadísticos que veremos en las unidades siguientes.

## Introducción {#prob-intro}

La probabilidad o el azar juega un papel muy importante en el razonamiento científico. Ejemplos de procesos biológicos donde la probabilidad juega un papel relevante son: i) la segregación de cromosomas en la formación de gametos o la ocurrencia de mutaciones genéticas. En otras ocasiones es el propio diseño experimental el que introduce la aleatoriedad como por ejemplo cuando dividimos un grupo de sujetos en función del tratamiento al que se van a ver sometidos.

Las conclusiones del análisis estadístico de datos se expresan en muchas ocasiones en términos de probabilidad, ya que implícitamente se está introduciendo la aleatoriedad debida a la muestra de sujetos con el que estamos trabajando, y que generalmente no coincide con toda la población bajo estudio.

En las unidades anteriores hemos visto que el estudio estadístico se centra en la información recogida sobre alguna variable relacionada directamente con el objetivo u objetivos del diseño experimental planteado. Un hecho cierto es que debido a la aleatoriedad e los sujetos resulta imposible saber con certeza el valor de dicha variable para un sujeto en particular. Se introduce de esta forma el concepto de **variable aleatoria** que hace referencia a todas aquellas en las que intrínsecamente se reconoce variabilidad en la respuesta de los sujetos.

En este punto introducimos algo de notación que nos resultará de utilidad de aquí en adelante. Las variables aleatorias siempre se denotan en mayúsculas, $Y$, mientras que los valores observados para un conjunto de sujetos en esa variable (muestra) se denotan por minúsculas e indicando la posición que el sujeto ocupa en el banco de datos $y_1,y_2,...,y_n$.

Por razones obvias se definen entonces las variables aleatorias discretas y las variables aleatorias continuas. Una variable discreta es aquella que sólo puede tomar un número finito o contable de posibles resultados, de forma que es posible conocer de antemano cuales son los posibles resultados que se pueden observar. Una variable continua es aquella que puede tomar infinitos valores numéricos, y por tanto es imposible identificar cada uno de los posibles valores de la variable, aunque si es posible conocer el rango de posibles resultados que se pueden observar. De forma natural se puede establecer una equivalencia entre la definición de variables categóricas y numéricas introducidas en unidades anteriores con las variables discretas y continuas.

Se puede describir de forma completa una variable aleatoria sin más que especificar la probabilidad asociada a cada uno de sus posibles valores. Esta especificación se conoce con el nombre de **distribución de probabilidad**. Sin embargo, la forma en que se puede especificar dicha distribución de probabilidad depende del tipo de variable aleatoria con la que estemos trabajando. En el caso de variables discretas basta con determinar la probabilidad de cada uno de los posibles resultados observables de la variable, pero no ocurre así en las variables continuas donde es imposible conocer todos sus posibles valores.

## Distribuciones-Modelos

En la Unidad anterior se ha visto que el objetivo principal de muchos análisis estadísticos es estudiar el comportamiento de los sujetos de una población, a partir de la información recogida en una muestra de sujetos seleccionados de dicha población. Sin embargo, cuando hablamos de una población lo hacemos teniendo en mente las variables que se han medido sobre ellos. Imaginemos que estamos interesados en conocer la nota media de acceso de todos los estudiantes de primero de grado en la Universidad Miguel Hernández de Elche (UMH). En este caso es evidente que la población objetivo son todos los estudiantes de primero de grado que han accedido a la UMH, aunque esa población contiene información sobre muchas otras variables (sexo, edad, localidad de residencia,...).Por lo tanto, todas las poblaciones se considerarán poblaciones de valores de alguna variable especificada. Si la población es infinita, nunca podremos obtener todos sus valores, e incluso si la población es finita, generalmente no queremos medir todos sus valores. En cualquier caso, deseamos obtener información sobre características particulares de la población a partir de un número restringido de valores de muestra. Por ejemplo, en el estudio de la nota de acceso podríamos querer utilizar la media de la nota media de todos los estudiantes como referencia de la población, aunque podríamos utilizar otros como el percentil 80, etc…

Para proceder de esta forma, necesitamos formular un modelo adecuado para los valores $x$ que componen la población, relacionar las características de interés con los aspectos apropiados del modelo y luego usar los datos de la muestra para proporcionar estimaciones de estos aspectos.La característica esencial de tales valores $x$ en su imprevisibilidad, y lo mejor que podemos hacer es especificar la probabilidad de obtener un valor dado o un valor en un rango dado. Por lo tanto, las distribuciones de probabilidad proporcionan los modelos más apropiados para las poblaciones variables de respuesta. Más específicamente, la función de densidad de probabilidad $f(x)$ o la función de distribución de probabilidad $F(x)$ proporcionan la información necesaria, por lo que constituye el modelo de población para la variable aleatoria $X$. Por supuesto, dado que nunca conocemos esta distribución con una certeza total, debemos suponer una forma funcional específica (modelo matemático) para $f(x)$ y $F(x)$. Esta forma funcional usualmente involucra uno o más parámetros que pueden ser variados, y leso que se espera es que habrá algunos valores específicos de estos parámetros para los cuales la distribución resultante se ajusta adecuadamente a nuestros datos observados. Tal modelo se llama **modelo paramétrico** para $X$. En el tema siguiente se presentarán los modelos paramétricos más frecuentes para una variable aleatoria $X$. 

Por el momento, supongamos que hemos especificado alguna función adecuada $f(x)$ como la densidad de probabilidad de nuestra población. ¿Qué valores resumen de esta densidad se relacionan con las características de la población que generalmente son de interés? Para responder a esta situación, centrémonos en las variables discretas e interpretemos la probabilidad como una frecuencia relativa. Si la variable aleatoria $X$ tiene valores posibles $x_1, x_2, x_3, ... ,x_n$ y si $p_i = P(X = x_i)$, entonces podemos pensar en $p_i$ como la frecuencia relativa con la que el valor $x_i$ ocurre en toda la población. 

Definimos el valor esperado de $X$ por la ecuación:

\begin{equation} 
  E(X) = \sum_{i=1}^n  x_i p_i
  (\#eq:espdisc)
\end{equation}

Utilizando la interpretación de frecuencia relativa de $p_i$ dada anteriormente, $E(X)$ puede interpretarse como el promedio de los valores $X$ en toda la población, por lo que es la media poblacional. Este es claramente uno de los principales valores de resumen para la población. A menudo se denota $\mu$ y como mide el "centro" de la población también se lo conoce como el parámetro de localización de la población.

De forma similar se define la varianza de la población, $\sigma2$, como:

\begin{equation} 
  \sigma^2 = V(X) = E\{(X-\mu)^2\} = \sum_{i=1}^n  (x_i - \mu)^2 p_i
  (\#eq:vardisc)
\end{equation}

La desviación típica poblacional, $\sigma$,  se obtiene  a partir de la raíz cuadrada de la varianza poblacional:

\begin{equation} 
  \sigma = DT(X) = \sqrt{V(X)}
  (\#eq:sddisc)
\end{equation}

Siguiendo con el ejemplo de la variable aleatoria que representa la suma del lanzamiento de dos dados ¿Cuál es el valor esperado de la suma de ambos lanzamientos? ¿Cuál es la desviación típica? En este caso ¿la distribución es aproximada o la distribución poblacional?

En el caso de variables aleatorias continuas, donde denotamos por $R$ el rango de todos los valores posibles, se define la media, varianza y desviación típica poblacional como

\begin{equation} 
  \mu = E(X) = \int_R x f(x) dx
  (\#eq:espcont)
\end{equation}

\begin{equation} 
  \sigma^2 = V(X) = \int_R (x - \mu)^2 f(x) dx
  (\#eq:varcont)
\end{equation}

\begin{equation} 
  \sigma = DT(X) = \sqrt{V(X)}
  (\#eq:sdcont)
\end{equation}

Para las variables de tipo continuo más habituales la esperanza, varianza y desviación típica poblacionales se aproximan  de forma precisa (si la muestra de trabajo es adecuada) por la media, varianza y desviación típica muestral.

## Distribuciones notables

En la práctica resultaría complicado y farragoso  si para cada experimento aleatorio ligeramente diferente de otro ya realizado, tuviéramos que determinar las distribuciones de probabilidad desde cero. Afortunadamente, podemos hacer uso de las  similitudes que existen entre ciertos tipos o familias de experimentos y ciertas distribuciones de probabilidad, conocidas con el nombre de **distribuciones notables**, que nos permiten el desarrollo de las funciones de distribución que representen las características generales del experimento.

Por ejemplo, muchos experimentos comparten el elemento común de que sus resultados se pueden clasificar en uno de dos eventos: una moneda puede salir cara o cruz; un niño puede ser hombre o mujer; una persona puede morir o no morir; una persona puede ser empleada o desempleada. Estos resultados a menudo se etiquetan como "éxito" o "fracaso", teniendo en cuenta que aquí no hay connotación de "bondad"; por ejemplo, al observar los nacimientos, el estadístico podría calificar el nacimiento de un niño como un "éxito" y la el nacimiento de una niña como un "fracaso", pero los padres no necesariamente verían las cosas de esa manera. Esta situación experimental se conoce como experimento Bernouilli, asignando probabilidad $\theta$ al suceso calificado como éxito y probabilidad  $1-\theta$ al suceso calificado como de fracaso. Dichas probabilidades son diferentes para cada situación y se pueden aproximar mediante la obtención de datos experimentales. La distribución de probabilidad que surge en este experimento se conoce como distribución Bernouilli y es la primera distribución de probabilidad notable conocida. 


### Distribución Binomial {#prob-binomial}

A menudo nos interesa el resultado de los ensayos independientes y repetidos de Bernoulli, es decir, el número de éxitos en ensayos repetidos. En esta situación se considera:

1. sucesos independientes: el resultado de un ensayo no afecta el resultado de otro ensayo.
2. repeticiones: las condiciones son las mismas para cada prueba, es decir, la probabilidad de éxito y fracaso permanecen constantes a través de los diferentes ensayos realizados. 

Una distribución binomial nos da las probabilidades asociadas con los ensayos independientes y repetidos de Bernoulli. En una distribución binomial, las probabilidades de interés son las de observar un cierto número de éxitos, $r$, en $n$ ensayos independientes, cada uno de los cuales tiene solo dos resultados posibles y la misma probabilidad, $\theta$, de éxito. Por ejemplo, usando una distribución binomial, podemos determinar la probabilidad de obtener 4 caras en 10 lanzamientos de una misma moneda.

La distribución de probabilidad para este experimento queda completamente determinado si conocemos $n$ (número de repeticiones realizadas) y $\theta$ (probabilidad de éxito). Si $X$ denota la variable aleatoria asociada con este experimento, la función de densidad de probabilidad se define como:

\begin{equation} 
  P(X = x) = {n \choose x} \theta^x (1-\theta)^{n-x}
  (\#eq:probbinom)
\end{equation}

donde ${n \choose x}$ representa el número combinatorio de $n$ sobre $x$, y $x$ el número de éxitos observados. Dicha distribución se denota habitualmente:

\begin{equation} 
  X \sim Bi(n, \theta)
  (\#eq:varbinom)
\end{equation}

Esta distribución se puede evaluar para cualquier valor entre 0 y el número de repeticiones del experimento $n$. En `R` la función `dbinom` permite obtener cualquier probabilidad de la distribución binomial una vez fijamos el valor que deseamos evaluar ($x$), el número de repeticiones ($n$), y la probabilidad de éxito asociada ($\theta$). 

Una vez establecida la función de densidad de probabilidad resulta posible obtener el valor esperado del número de éxitos, así como conocer su variabilidad haciendo uso de las definiciones expuestas en el tema anterior. En concreto, para una variable que sigue una distribución de probabilidad Binomial (\@ref(eq:varbinom)) tenemos que:

\begin{equation} 
  E(X) = n \theta
  (\#eq:espbinom)
\end{equation}

\begin{equation} 
  V(X) = n \theta (1-\theta)
  (\#eq:varbinom)
\end{equation}

\begin{equation} 
  DT(X) = \sqrt{n \theta (1-\theta)}
  (\#eq:sdbinom)
\end{equation}

En la página web http://www.artofstat.com/ se encuentra disponible una aplicación de trabajo de la distribución Binomial (https://istats.shinyapps.io/BinomialDist/). Dicha aplicación nos permite calcular tanto probabilidades como cuantiles para un experimento binomial. Utiliza dicha aplicación para responder a las cuestiones siguientes: 

* En una población de sujetos se conoce que el 39% de ellos sufre algún tipo de mutación genética. Si se obtiene una muestra de 20 sujetos ¿cuál es la probabilidad de observar tres sujetos con esa mutación genética?
* En una población de moscas de la fruta se conoce que el 30% son de color negro y el 70% son de color gris. Si se extrae una muestra de 15 moscas ¿cuál es la probabilidad de observar al menos cuatro moscas de color negro? ¿y de color gris?
* Una cierta droga causa daños en el hígado en el 1% de los pacientes. Se van a realizar estudios completos sobre 50 pacientes que están tomando dicha droga para detectar daños en el hígado. ¿Cuál es la probabilidad de que ninguno de lo pacientes muestre daños en el hígado? ¿Cuál es la probabilidad de que al menos uno de los pacientes muestre daños en el hígado?
* Los estudios realizados concluyen que el 10% de las adolescentes de EEUU tienen deficiencia de hierro. Se obtiene una muestra de 14 adolescentes y se desea conocer ¿cuál es la probabilidad de que al menos el 50% de ellas tengan una deficiencia de hierro?
* En un experimento se comprobó que la aplicación de un tratamiento químico aumentaba la resistencia a la corrosión de un material en un 80 % de los casos. Si se tratan ocho piezas, determina: (i) probabilidad de que el tratamiento sea efectivo para más de cinco piezas, (ii) probabilidad de que el tratamiento sea efectivo para al menos tres piezas, (iii) número de piezas para las que espera que el tratamiento sea efectivo.
* Se dispone de un cristal que tiene dos tipos de impurezas que absorben radiación de la misma longitud de onda. Una de ellas emite un electrón tras la absorción de un fotón, mientras que la segunda no emite electrones. Las impurezas están en igual concentración y distribuidas homogéneamente en el cristal. Sin embargo, la sección eficaz de absorción, que es una medida de la probabilidad de absorber un fotón, es 90 veces mayor para la impureza que emite electrones que el de la impureza que no los emite. Suponiendo que sobre el cristal inciden 200 fotones y que este es lo suficientemente grande para absorber todos, calcula la probabilidad de que al menos se emitan tres electrones.

### Distribución Poisson {#prob-poisson}
 
La distribución de Poisson se emplea como un modelo para variables aleatorias de tipo discreto cuando se quieren obtener las probabilidades de ocurrencia de un evento que se distribuye al azar en el espacio o el tiempo. Algunos ejemplos de esta distribución son:

* En el estudio de cierto organismo acuático, se toman un gran número de muestras de un lago y se cuentan el número de organismos que aparecen en cada muestra. El interés principal radica en conocer cuál es la probabilidad de encontrar algún organismo en una muestra próxima si la media observada en el conjunto de nuestras es de 2 organismos.
* En un estudio sobre la efectividad de un insecticida sobre cierto tipo de insecto, se fumiga una gran región. Posteriormente se crea una cuadrícula sobre el terreno, se selecciona de forma aleatoria un conjunto de ellas, y se cuenta el número de insectos vivos dentro de cada una. Estamos interesados en conocer cuál es la probabilidad de que no encontremos ningún insecto vivo en una cuadrícula próxima si se sabe que que la media de insectos vivos en las cuadriculas analizadas es de 0.5.  
* Un grupo de investigadores observó la ocurrencia de hemangioma capilar retiniano (RCH) en pacientes con la enfermedad de von Hippel-Lindau (VHL). RCH es un tumor vascular benigno de la retina. Usando una revisión retrospectiva de series de casos consecutivos, los investigadores encontraron que el número de medio de tumores RCH por ojo para pacientes con VHL era de 4. Están interesados en conocer cuál es la probabilidad de que se detecten más de cuatro tumores por ojo.
 
Como se puede ver en los ejemplos la distribución de Poisson es muy habitual en los campos de la biología y la medicina. En una distribución de Poisson, las probabilidades de interés son las de observar un número de eventos, $x$, en un tiempo o espacio determinado. La distribución de probabilidad para este experimento queda completamente determinada si conocemos el número de eventos y $\lambda$ la tasa o media del número de eventos que ocurren por unidad de tiempo o espacio. Si $X$ denota la variable aleatoria asociada con este experimento, la función de densidad de probabilidad se define como:

\begin{equation} 
  P(X = x) = \frac{e^{-\lambda} \lambda^x}{x!}
  (\#eq:probpois)
\end{equation}

Dicha distribución se denota habitualmente: $$ X \sim Po(\lambda)$$. Esta distribución se puede evaluar para cualquier valor entre 0 y el número de máximo de ocurrencias que pueden ocurrir. Dado que este valor no se conoce de antemano se debe prefijar un valor máximo que asegure que la probabilidad de ese valor sea cero.

En `R` la función `dpois` permite obtener cualquier probabilidad de la distribución de Poisson una vez fijamos el valor que deseamos evaluar ($x$), y la tasa o media del número de eventos ($\lambda$). 

Una vez establecida la función de densidad de probabilidad resulta posible obtener el valor esperado del número de eventos ocurridos, así como conocer su variabilidad haciendo uso de las definiciones expuestas en el tema anterior. En concreto, para una variable que sigue una distribución de probabilidad Poisson ($X \sim Po(\lambda)$) tenemos que:

\begin{equation} 
  E(X) = \lambda
  (\#eq:meanpois)
\end{equation}

\begin{equation} 
  V(X) = \lambda
  (\#eq:varpois)
\end{equation}

\begin{equation} 
  DT(X) = \sqrt{\lambda}
  (\#eq:sdpois)
\end{equation}


En la página web http://www.artofstat.com/ se encuentra disponible una aplicación de trabajo de la distribución Binomial (https://istats.shinyapps.io/PoissonDist/). Dicha aplicación nos permite calcular tanto probabilidades como cuantiles para un experimento poisson Utiliza dicha aplicación para responder a las cuestiones siguientes: 

(Hint: Recuerda que la tasa de la poisson se debe modificar si las cuestiones de interés no están referenciadas a la misma escala que la escala proporcionada).

* Un laboratorio es capaz de realizar 20 análisis de cierto tipo en un hora. ¿Cuál es la probabilidad de realizar entre 30 y 36 análisis en las próximas dos horas?¿y la probabilidad de realizar más de 36? (Hint: En este caso no concemos directamente la media de la poisson ya que la escala medida no es la misma sobre la que se este interesado, por lo que es necesario ajustar la tasa a cada pregunta)
* Consideremos que el número de trozos de chocolate en una determinada galleta sigue una distribución de Poisson. Queremos que la probabilidad de que una galleta seleccionada al azar tenga por lo menos tres trozos de chocolate sea mayor que 0.8. Encontrar el valor entero más pequeño de la media de la distribución que asegura esta probabilidad.
* Un fabricante de maquinaria pesada tiene instalados en el campo 3840 generadores de gran tamaño con garantía. Sí la probabilidad de que cualquiera de ellos falle durante el año dado es de 1/1200 determine la probabilidad de que a) 4 generadores fallen durante el año en cuestión, b) que más 1 de un generador falle durante el año en cuestión.
* En un proceso de manufactura, en el cual se producen piezas de vidrio, ocurren defectos o burbujas, ocasionando que la pieza sea indeseable para la venta. Se sabe que en promedio 1 de cada 1000 piezas tiene una o más burbujas. ¿Cuál es la probabilidad de que en una muestra aleatoria de 8000 piezas, menos de 3 de ellas tengan burbujas?
* Se sabe que el número de microorganismos por gramo de una cierta muestra de suelo diluida en agua destilada se distribuye según una variable aleatoria de media 0.08. Si una preparación con un gramo de esta disolución se vuelve turbia, este gramo contiene al menos un microorganismo. Hallar la probabilidad de que una preparación que se ha vuelto turbia contenga:
  + Un solo microorganismo
  + Menos de tres microorganismos
  + Más de dos microorganismos
* En un monte, el número de plantas de romero por círculo de un metro de radio, se distribuye según una variable aleatoria. Se eligen 1o plantas al azar y se mide la distancia a la planta que tiene más próxima. Las distancias observadas fueron (medidas en metros): 0.7, 1.7, 1.2, 0.4, 0.2, 0.5, 0.7, 0.8, 1.5, y 2.1. A partir de estos datos estima los parámetros de interés de la distribución y calcula cuantas plantas es de esperar que habrá en una zona del monte de unos 12500 m2 de superficie.

### Distribución Normal {#prob-normal}

Hasta ahora todas las distribuciones consideradas eran de tipo de discreto. En este punto tratamos la distribución de probabilidad de tipo continuo y más concreta mente la más famosa y utilizada de todas ellas: la distribución de probabilidad Normal. Una variable de tipo continuo es aquella que puede tomar cualquier valor dentro de un rango de valores, es decir, existe un infinito número de valores posibles para la variable aleatoria. Para representar gráficamente una distribución de una variable aleatoria continua se debe construir un subconjunto de clases o intervalos consecutivos para el rango de valores de la variable considerada y considerar el histograma resultante. Cuando consideramos un número muy grande de clases o  intervalos podríamos obtener la curva suavizada que representa la función de densidad de probabilidad de la variable aleatoria.

La función de densidad de probabilidad para una variable aleatoria $X$ que sigue una distribución Normal con parámetros $\mu$ y $\sigma$, denotada por $N(\mu, \sigma^2)$ viene dada por:
\begin{equation} 
  f(x) = \frac{1}{2\pi\sigma^2} exp\left(-\frac{(x - \mu)^2}{2\sigma^2}\right)
  (\#eq:densinormal)
\end{equation}

Los dos parámetros de la distribución son $\mu$ que representa la medida de localización y $\sigma$ la medida de dispersión. Habitualmente los conocemos por sus nombres más habituales como son la media y la desviación típica. En concreto, para una variable que sigue una distribución de probabilidad Normal ($X \sim N(\mu, \sigma^2)$) tenemos que:

\begin{equation} 
  E(X) = \mu
  (\#eq:meannormal)
\end{equation}

\begin{equation} 
  V(X) = \sigma^2
  (\#eq:varnormal)
\end{equation}

\begin{equation} 
  DT(X) = \sigma
  (\#eq:sdnormal)
\end{equation}

Las características más importantes de la distribución Normal:

* Es una distribución simétrica alrededor de la media, $\mu$.
* La media, la mediana y la moda son iguales.
* La distribución Normal queda completamente especificada a partir de los valores de $\mu$ y $\sigma$. Los diferentes valores de la media y la desviación típica desplazan hacia un lado o el otro, o consiguen una distribución más puntiaguda (desviaciones típicas más pequeñas) o más achatada (desviaciones típicas más grandes). 
* El intervalo definido por $\mu - 5 * \sigma, \mu + 5 * \sigma$ tiene probabilidad 1, es decir, la probabilidad de los extremos inferior y superior del rango de valores se puede considerar despreciable.   
* Dada una variable aleatoria Normal con media $\mu$ y desviación típica $\sigma$ y tenemos un escalar $a$ tenemos que:

$$ X \sim N(\mu, \sigma^2) \Longrightarrow aX \sim N(a\mu, a^2\sigma^2)$$
* Dada dos variables aleatorias Normales, $X e Y$, con medias y  desviaciones típicas respectivas: $\mu_1$, $\sigma_1$ y $\mu_2$, $\sigma_2$ y dos escalares $a$ y $b$ tenemos que

$$ X \sim N(\mu_1, \sigma^2_1), Y \sim N(\mu_2, \sigma^2_2) \Longrightarrow aX + bY\sim N(a\mu_1+b\mu_2, a^2\sigma^2_1+b^2\sigma^2_2)$$

Esta última propiedad se puede generalizar para el caso de $m$ variables aleatorias en lugar de 2.

#### Teorema Central de Límite

Si las variables aleatorias $X_1,...X_n$ son una muestra aleatoria de una distribución con media $\mu$ y desviación típica $\sigma$ entonces las variables aleatorias suma ($T = X_1+...+X_n$) y media ($M = (X_1+...+X_n)/n$) tienen distribuciones:
$$ T \sim N\left(n\mu,n\sigma^2\right)$$
$$ M \sim N\left(\mu,\frac{\sigma^2}{n}\right)$$

En la página web http://www.artofstat.com/ se encuentra disponible una aplicación de trabajo de la distribución Binomial (https://istats.shinyapps.io/NormalDist/). Dicha aplicación nos permite calcular tanto probabilidades como cuantiles para un experimento  normal Utiliza dicha aplicación para responder a las cuestiones siguientes: 

* La longitud L (en micras) de ciertas larvas parásitas de moluscos, sigue la distribución Normal, pero de media y desviación típica distinats, según la larva se encuentre en estadio 1 o en estadio 2 de su crecimiento. La media y la desviación típica son: 219 y 20 para el estadio 1, y 241 y 14 para el estadio 2. Para identificar el estadio en que se encuentra la larva, se adopta el criterio siguiente: Si L≤230 pertenece al estadio 1, en otro caso pertenece al estadio 2. Resolver las siguientes cuestiones:
  + Calcular la probabilidad de la presencia del suceso L≤230 para larvas del estadio 1. Idem. para larvas en el esatdio 2.
  + Desde que nace una larva hasta que llega al estadio 3 de su desarrollo (que es fácilmente identificable), trasncurren 12 horas, permaneciendo 8 horas en el estadio 1 y 4 horas en el estadio 2. La observación de una larva cualquiera se hace en un instante que puede considerarse elegido al azar durante estas 12 horas (se desconoce cuando ha nacido la larva). Entonces, si se ha observado que una larva mide L = 225 y, por lo tanto, se considera que pertenece al estadio 1, calcular la probabilidad de equivocarnos al tomar esta decisión.
* Supongamos que el diámetro de las aceitunas rellenas de anchoa varía entre 15 y 17 mm. Debido a al sequía del último año, la producción de aceituna verde se ha distribuido según una normal de media 15.5 mm. y desviación típica 1.2 mm. ¿Qué porcentaje de aceitunas podría ser aprovechado para rellenar de anchoa?
* La duración media de un lavavajillas es de 15 años y su desviación típica 0.5. Sabiendo que la vida útil del lavavajillas se distribuye normalmente, hallar la probabilidad de que al adquirir un lavavajillas, este dure más de 15 años.
* Una normativa europea obliga a que en los envases de yogur no debe haber menos de 120 gr. La máquina dosificadora de una empresa láctea hace los envases de yogur según una distribución normal de desviación típica de 2 gr. y media 122 gr.
  + ¿Qué tanto por ciento de los envases de yogur de esa empresa cumplirá la normativa?
  + ¿Cuál deberá ser la media de la distribución normal con la cual la máquina dosificadora debe hacer los envases para que el 98% de la producción de yogures de la empresa cumpla la normativa?. (La desviación típica sigue siendo de 2 gr.).

### Otras distribuciones

A partir de la distribución de probabilidad Normal se pueden obtener otras distribuciones de probabilidad que resultan de gran utilidad para los procesos de generalización de resultados de un diseño experimental a una población que veremos en la unidad siguiente. 

#### Distribución Chi-cuadrado

Sean n variables aleatorias $X_1,...,X_n$ independientes entre sí cuya distribución de probabilidad es idéntica para todas ellas e igual a una Normal estándar ($N(0,1)$). Si definimos la variable aleatoria suma como:
$$X = X_1 + ... + X_n,$$ entonces decimos que $X$ se distribuye según una distribución Chi-cuadrado con $n$ grados de libertad y la denotamos por $$X \sim \chi^2_n.$$

#### T se Student

Si tenemos dos variables aleatorias independientes $Y$ y $Z$ con distribuciones de probabilidad
$$Z \sim N(0,1); Y \sim \chi^2_n,$$ y consideramos la variable aleatoria $X$ dada por:
$$X = \frac{Z}{\sqrt{Y/n}},$$ entonces decimos que dicha variable sigue una distribución $t$ de Student con $n$ grados de libertad, y se denota por
$$X \sim t_n.$$

#### F de Snedecor

Si tenemos dos variables aleatorias independientes $Y$ y $Z$ con distribuciones de probabilidad
$$Z \sim \chi^2_n; Y \sim \chi^2_m,$$ y consideramos la variable aleatoria $X$ dada por:
$$X = \frac{Z/n}{Y/m},$$ entonces decimos que dicha variable sigue una distribución $F$ de Snedecor con $n$  y $m$ grados de libertad, y se denota por
$$X \sim F_{n,m}.$$

