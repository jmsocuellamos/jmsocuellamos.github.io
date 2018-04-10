---
layout: default
---

***
# Caso 4
***

Es bien sabido que la concentración de colesterol en el suero sanguíneo aumenta con la edad, pero es menos claro si el nivel de colesterol también está asociado con el peso corporal. Los datos muestran para una treinta de mujeres el colesterol sérico (milimoles por litro), la edad (años) y el índice de masa corporal (peso dividido por la altura al cuadrado, donde el peso se midió en kilogramos y la altura en metros). Los datos corresponden con la tabla 6.17 de @Dobson02.

Lectura de datos
```
# Lectura de datos
ejer05 <- read_csv("https://goo.gl/EKXWRc", 
                   col_types = "ddd")
```

Debemos plantear un modelo de regresión lineal múltiple donde tratamos de explicar la concentración de colesterol en el suero sanguíneo en función de la edad y del peso corporal. Realizamos gráficos de dispersión individual para cada posible predictora. 

**¿Qué conclusiones podemos extraer de este gráfico? ¿Las dos predictoras pueden tener efecto sobre la concentración de colesterol?**

```
datacomp = melt(ejer05, id.vars='chol')
# Gráfico respecto de cada predictora
ggplot(datacomp) +
  geom_jitter(aes(value,chol, colour=variable),) + 
  geom_smooth(aes(value,chol, colour=variable), 
              method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Chol") +
  theme_bw()
```


## Estimación del modelo 

Procedemos con la estimación del mejor modelo realizando un proceso de selección de variables. 

**¿Qué predictoras contiene el modelo resultante del proceso de selección de efectos? ¿El modelo obtenido es adecuado para explicar la concentración de colesterol? Justifica tu respuesta. ¿Cuál es la ecuación de modelo ajustado? ¿Cómo aumenta la concentración del colesterol para dos mujeres con un diferencia de edad de cinco años? ¿y de un volumen de masa corporal de de cinco unidades? ¿Tiene sentido una interceptación negativa para este modelo? ¿Cuál sería la posible explicación? Vamos a probar a ajustar un modelo sin interceptación y compararemos los resultados con el modelo con interceptación.**

```
# Ajuste del modelo
ajuste <- lm(chol ~ age + bmi , 
             data = ejer05)
# Selección del modelo
ajuste <- step(ajuste)
# Inferencia
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
```

Modelo sin interceptación. 

**¿Resulta más adecuado este modelo? Justifica tu respuesta y responde a las preguntas sobre el modelo planteadas anteriormente.**

```
# Ajuste del modelo sin interceptación
ajuste2 <- lm(chol ~ age + bmi - 1, 
              data = ejer05)
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

## Diagnóstico del modelo 

Realizamos el diagnóstico del modelo tanto mediante procedimientos gráficos como los tests de diagnóstico. 

**¿Que opinas sobre los gráficos de diagnóstico? ¿Podríamos considerar que existen observaciones influyentes? Realizamos el análisis correspondiente. ¿Que conclusión extraemos de este análisis? ¿Debemos considerar la eliminación de alguna observación? Justifica tu respuesta.**


```
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","age", "bmi", ".stdresid")] 
datacomp = melt(diagnosis, id.vars='.stdresid')
# Gráfico residuos, ajustados y regresora
ggplot(datacomp) +
  geom_jitter(aes(value,.stdresid, colour=variable),) + 
  geom_smooth(aes(value,.stdresid, colour=variable), 
              method= mgcv::gam, se=FALSE) +
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

Tests diagnósticos. 

**¿Podemos considerar como adecuado el modelo obtenido? Justifica tu respuesta.**

```
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

## Predicción

Construimos un grid de valores para la edad y trabajamos con el valor mínimo, máximo y la media del índice de masa corporal para representar la predicción asociada. 

**¿Cómo podemos interpretar los modelos de predicción obtenidos? ¿Cuál es la diferencia de predicción para una edad de 50 años?**

```
# Predicción
newdata <-expand.grid(age = round(seq(min(ejer05$age), 
                                      max(ejer05$age), 
                                      length = 10),0), 
                      bmi = round(c(min(ejer05$bmi),
                                    mean(ejer05$bmi),
                                    max(ejer05$bmi)),0))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))
# Gráfico para cada valor 
ggplot(newdata, aes(x = age, y = fit)) + 
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Age", y ="Cholesterol", title = "BMI") +
  facet_wrap(~ bmi, scales="free_x") +  
  theme_bw()
```


[Volver a la página principal](https://jmsocuellamos.github.io)
