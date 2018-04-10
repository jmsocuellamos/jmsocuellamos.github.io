---
layout: default
---

***
# Caso 1
***

Se trata de determinar la pérdida de calor sufrida por cierto compuesto cuando es sometido a altas temperaturas. Los datos recogidos son los siguientes:
```
temperatura <- c(460, 450, 440, 430, 420, 410, 450, 440, 430, 
                 420, 410, 400, 420, 410, 400)
perdida <- c(0.3, 0.3, 0.4, 0.4, 0.6, 0.5, 0.5, 0.6, 0.6, 0.6, 
             0.7, 0.6, 0.6, 0.6, 0.6)
ejer02 <- data.frame(temperatura,perdida)
```

En este caso ambas variables son de tipo numérico y debemos plantear un modelo de regresión lineal simple. Comenzamos con la representación gráfica de los datos. **¿Qué tipo de tendencia se aprecia en el gráfico? ¿Podemos decir que la pérdida de calor disminuye cuando aumenta la temperatura? ¿La tendencia lineal parece apropiada para este tipo de datos?**


```
ggplot(ejer02,aes(temperatura,perdida)) + 
  geom_point() + 
  labs(x = "Temperatura", y = "Pérdida de calor") + 
  theme_bw() + 
  geom_smooth(method = "lm", se = FALSE)
```


## Estimación del modelo 

Dado que sólo tenemos una predictora, la selección del modelo consiste únicamente en la valoración de la bondad del ajuste proporcionado por la variable predictora. **¿El modelo obtenido es adecuado para explicar el comportamiento de la pérdida de calor a través de la temperatura? ¿Cuál es la ecuación del modelo ajustado? ¿Cómo interpretamos la interceptación del modelo? ¿Y la pendiente? Si aumentamos 100 grados ¿qué ocurre con la pérdida de calor?**

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

Realizamos el diagnóstico del modelo para verificar si se cumplen las hipótesis del modelo de regresión lineal simple. **Respecto del análisis gráfico ¿qué podemos decir de los gráficos obtenidos?** 

```
# Valores de diagnóstico
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","temperatura",".stdresid")] 
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
```

**Respecto de los tests diagnósticos ¿qué podemos decir sobre las hipótesis del modelo? ¿El modelo obtened es adecuado para predecir el comportamiento de la pérdida de calor?**

```
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

## Predicción del modelo

Pasamos a la predicción de la media para el modelo ajustado. **¿Cuál es estimación de la pérdida de calor para las temperaturas de 400, 420, 440, y 460? ¿Cuál es el rango de confianza para la predicción en cada una de las temperaturas anteriores? ¿Resulta posible calcular la pérdida de calor para una temperatura de 480? ¿Cuál sería el intervalo de confianza para dicha predicción?**

```
# Predicción
newdata <- data.frame(temperatura = seq(min(ejer02$temperatura), 
                                        max(ejer02$temperatura), .01))
# Obtenemos la predicción para el modelo ajustado
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))
# Gráfico de la predicción
ggplot(newdata, aes(x = temperatura, y = fit)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Temperatura", 
       y = "Pérdida de calor", 
       title = "Bandas de predicción") +
  theme_bw()
```

[Volver a la página principal](https://jmsocuellamos.github.io/)

[Volver al listado de casos](modelos-lineales)

