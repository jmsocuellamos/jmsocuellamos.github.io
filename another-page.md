---
layout: default
---

# Análisis de diseños experimentales de ML

En este documento se puede acceder a los códigos necesarios para la resolución de diferentes casos experimentales relacionados con la modelización mediante modelos lineales. Se pkantea un guión de preguntas para ir analizando los diferentes resultados que iremos obteniendo.

***
# Librerías de R y bibliografía

Cargamos las librerías necesarias para la realización de los análisis estadísticos y presentamos el material bibliográfico necesario.

```
# Cargamos las librerías
library(tidyverse)
library(forcats)
library(broom)
library(reshape2)
library(lmtest)
library(mgcv)
library(MASS)
library(modelr)
```
## Bibliografía

- Daniel, Wayne W. 2005. Biostatistics. Eighth Edition. Wiley.
- Dobson, A. 2002. An Introduction to Generalized Linear Models. CRC Press.
- Faraway, JJ. 2006. Extending the Linear Model with R. Chapman & Hall.
- Krzanowski, Wojtek J. 1998. An Introduction to Statistical Modelling. Arnold.
- Mayoral, A.M., and Morales J. 2001. Modelos Lineales Generalizados. Servicio de publicaciones de la UMH.
- Ugarte, MD, AF Militino, and A Arnholt. 2008. Probability and Statistics with R. CRC Press.
- Wickham, Hadley, and Garrett Grolemund. 2016. R for Data Science. O’Reilly.
- Wood, SN. 2006. Generalized Additive Models. Chapman & Hall.

***
# Caso 1

Se trata de determinar la pérdida de calor sufrida por cierto compuesto cuando es sometido a altas temperaturas. Los datos recogidos son los siguientes:

```
temperatura <- c(460, 450, 440, 430, 420, 410, 450, 440, 430, 420, 410, 400, 420, 410, 400)
perdida <- c(0.3, 0.3, 0.4, 0.4, 0.6, 0.5, 0.5, 0.6, 0.6, 0.6, 0.7, 0.6, 0.6, 0.6, 0.6)
ejer02 <- data.frame(temperatura,perdida)
```

En este caso ambas variables son de tipo numérico y debemos plantear un modelo de regresión lineal simple. Comenzamos con la representación gráfica de los datos. 

> ¿Qué tipo de tendencia se aprecia en el gráfico? ¿Podemos decir que la pérdida de calor disminuye cuado aumenta la temperatura? ¿La tendencia lineal parece apropiada para este tipo de datos?

```
ggplot(ejer02,aes(temperatura,perdida)) + 
  geom_point() + 
  labs(x = "Temperatura", y = "Pérdida de calor") + 
  theme_bw() + 
  geom_smooth(method = "lm", se = FALSE)
```

## Estimación del modelo 

Dado que sólo tenemos una predictora, la selección del modelo consiste unicamente en la valoración de la bondad del ajuste proporcionado por la variable predictora. 

> ¿El modelo obtendio es adecuado para explicar el comportamiento de la pérdida de calor a través de la temperatura? ¿Cuál es la ecuación del modelo ajustado? ¿Cómo interpretamos la interceptiación del modelo? ¿Y la pendiente? Si aumentamos 100 grados ¿qué ocurre con la pérdida de calor?

```
# Ajuste del modelo
ajuste <- lm(perdida ~ temperatura,data = ejer02)
# Inferencia sobre los coeficientes del modelo
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
# Alternativamente podríamos utilizar summary(ajuste)
```

## Diagnóstico del modelo

Realizamos el diagnóstico del modelo para verificar si se cumplen las hipótesis del mdoelo de regresión lineal simple. Comenzamos con el análisis gráfico. 

> ¿Qué podemos decir de los gráficos obtenidos? 

```
# Valores de diagnóstico
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","temperatura",".stdresid")] 
datacomp = melt(diagnosis, id.vars='.stdresid')
# Gráfico residuos, ajustados y regresora
ggplot(datacomp) +
  geom_jitter(aes(value,.stdresid, colour=variable),) + 
  geom_smooth(aes(value,.stdresid, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Residuos") +
  theme_bw()
# Gráfico de normalidad de los residuos
ggplot(fitinf, aes(sample=.stdresid)) + 
  stat_qq() + 
  geom_abline() +
  theme_bw()
```
 
Veamos ahora los tests diagnósticos. ¿Qué podemos decir sobre las hipótesis del modelo? ¿El modelo obtenedo es adecuado para predecir el comportamiento de la pérdida de calor?

```
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

## Predicción del modelo

Pasamos a la predicción del modelo. Planteamos la predicción de la respuesta media para una combinación de valores de la predictora. 

> ¿Cuál es estimación de la pérdida de calor para las temperaturas de 400, 420, 440, y 460? ¿Cuál es el rango de confianza para la predicción en cada una de las temperaturas anteriores? ¿Resulta posible calcular la pérdida de calor para una temperatura de 480? ¿Cuál sería el intervalo de confianza para dicha predicción?

```
# Predicción
newdata <- data.frame(temperatura = seq(min(ejer02$temperatura), max(ejer02$temperatura), .01))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))
# Gráfico de la predicción
ggplot(newdata, aes(x = temperatura, y = fit)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Temperatura", y = "Pérdida de calor", title = "Bandas de predicción") +
  theme_bw()
```

***
# Caso 2

Los datos muestran el porcentaje de calorías totales obtenidas de carbohidratos complejos, para veinte diabéticos dependientes de insulina que habían seguido una dieta alta en carbohidratos durante seis meses. Se consideró que el cumplimiento del régimen estaba relacionado con la edad (en años), *age*, el peso corporal (relativo al peso "ideal" para la altura), *weight*, y otros componentes de la dieta como el porcentaje de proteínas ingeridas. Los datos corresponden con la tabla 6.3 de @Dobson02.

```
# Lectura de datos
ejer03 <- read_csv("https://goo.gl/Grm8xM", col_types = "dddd")
```

En este caso todas las variables son de tipo numérico, y debemos plantear un modelo de regresión lineal múltiple donde tratamos de explicar el comportamiento de `carbohydrate` en función del conjunto de posibles predictoras. Comenzamos con la representación gráfica de los datos a través de gráficos de dispersión individualizados de la respuesta con cada posible predictora. 

> ¿Qué variable o variables parecen influir más, de forma individual, en el porcentaje de calorías totales obtenidas de carbohidratos complejos? ¿Qué tipo de tendencia se observa para cada una de las predictoras? ¿Son tendencia lineales o podríasmos sospechar la presencia de tendencias no lienales?

```
# Gráfico inicial
datacomp = melt(ejer03, id.vars='carbohydrate')
# Gráfico respecto de cada predictora
ggplot(datacomp) +
  geom_jitter(aes(value,carbohydrate, colour=variable),) + 
  geom_smooth(aes(value,carbohydrate, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "carbohydrate") +
  theme_bw()
```

## Estimación del modelo 

En primer lugar procedemos con la selección del modelo. Posteriormente haremos el análisis inferencial de dicho modelo. Partimos del modelo completo con todas las predictoras y utlizamos el procedmiento por pasos para la selección de efectos en el modelo final

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(carbohydrate ~ age + weight + protein, data = ejer03)
# Selección del modelo
ajuste <- step(ajuste)
```

¿Qué varaibles predictoras aparecen el modelo final? Justifica la posible elimianción de alguna o algunas de las varaibles predictoras?

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo final
ajuste <- lm(carbohydrate ~ weight + protein, data = ejer03)
# Inferencia
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
# Tabla ANOVA
anova(ajuste)
```

 ¿Consideras que el modelo obtenido es adecuado para explicar el porcentaje de calorías totales obtenidas de carbohidratos complejos? Justifica tu respuesta. ¿Los efectos presentes en el odelo se pueden considerar como relevantes? ¿Cuál es el modelo ajustado? Interpreta los coeficientes de dicho modelo.

## Diagnóstico del modelo

En este caso nos centramos únicamente en los procedimientos numéricos y tests

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
fitinf
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","protein", "weight", ".stdresid")] 
datacomp = melt(diagnosis, id.vars='.stdresid')
# Tests estadísticos
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

¿Se detecta alguna observación influyente? ¿Que indican los tests de diagnóstico? ¿Podemos utilizar el modelo ajustado para la predcción del porcentaje de de calorías totales obtenidas de carbohidratos complejos?

## Predicción

Construimos un rango de predicción para las proteinas y trabajamos con un peso medio para obtener la gra´fica de predicción. 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
newdata <-expand.grid(protein = round(seq(min(ejer03$protein), max(ejer03$protein), length = 10),0), 
                      weight = round(mean(ejer03$weight),0))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))
# Gráfico para cada valor de weight
ggplot(newdata, aes(x = protein, y = fit)) + 
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Protein", y ="Carbohydrate", title = "Mean Weight") +
  facet_wrap(~ weight, scales="free_x") +  
  theme_bw()
```

¿Cómo interpretamos este gráfico de predicción? ¿Tendría sentido reconvertir la variable peso en un factor ordinal y ajsutar el modelo correspoendiente? Justifica tu respuesta. ¿Qué tipo de modelo tendríamos entonces? ¿Sería más o menos informativo que el modelo con todas las predcitroas numéricas? ¿Qué diferencias en el estudio del modelo deberíamos tener en cuenta? Ajusta un nuevo modelo considerando tres grupos en la variable peso identificados como menos de 100, entre 100 y 120, y más de 120.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Creación de la nueva varaible
ejer03$weight.fac <- cut(ejer03$weight,breaks=c(50,100,120,150))
```

***
# Ejercicio 4 
***

Los datos recogen la respuesta de un pasto de gramíneas y leguminosas a varias cantidades de fertilizante de fósforo (datos de D. F. Sinclair). En concreto se mide el rendimiento total (en kilogramos por hectárea), de pasto y leguminosa juntos, y la cantidad de potasio ($K$) utilizada (en kilogramos por hectárea). Los datos corresponden con la tabla 6.16 de @Dobson02.

## Análisis preliminar 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
ejer04 <- read_csv("https://goo.gl/AOikQU", col_types = "dd")
```

En este caso tenemos las dos variables de tipo  numérico. 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Gráfico inicial
ggplot(ejer04,aes(K,yield)) + 
  geom_point() + 
  labs(x = "K", y = "yield") + 
  theme_bw() + 
  geom_smooth(method = "lm", se = FALSE)
```

¿Cómo afecta la cantidad de potasio en el rendimiento total del pasto? ¿Podemos considear la relación entre ambas variables de tipo lineal? 

## Estimación del modelo 

Construimos y evaluamos la calidad del ajuste obtneido

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(yield ~ K,data = ejer04)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
```

¿Podemos decir que la cantidad de potasio explica el rendimiento total del pasto? Justifica tu respuesta. ¿Que relación se ha establecido entre ambas varaibles? ¿Cúal sería el rendimieto del pasto si no utilizamos el potasio? ¿Cómo aumenta el rendimiento por cada aumentar en 10 kilogramos por hectárea la cantidad de potasio?

## Diagnóstico del modelo 

Realizamos el diagnóstico del modelo tanto mediante procedimientos gráficos como los tests de diagnóstico.

### Análisis gráfico

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","K",".stdresid")] 
datacomp = melt(diagnosis, id.vars='.stdresid')
# Gráfico residuos, ajustados y regresora
ggplot(datacomp) +
  geom_jitter(aes(value,.stdresid, colour=variable),) + 
  geom_smooth(aes(value,.stdresid, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Residuos") +
  theme_bw()
# Gráfico de normalidad de los residuos
ggplot(fitinf, aes(sample=.stdresid)) + 
  stat_qq() + 
  geom_abline() +
  theme_bw()
```

¿Cómo interpretamos los gráficos de diagnóstico? 

Veamos los tests diagnósticos

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

¿Encontramos alguna dificultad con las hipótesis del modelo? ¿Podemos considerar que le modelo obtneido es adecuado para explicar la relación entre la cantidad de potasio y el rendimiento del pasto?

## Predicción

Calculamos un grid de valores de potasio y obtenemos el correspondiente modelo de predicción

```{r ,error=FALSE,warning=FALSE,message=FALSE}
newdata <- data.frame(K = seq(min(ejer04$K), max(ejer04$K), .5))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))
# Gráfico de la predicción
ggplot(newdata, aes(x = K, y = fit)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "K", y = "Yield", title = "Bandas de predicción") +
  theme_bw()
```

¿Cuáles son la predicciones del rendimiento del pasto para los valores de potasio de 20, 30, y 40?

***
# Ejercicio 5 
***

Es bien sabido que la concentración de colesterol en el suero sanguíneo aumenta con la edad, pero es menos claro si el nivel de colesterol también está asociado con el peso corporal. Los datos muestran para una treinta de mujeres el colesterol sérico (milimoles por litro), la edad (años) y el índice de masa corporal (peso dividido por la altura al cuadrado, donde el peso se midió en kilogramos y la altura en metros). Los datos corresponden con la tabla 6.17 de @Dobson02.

## Análisis preliminar 

Lectura de datos
```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
ejer05 <- read_csv("https://goo.gl/EKXWRc", col_types = "ddd")
```

Debemos plantear un modelo de regresión lineal múltiple donde tratamoes de explicar la concentración de colesterol en el suero sanguñineo en función de la edad y del peso corporal. Realizamos gráficos de disperción individual para cada posible predictora.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
datacomp = melt(ejer05, id.vars='chol')
# Gráfico respecto de cada predictora
ggplot(datacomp) +
  geom_jitter(aes(value,chol, colour=variable),) + 
  geom_smooth(aes(value,chol, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Chol") +
  theme_bw()
```

¿Qué conclusiones podemos extraer de este gráfico? ¿Las dos predcitoras pueden tener efecto sobre la concentración de colesterol?

## Estimación del modelo 

Procedemos con la estimación del mejor modelo realizando un proceso de selección de variables.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(chol ~ age + bmi , data = ejer05)
# Selección del modelo
ajuste <- step(ajuste)
# Inferencia
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
```

¿Qué predictoras contiene el modelo resultante del proceso de selección de efectos? ¿El modelo obtenido es adecuado para explicar la concentración de colesterol? Justifica tu respuesta. ¿Cuál es la ecuación de modelo ajsutado? ¿Cómo aumneta la concentración del colesterol para dos mujeres con un diferencia de edad de cinco años? ¿y de un volumen de masa corporal de de cinco unidades? ¿Tiene sentido una interceptación negativa para este modelo? ¿Cuál sería la posible explicación? Vamos a probar a ajustar un modelo sin interceptación y compararemos los resultados con el modelo con interceptación.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo sin interceptación
ajuste2 <- lm(chol ~ age + bmi - 1 , data = ejer05)
# Selección del modelo
ajuste2 <- step(ajuste2)
# Inferencia
tidy(ajuste2)
# Bondad del ajuste
glance(ajuste2)
# Comparación de ambos modelos
anova(ajuste,ajuste2)
ajuste <- ajuste2
```

¿Resulta más adecuado este modelo? Justifica tu respuesta y responde a las preguntas sobre el modelo planteadas anteriormente.

## Diagnóstico del modelo 

Realizamos el diagnóstico del modelo tanto mediante procedimientos gráficos como los tests de diagnóstico.

### Análisis gráfico

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","age", "bmi", ".stdresid")] 
datacomp = melt(diagnosis, id.vars='.stdresid')
# Gráfico residuos, ajustados y regresora
ggplot(datacomp) +
  geom_jitter(aes(value,.stdresid, colour=variable),) + 
  geom_smooth(aes(value,.stdresid, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Residuos") +
  theme_bw()
# Gráfico de normalidad de los residuos
ggplot(fitinf, aes(sample=.stdresid)) + 
  stat_qq() + 
  geom_abline() +
  theme_bw()
```

¿Que opinas sobre los gráficos de diagnóstico? ¿Podríamos considerar que existen observaciones influyentes? Realizamos el análisis correspondiente.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Análisis de influencia
fitinf$.cooksd > 1
```

¿Que conclusión extraemos de este análisis? ¿Debemos considerar la elimnación de alguna observación? Justifica tu respuesta.

### Tests diagnósticos

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

¿Podemos considerar como adecuado el modelo obtenido? Justifica tu respuesta.

## Predicción

Construimos un grid de valores para la edad y trabajamos con el valor mínimo, máximo y la media del índice de masa corporal para representar la predicción asociada.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Predicción
newdata <-expand.grid(age = round(seq(min(ejer05$age), max(ejer05$age), length = 10),0), 
                      bmi = round(c(min(ejer05$bmi),mean(ejer05$bmi),max(ejer05$bmi)),0))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))
# Gráfico para cada valor 
ggplot(newdata, aes(x = age, y = fit)) + 
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Age", y ="Cholesterol", title = "BMI") +
  facet_wrap(~ bmi, scales="free_x") +  
  theme_bw()
```

¿Cómo podemos interpretar los modelos de predicción obtenidos? ¿Cuál es la diferencia de predicción para una edad de 50 años?

***
# Ejercicio 6 
***

En un estudio sobre las diferentes clases de queso cheddar que se fabrican en LaTrobe Valley de Victoria, Australia, se analizaron muestras de queso por su composición química: concentración de ácido acético (escala logarítmica); concentración de sulfuro de hidrógeno (escala logarítmica); y la concentración de ácido láctico. Por otro lado, se paso una muestra de cada uno de ellos a un conjunto de catadores y se registro la puntuación obtenida por cada uno de ellos. Estamos interesados en relacionar la puntuación final de los catadores con los resultados del análisis químico.

## Análisis preliminar 

Nos encontramos ante un modelo de regresión lineal múltiple (todas las variables son de tipo numérico) donde tratamos de explicar la puntuación final de cada queso en función de la concentración de ácido acético, concentración de sulfuro de hidrógeno, y la concentración de ácido láctico. cargamos los datos y realizamos los gráficos de dispersión individuales.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
ejer06 <- read_csv("https://goo.gl/V4lDNs", col_types = "dddd")
# Gráfico inicial
datacomp = melt(ejer06, id.vars='Taste')
# Gráfico respecto de cad predictora
ggplot(datacomp) +
  geom_jitter(aes(value,Taste, colour=variable),) + 
  geom_smooth(aes(value,Taste, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Taste") +
  theme_bw()
```

¿Cómo podeos interpretar los gráficos obtenidos? ¿tenemos alguna indicación de las varaibles ue pueden resultar más relevantes para explicar la puntuación final obtenida?

## Estimación del modelo

Construímos el mejor modelo posible en función de las varaibles predictoras consideradas

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(Taste ~ Acetic + H2S + Lactic, data = ejer06)
# Selección del modelo
ajuste <- step(ajuste)
# Inferencia
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
```

¿Cuáles son las predictoras involucrdas en el modelo final? ¿Cuál es la capacidad explicativa de dicho modelo? ¿Cuáles son los coeficientes del modelo obtenido? ¿Tienen sentido o deberíamos modificar nuestro modelo? ¿Cuál es tu propuesta de mejora?

Construimos el nuevo modelo y lo comparamos con el anterior.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste2 <- lm(Taste ~ Acetic + H2S + Lactic - 1, data = ejer06)
# Selección del modelo
ajuste2 <- step(ajuste2)
# Inferencia
tidy(ajuste2)
# Bondad del ajuste
glance(ajuste2)
```

El modelo obtenido ¿podemos considerarlo mejor que el anterior? ¿Cómo interpretamos el comportamiento de cada predictora en este nuevo modelo? 

## Diagnóstico del modelo

Procedemos con el diganóstico del último modelo ajustado. Comenzmaos con el análisis gráfico y de influencia

```{r ,error=FALSE,warning=FALSE,message=FALSE}
ajuste <- ajuste2
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","H2S", "Lactic","Acetic", ".stdresid")] 
datacomp = melt(diagnosis, id.vars='.stdresid')
# Gráfico residuos, ajustados y regresora
ggplot(datacomp) +
  geom_jitter(aes(value,.stdresid, colour=variable),) + 
  geom_smooth(aes(value,.stdresid, colour=variable), method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Residuos") +
  theme_bw()
# Gráfico de normalidad de los residuos
ggplot(fitinf, aes(sample=.stdresid)) + 
  stat_qq() + 
  geom_abline() +
  theme_bw()
# Análisis de influencia
fitinf$.cooksd > 1
```

¿Qué podemos concluir del análisis realizado? Realizamos los tests diagnósticos

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

¿Es válido el modelo construido? Justifica tu respuesta

## Predicción

Construimos un grid de valores para el ácido láctico y tomamos los valores medios para las otras dos predictoras

```{r ,error=FALSE,warning=FALSE,message=FALSE}
newdata <-expand.grid(H2S = mean(ejer06$H2S), 
                      Lactic = seq(min(ejer06$Lactic), max(ejer06$Lactic), by = 0.2),
                      Acetic = mean(ejer06$Acetic))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))
# Gráfico para cada valor de weight
ggplot(newdata, aes(x = Lactic, y = fit)) + 
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Lactic", y ="Taste") +
  theme_bw()
```

¿Cuál es la predicción de la puntuación para para los valores medios de las tres predictoras? ¿Qué varaible crees que influirá más en la predicción final? ¿Cómo podríamos comporbar este hecho?


## Ejercicio 7

En una investigación para la reducción de peso corporal se registran los pesos, en kilogramos, de veinte hombres antes y después de la participación en un programa de "pérdida de cintura". Los datos corresponden con la tabla 2.8 de @Dobson02. Para analizar adecuadamente estos datos junytamos las columnas correspondientes a antes y después en una columna con un factor que indica a cuando corresponde la observación. 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
previo <- read_csv("https://goo.gl/jWGurk", col_types = "idd")
# Para construir modelos trasformamos los datos originales en la forma habitual  
ejer07 <- previo %>% gather(`before`,`after`,key = "Time", value = "Weight")
```

Representamos los datos en la forma habitual

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Gráfico de cajas
ggplot(ejer07,aes(x = Time, y = Weight)) + 
  geom_boxplot() + 
  theme_bw()
```

¿Qué conclusión podemos extraer de este gráfico? ¿Qué resultado esperas que porpocione el modelo que puedas ajustar?

## Estimación del modelo

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(Weight ~ Time, data = ejer07)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

Atendiendo a la tabla ANOVA resultante para este modelo ¿Cuál es la conclusión del moelo obtenido? ¿Es necesario seguir trabajando sobre este modelo? Justifica tu respuesta.

***
# Ejercicio 8 
***

Se realiza una investigación para conocer los niveles de fosfato inorgánico en plasma (mg / dl) una hora después de una prueba de tolerancia a la glucosa estándar para sujetos obesos, con o sin hiperinsulinemia (H-O, y N-O respectivamente), y controles (C). Los datos corresponden con la tabla 6.18 de @Dobson02.

## Análisis preliminar

En este caso tenemos un modelo ANOVA de un vía (grupo de pacientes) para explicar el nivel de fósfato inorgánico en plasma. Cargamos los datos y rerpesentamos de la forma habitual.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
ejer08 <- read_csv("https://goo.gl/3L4EtK", col_types = "cd")
# Gráfico de cajas
ggplot(ejer08,aes(x = Group, y = phosphate)) + 
  geom_boxplot() + 
  theme_bw()
```

¿Qué conclusión podemos extraer de este gráfico? ¿Consideras adecuadoplantear un modelo para tratar de conocer si el nivel medio de fósfato es diferente entre los grupos considerados?

## Estimación del modelo

Construimos el modelo correspondiente a esta situación

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(phosphate ~ Group, data = ejer08)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

¿Qué conclusión podemos extraer de la tbala ANOVA obtenida? ¿Cuál es la media estimada del nivel de fósfato para cada grupo? ¿Podemos considerar que las medias en todos los grupos son diferentes? Realizamos el test de comparación múltiples para contestar a esta pregunta.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Comparaciones múltiples
TukeyHSD(aov(phosphate ~ Group, data = ejer08))
```
¿Qué conclusiones podemos extraer del análisis realizado? ¿tendrá interés el diseño experimnetal llevado a cabo? Justifica tu respuesta.

## Diagnóstico del modelo

En este caso hemos de tener en cuenta que al tratarse de un modelo ANOVA la verificación de las hipótesis son un poco más laboriosas. Nos centramos unicamente en los tests diagnósticos.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico
fitinf <- fortify(ajuste)
# Varianza constante
bartlett.test(phosphate ~ Group, data = ejer08)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Group == "C"])
shapiro.test(fitinf$.stdresid[fitinf$Group == "H-O"])
shapiro.test(fitinf$.stdresid[fitinf$Group == "N-O"])
```

¿Quñe podemos concluir del proceso de diagnóstico del modelo? ¿Podemos utilizar el modelo construido apra estimar la media del nivel de fósfato en función del grupo al que pertenecen los sujetos?

## Predicción

En esta caso la predicción se basa unicamente en el cálculo de los intervalos de confianza de la media para cada uno de los grupos considerados.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Predicción
# Secuencia de valores de predicción
newdata <- data.frame(Group = unique(ejer08$Group))
# Predicción para la media de la respuesta
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence")) # Opción interval = "confidence" 
# Gráfico resultante (predicción y bandas de predicción). Ordenamos las clases de acuerdo al valor de fit
ggplot(newdata, aes(x = fct_reorder(Group,fit), y= fit)) + 
    geom_errorbar(aes(ymin=lwr, ymax=upr), width=.1) +
    geom_line() +
    geom_point() +
    labs(x = "Grupo", y = "Nivel de fósfato") +
    coord_flip() +
    theme_bw()
```

¿Qué podemos decir de los intervalos de predicción obtenidos? ¿Podríamos utilizar esta predicción como diagnóstico médico? Justifica tu respuesta.

***
# Ejercicio 9 
***

Se lleva a cabo un estudio sobre el contenido promedio de grasa (en porcentaje) en la leche del ganado de cinco razas distintas canadienses. Para ello se consideran veinte ejemplares de pura raza (diez de dos años y diez maduras de más de cuatro años de cada una de cinco razas. 

## Análisis preliminar

Nos encontramos ante un diseño experimental con dos posibles varaibles predictoras de tipo categórico. Debmos plantear un modelo ANOVA con dos vías de clasificación. Recordemos que en estas situaciones el estudio principal se centra en primer lugar en determinar si existe interacción entre los fatores considerados, es decir, si la media del contenido de grasa en la leche depende simúltaneamente de la raza de la oveja y de su edad. Cargamos los datos y relizamos el gráfico de cajas conjunto y el de interacción de las medias de cada combinación de ambos factores.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
ejer09 <- read_csv("https://goo.gl/J2ZKWK", col_types = "dcc")
# Diagrama de cajas
ggplot(ejer09,aes(x = Bread, y = Butterfat, color = Age)) + 
  geom_boxplot() + 
  theme_bw()
# Gráfico de interacción de medias
ggplot(ejer09, aes(x = Bread, y = Butterfat, group = Age, color = Age)) +
  stat_summary(fun.y = mean, geom = "point") +
  stat_summary(fun.y = mean, geom = "line") +
  theme_bw()
```

¿Qué conclusiones podemos extraer del gráfico de cajas conjunto? ¿Qué consideras más relevante del gráfico obtenido? ¿Qué podemos decir a la vista del gráfico de interacción? ¿Qué tipo de modelo parecería más adecuado a la vista de estos datos?

## Estimación del modelo

Procedemos con la estimación del modelo completo considerando la posible interacción entre los factores, a pesar de que el gráfico de interacción parece indicar que dicho efecto es irrelevante.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Ajuste del modelo
ajuste <- lm(Butterfat ~ Bread*Age, data = ejer09)
# Selección del modelo
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

¿Qué efectos son relevantes para explicar el contenido promedio de grasa (en porcentaje) en la leche del ganado? Justifica tu respuesta. ¿Cúal es el modelo estimado? ¿Consideras que se ha finalizado con el porceso de estimación del modelo? ¿Qué otra información deberíamos extaer?

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Comparaciones múltiples
TukeyHSD(aov(Butterfat ~ Bread, data = ejer09))
```

¿Qué concluiones podemos extraer del análiis realizado? ¿Cómo podrías representar la información obtenida para una mejor visualización de los resultados?

## Diagnóstico del modelo

Comenzamos con el diagnóstico del modelo ajustado.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico
fitinf <- fortify(ajuste)
# Varianza constante
bartlett.test(Butterfat ~ Bread, data = ejer09)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Ayrshire"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Canadian"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Guernsey"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Holstein-Fresian"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Jersey"])
```

¿Qué conclusiones extraemos del diagnóstico realizado? ¿Cómo propones que deberíamos modificar nuestro modelo? 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Transformación de boxcox
boxcox(ajuste) 
```

¿Qué transformación debemos reazlizar sobre la varaible respuesta? Transformamos la varaible y ajustamos de nuevo el modelo completo.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Creamos la variable transformada
ejer09 <- ejer09 %>% mutate(Butterfatinv = 1/Butterfat)
#####################################
# Realizamos el ajuste de nuevo
ajuste <- lm(Butterfatinv ~ Bread*Age, data = ejer09)
# Selección del modelo
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

¿Qué modelo resulta tras la transfroamción de la respuesta? ¿Cúales son las ecuaciones del modelo ajustado? ¿Cómo interpretamos los coeficientes de este modelo? A la vista de la tabla ANOVA ¿cúal debería ser el modelo a utilizar? Realizamos el diagnóstico para este nuevo modelo.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Realizamos el ajuste de nuevo
ajuste <- lm(Butterfatinv ~ Bread, data = ejer09)
# Diagnóstico
fitinf <- fortify(ajuste)
# Varianza constante
bartlett.test(Butterfatinv ~ Bread, data = ejer09)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Ayrshire"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Canadian"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Guernsey"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Holstein-Fresian"])
shapiro.test(fitinf$.stdresid[fitinf$Bread == "Jersey"])
```

¿Es adecuado el modelo obtenido? Justifica tu respuesta.

## Predicción 

Construímos los intervalos de confianza para la predicción correspondiente al modelo ajustado

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Predicción
# Secuencia de valores de predicción
newdata <- data.frame(Bread = c("Ayrshire","Canadian","Guernsey","Holstein-Fresian","Jersey"))
# Predicción para la media de la respuesta
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence")) # Opción interval = "confidence" 
# Calculamos valore de predicción en la escala original
newdata$fit <- 1/newdata$fit
newdata$lwr <- 1/newdata$lwr
newdata$upr <- 1/newdata$upr
#Gráfico de predicción
ggplot(newdata, aes(x = fct_reorder(Bread,fit), y= fit)) + 
    geom_errorbar(aes(ymin=lwr, ymax=upr), width=.1) +
    geom_line() +
    geom_point() +
    coord_flip() +
    labs(y = "Contenido promedio de grasa", x = "Raza") +
    theme_bw()
```

¿Qué podemos conlcluir a la vista del gráfico de predicción? ¿Sería posible clasificar una oveja sin mirarla conociendo el contenido promedio de grasa de su leche? Justifica como procederías para contestar a esta cuestión.


***
# Ejercicio 10
***

Los datos que se presentan son los pesos al nacer (en gramos) y las edades gestacionales estimadas (en semanas) de 12 bebés hombres y mujeres nacidos en un determinado hospital. Las edades medias son casi las mismas para ambos sexos. Se está interesado en estimar el peso de los bebes a partir de su sexo y su edad gestacional.

## Análisis preliminar

Tenemos una variable predictora de tipo numérico (edad gestacional) y otra de tipo categórico (sexo). Consideramos un modelo ANCOVA con un regresor y un factor. Como el sexo viene codificado como 1 (Boy) y 2 (Girl), construímos el factro asociado antes de la representación gtáfica de los datos.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
previo <- read_csv("https://goo.gl/B3yoLJ", col_types = "ddc")
# Recodificación del factor
ejer10 <- previo %>% mutate(sex=fct_recode(sex,"Boy"="1","Girl"="2"))
```

Para la representación gráfica de los datos consideramos las tres posibilidades de modelización para estos datos:

- Una única recta de regresión entre peso y edad. Sse considera irrelevante la asociación entre el sexo y edad, y el posible efecto del sexo del bebé en su peso final. 
- Dos rectas de regresión paralelas (una por cada sexo). Se considera irrelevante la asociación entre el sexo y edad, pero se considera un posible efecto del sexo del bebé en su peso final. 
- Dos rectas distintas (una por cada sexo). Se considera relevante la asociación entre el sexo y edad para el peso final del bebé. 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Representación gráfica de los modelos de regresión
# Modelo con una única recta
M0 <- lm(weight ~ age,data = ejer10)
# M1: modelo con rectas paralelas
M1 <- lm(weight ~ sex + age,data = ejer10)
# M2: modelo con rectas no paralelas
M2 <- lm(weight ~ sex * age,data = ejer10)
# grid de valores para construir los modelos
grid <- ejer10 %>% data_grid(sex,age) %>% gather_predictions(M0,M1,M2)
# Gráfico
ggplot(ejer10,aes(age,weight,colour = sex)) + 
  geom_point() + 
  geom_line(data = grid, aes(y = pred)) +
  facet_wrap(~ model) +
  labs(x = "Edad Gestacional", y = "Peso") + 
  theme_bw()
```

¿Hay evidencias de cuál puede ser el mejor modelo de los tres planteados? Justifica tu respuesta

## Estimación del modelo

Comparamos los modelos construidos en el análisis preliminar para decidirnos entre uno de ellos. Para ello partirmos del modelo más complejo y realizamos el proceso de selección de efectos. 

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Selección del modelo
ajuste <- M2
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Coeficientes del modelo
tidy(ajuste)
```

¿Qué efectos están presentes en el modelo según el porceso de selección automático? Si comparamos este resultado con la tabla ANOVA resultante ¿el modelo seleccionado sería el mismo? ¿Qué opción te parece más adecuada? ¿qué implicaciones experimnetales tienen ambos modelos? Si consideramos como válido el porporcionado por el procedimiento automático de selección ¿Cómo interpretamos los coeficentes de dicho modelo? ¿Podríamos eliminar la interceptación de este modelo?

## Diagnóstico del modelo

Ralizamos el diagnóstico del modelo que contiene los efectos principales de edad y sexo del bebé. Realizasmos tanto los gráficos de residuos como los tests de diagnóstico.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Residuos versus ajustados
ggplot(fitinf, aes(x = .fitted, y =.stdresid)) + 
  geom_point() +
  facet_wrap(~ sex, scales="free_x") +  
  theme_bw()
# Residuos versus predictora
ggplot(fitinf, aes(x = age, y =.stdresid)) + 
  geom_point() +
  facet_wrap(~ sex, scales="free_x") +  
  theme_bw()
# Tests para verificar las hipótesis
# Varianza constante
bartlett.test(.stdresid ~ sex, data = fitinf)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$sex == "Boy"])
shapiro.test(fitinf$.stdresid[fitinf$sex == "Girl"])
```

¿Se detecta algún problema con las hipótesis del modelo? ¿Cómo afecta eso al modelo construido?

## Predicción

Creasmo un grid de valores para la edad gestacional y construimos los modleos de predicción asociados con cada nivel del factor.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
newdata <- data.frame(age = rep(seq(min(ejer10$age), max(ejer10$age), .01),each=2), 
                      sex = factor(c("Boy", "Girl")))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))
# Gráfico de la predicción
ggplot(newdata, aes(x = age, y = fit, color = sex)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr, fill = sex), alpha = 1/5) +
  labs(x = "Edad gestacional", y = "Peso al nacer", title = "Bandas de predicción") +
  theme_bw()
```

¿Qué conclusiones podemos extraer de los modelos de predicción construidos? ¿Qué diferencia de peso estimamos para una edad gestacional de 38 semanas entre ambos sexos? ¿Podemos tomar este modelo de predicción como válido para futuros embarazos? ¿Cómo procederíamos y que dificultades nos podríamos encontrar?

***
# Ejercicio 11
***

Se lleva a cabo una investigación sobre diversas malformaciones del sistema nervioso central registradas en nacidos vivos en Gales del Sur, Reino Unido. El estudio fue diseñado para determinar el efecto de la dureza del agua sobre la incidencia de tales malformaciones. La información registrada son: NoCNS = recuento de nacimientos sin problema CNS; An = conteo de nacimientos de Anencephalus; Sp = conteo de nacimientos de espina bífida; Otro = recuento de otros nacimientos del SNC; Agua = endurecimiento del agua; Trabajo = un factor con niveles Manual o No manual en función del tipo de trabajo realizado por los padres.


## Análisis preliminar

Dadoq ue estamos interesados en establecer un modelo para el número total de malformaciones, deberíamos construir en primer lugar un avaraible que contenga dichos valores como la suma de todos los tipos de malformaciones registradas. Esta nueva varaible se utilizará como respuesta en el modelo porpuesto. Como posibles variables predictoras tenemos el tipo de trabajo de los padres y la dureza del agua. Planteamos un modelo ANCOVA con un regresor y un factor. No consideramos el distrito origen de los sujetos para este modelo. Procedemos con el caso anterior para reperesntar las diferentes opciones de modelización para estos datos.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Lectura de datos
previo <- read_csv("https://goo.gl/bNOSxt", col_types = "cdddddc")
# Calculamos el número total de malformaciones para utilizarla como variable
ejer11 <- previo %>% mutate(CNS=An+Sp+Other)
# Variables predictoras: Water, Work; Respuesta: CNS
###################################################################
# Representación gráfica de los modelos de regresión
# Modelo con una única recta
M0 <- lm(CNS ~ Water,data = ejer11)
# M1: modelo con rectas paralelas
M1 <- lm(CNS ~ Work + Water,data = ejer11)
# M2: modelo con rectas no paralelas
M2 <- lm(CNS ~ Work * Water,data = ejer11)
# grid de valores para construir los modelos
grid <- ejer11 %>% data_grid(Work,Water) %>% gather_predictions(M0,M1,M2)
# Gráfico
ggplot(ejer11,aes(Water,CNS,colour = Work)) + 
  geom_point() + 
  geom_line(data = grid, aes(y = pred)) +
  facet_wrap(~ model) +
  labs(x = "Dureza del agua", y = "Número malformaciones") + 
  theme_bw()
```

¿Qué información proporcionan los gráficos obtenidos de cara a la elección del posible modelo final sobre estos datos?

## Estimación del modelo

Seleccionamos el mejor modelo para estos datos partiendo del modelo más complejo (ineteracción entre dureza del agua y tipo de trabajo)

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Selección del modelo
ajuste <- M2
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Coeficientes del modelo
tidy(ajuste)
```

¿Qué tipo de modelo se ha estimado finalmente? ¿Cuales son las rectas de regresión estimadas? ¿Cómo interpretamos los coeficientes de dichos modelos? ¿Qué sentido tiene la interceptación del modelo?

## Diagnóstico del modelo

Realizamos el diagnóstico gráfico y basado en los tests de diagnóstico para este tipo de modelos:

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Residuos versus ajustados
ggplot(fitinf, aes(x = .fitted, y =.stdresid)) + 
  geom_point() +
  facet_wrap(~ Work, scales="free_x") +  
  theme_bw()
# Residuos versus predictora
ggplot(fitinf, aes(x = Water, y =.stdresid)) + 
  geom_point() +
  facet_wrap(~ Work, scales="free_x") +  
  theme_bw()
# Tests para verificar las hipótesis
# Varianza constante
bartlett.test(.stdresid ~ Work, data = fitinf)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Work == "Manual"])
shapiro.test(fitinf$.stdresid[fitinf$Work == "NonManual"])
```

¿Qué indica el gráfico de residuos versus ajustados para cada nivel del factor? ¿Cómo interpretamos los resultados de los tests de diagnóstico? ¿Cómo procedemos a partir de ahora?


Probamos con las tranaformaciones de Box-Cox
```{r ,error=FALSE,warning=FALSE,message=FALSE}
boxcox(ajuste) 
```

¿Existe una transformación adecuada para este modelo? Trasnformamos la respuesta y reajustamos el modelo.

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Creamos la variable transformada
ejer11 <- ejer11 %>% mutate(CNSlog = log(CNS))
#################################################
# Nuevo ajuste
ajuste <- lm(CNSlog ~ Work * Water,data = ejer11)
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Coeficientes del modelo
tidy(ajuste)
```

¿Qué modelo resulta del nuevo proceso de estimación? ¿Cómo interpretamos los coeficientes de dicho modelo?

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Nuevo diagnóstico
fitinf <- fortify(ajuste)
# Varianza constante
bartlett.test(.stdresid ~ Work, data = fitinf)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Work == "Manual"])
shapiro.test(fitinf$.stdresid[fitinf$Work == "NonManual"])
```

¿Qué ocurre con las hipótesis de este modelo?

## Predicción

Construimos los intervalos de confianza para la predicción correspondiente a cada uno de los niveles del factor. Por el momneto seguimos trabajando con la variable transformada

```{r ,error=FALSE,warning=FALSE,message=FALSE}
# Secuencia de valores de predicción
newdata <- data.frame(Work = unique(ejer11$Work))
# Predicción para la media de la respuesta
newdata <- data.frame(newdata, predict(ajuste, newdata, interval="confidence"))  
# Gráfico resultante (predicción y bandas de predicción). Ordenamos las clases de acuerdo al valor de fit
ggplot(newdata, aes(x = fct_reorder(Work,fit), y= fit)) + 
    geom_errorbar(aes(ymin=lwr, ymax=upr), width=.1) +
    geom_line() +
    geom_point() +
    labs(x = "Tipo de trabajo", y = "Logaritmo número de malformaciones") +
    coord_flip() +
    theme_bw()
```

¿Cómo onbtendríamos los valores de predicción en la escala original del número de malformaciones? 

***
# Ejercicio 12
***

Se ha realizado un estudio para establecer la calidad de los vinos de la variedad Pino Noir en función de un conjunto de características analizadas. Las características analizadas son claridad, aroma, cuerpo, olor y matiz. Para medir la calidad se organiza una cata ciega a un conjunto de expertos y se calcula la puntuación final de cada vino a partir de la información de todos ellos. Además se registra la región de procedencia del vino por si puede influir en la calidad del vino.

## Análisis preliminar

En este caso tenemos un modelo ANCOVA con un factor (región de procedencia) y cinco predictoras numéricas, para tratar de explicar la calidad del vino. En este caso no realizamos el análisis gráfico y pasamos directamente a la estimación del modelo. ¿Qué tipo de gráfico podríamos construir para represengtar la información de este modelo? ¿Nos paortaría información a la hora de plantear el posible modelo estadístico?

## Estimación del modelo

Planteamos el modelo completo y realizamos el correspondiente proceso de selección automática de efectos. ¿Qué modelo resulta del porceso de estimación? ¿Cómo podemos interpretar los diferentes efecto de interacción que aparecen en él? 

```
# Lectura de datos
ejer12 <- read_csv("https://goo.gl/OX9wgM", col_types = "ddddddc")
# Gráfico del modelo díficil de realizar
# Pasamos al ajuste directamente
ajuste <- lm(calidad ~ region * (claridad + aroma + cuerpo + olor + matiz),data = ejer12)
ajuste <- step(ajuste)
tidy(ajuste)
```

## Diagmóstico del modelo

¿Cómo deberíamos proceder para realizar el diagnóstico de este modelo? 

## Predicción

En este caso el gráfico de predicción resulta muy complejo debido a la gran cantidad de efecto presentes en el modelo. Nos podemos limitar a obtner una tabla de rpedcciones para valores específicos de las predictoras. ¿Qué valores de las predictoras propones para este proceso? ¿Cómo realizaríamos este proceso? La opción más adecuada en esta situación es el establecimiento de un análisis de escenarios para resolver este problema. En un análisi de escenarios propone tres valores de la variable numérica (uno bajo, otro intermedio y otro alto) y predice los valores correspondientes de la respuesta. ¿Como actuaríamos en este problema?

# Bibliografía
