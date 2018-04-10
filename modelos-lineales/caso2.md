---
layout: default
---

***
# Caso 2
***

Los datos muestran el porcentaje de calorías totales obtenidas de carbohidratos complejos, para veinte diabéticos dependientes de insulina que habían seguido una dieta alta en carbohidratos durante seis meses. Se consideró que el cumplimiento del régimen estaba relacionado con la edad (en años), *age*, el peso corporal (relativo al peso "ideal" para la altura), *weight*, y otros componentes de la dieta como el porcentaje de proteínas ingeridas. Los datos corresponden con la tabla 6.3 de @Dobson02.

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
# Lectura de datos
ejer03 <- read_csv("https://goo.gl/Grm8xM", col_types = "dddd")
```

En este caso todas las variables son de tipo numérico, y debemos plantear un modelo de regresión lineal múltiple donde tratamos de explicar el comportamiento de `carbohydrate` en función del conjunto de posibles predictoras. Comenzamos con la representación gráfica de los datos a través de gráficos de dispersión individualizados de la respuesta con cada posible predictora. 

**¿Qué variable o variables parecen influir más, de forma individual, en el porcentaje de calorías totales obtenidas de carbohidratos complejos? ¿Qué tipo de tendencia se observa para cada una de las predictoras? ¿Son tendencia lineales o podríamos sospechar la presencia de tendencias no lineales?**

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
# Gráfico inicial
datacomp = melt(ejer03, id.vars='carbohydrate')
# Gráfico respecto de cada predictora
ggplot(datacomp) +
  geom_jitter(aes(value,carbohydrate, colour=variable),) + 
  geom_smooth(aes(value,carbohydrate, colour=variable), 
              method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "carbohydrate") +
  theme_bw()
```

## Estimación del modelo 

En primer lugar procedemos con la selección del modelo. Posteriormente haremos el análisis inferencial de dicho modelo. Partimos del modelo completo con todas las predictoras y utilizamos el procedimiento por pasos para la selección de efectos en el modelo final. 

**¿Qué variables predictoras aparecen el modelo final? Justifica la posible eliminación de alguna o algunas de las variables predictoras?**

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
# Ajuste del modelo
ajuste <- lm(carbohydrate ~ age + weight + protein, 
             data = ejer03)
# Selección del modelo
ajuste <- step(ajuste)
```

**¿Consideras que el modelo obtenido es adecuado para explicar el porcentaje de calorías totales obtenidas de carbohidratos complejos? Justifica tu respuesta. ¿Los efectos presentes en el modelo se pueden considerar como relevantes? ¿Cuál es el modelo ajustado? Interpreta los coeficientes de dicho modelo.**

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
# Ajuste del modelo final
ajuste <- lm(carbohydrate ~ weight + protein, 
             data = ejer03)
# Inferencia
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
# Tabla ANOVA
anova(ajuste)
```

## Diagnóstico del modelo

En este caso nos centramos únicamente en los procedimientos numéricos y tests. 

**¿Se detecta alguna observación influyente? ¿Que indican los tests de diagnóstico? ¿Podemos utilizar el modelo ajustado para la predicción del porcentaje de de calorías totales obtenidas de carbohidratos complejos?**

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
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

## Predicción

Construimos un rango de predicción para las proteínas y trabajamos con un peso medio para obtener la gráfica de predicción. 

**¿Cómo interpretamos este gráfico de predicción? ¿Tendría sentido reconvertir la variable peso en un factor ordinal y ajustar el modelo correspondiente? Justifica tu respuesta. ¿Qué tipo de modelo tendríamos entonces? ¿Sería más o menos informativo que el modelo con todas las predictoras numéricas? ¿Qué diferencias en el estudio del modelo deberíamos tener en cuenta? Ajusta un nuevo modelo considerando tres grupos en la variable peso identificados como menos de 100, entre 100 y 120, y más de 120.**

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
newdata <-expand.grid(protein = round(seq(min(ejer03$protein), 
                                          max(ejer03$protein), 
                                          length = 10),0), 
                      weight = round(mean(ejer03$weight),0))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))
# Gráfico para cada valor de weight
ggplot(newdata, aes(x = protein, y = fit)) + 
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Protein", y ="Carbohydrate", 
       title = "Mean Weight") +
  facet_wrap(~ weight, scales="free_x") +  
  theme_bw()
```

Construcción de la nueva variable

```{r ,error=FALSE,warning=FALSE,message=FALSE, results ='hide', fig.show = 'hide' }
# Creación de la nueva varaible
ejer03$weight.fac <- cut(ejer03$weight,breaks=c(50,100,120,150))
```

[Volver a la página principal](https://jmsocuellamos.github.io/)

