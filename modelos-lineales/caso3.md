---
layout: default
---

***
# Caso 3
***

Los datos recogen la respuesta de un pasto de gramíneas y leguminosas a varias cantidades de fertilizante de fósforo (datos de D. F. Sinclair). En concreto se mide el rendimiento total (en kilogramos por hectárea), de pasto y leguminosa juntos, y la cantidad de potasio ($K$) utilizada (en kilogramos por hectárea). Los datos corresponden con la tabla 6.16 de @Dobson02.

```
# Lectura de datos
ejer04 <- read_csv("https://goo.gl/AOikQU", col_types = "dd")
```

En este caso tenemos las dos variables de tipo  numérico y planteamos un modelo de regresión lineal simple. 

**¿Cómo afecta la cantidad de potasio en el rendimiento total del pasto? ¿Podemos considerar la relación entre ambas variables de tipo lineal?**  

```
# Gráfico inicial
ggplot(ejer04,aes(K,yield)) + 
  geom_point() + 
  labs(x = "K", y = "yield") + 
  theme_bw() + 
  geom_smooth(method = "lm", se = FALSE)
```

## Estimación del modelo 

Construimos y evaluamos la calidad del ajuste obtenido. 

**¿Podemos decir que la cantidad de potasio explica el rendimiento total del pasto? Justifica tu respuesta. ¿Que relación se ha establecido entre ambas variables? ¿Cuál sería el rendimiento del pasto si no utilizamos el potasio? ¿Cómo aumenta el rendimiento por cada aumentar en 10 kilogramos por hectárea la cantidad de potasio?**

```
# Ajuste del modelo
ajuste <- lm(yield ~ K,data = ejer04)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
```

## Diagnóstico del modelo 

Realizamos el diagnóstico del modelo tanto mediante procedimientos gráficos como los tests de diagnóstico. 

**¿Cómo interpretamos los gráficos de diagnóstico? ¿Encontramos alguna dificultad con las hipótesis del modelo? ¿Podemos considerar que le modelo obtenido es adecuado para explicar la relación entre la cantidad de potasio y el rendimiento del pasto?** 

```
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted","K",".stdresid")] 
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

```
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

## Predicción

Calculamos un grid de valores de potasio y obtenemos el correspondiente modelo de predicción. 

**¿Cuáles son la predicciones del rendimiento del pasto para los valores de potasio de 20, 30, y 40?**


```
newdata <- data.frame(K = seq(min(ejer04$K), max(ejer04$K), .5))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))
# Gráfico de la predicción
ggplot(newdata, aes(x = K, y = fit)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "K", y = "Yield", title = "Bandas de predicción") +
  theme_bw()
```


[Volver a la página principal](https://jmsocuellamos.github.io/)
