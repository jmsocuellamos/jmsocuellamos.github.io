---
layout: default
---

***
# Caso 5
***

En un estudio sobre las diferentes clases de queso cheddar que se fabrican en LaTrobe Valley de Victoria, Australia, se analizaron muestras de queso por su composición química: concentración de ácido acético (escala logarítmica); concentración de sulfuro de hidrógeno (escala logarítmica); y la concentración de ácido láctico. Por otro lado, se paso una muestra de cada uno de ellos a un conjunto de catadores y se registro la puntuación obtenida por cada uno de ellos. Estamos interesados en relacionar la puntuación final de los catadores con los resultados del análisis químico.

```
# Lectura de datos
ejer06 <- read_csv("https://goo.gl/V4lDNs", col_types = "dddd")
```

Nos encontramos ante un modelo de regresión lineal múltiple (todas las variables son de tipo numérico) donde tratamos de explicar la puntuación final de cada queso en función de la concentración de ácido acético, concentración de sulfuro de hidrógeno, y la concentración de ácido láctico. cargamos los datos y realizamos los gráficos de dispersión individuales. 

**¿Cómo podemos interpretar los gráficos obtenidos? ¿tenemos alguna indicación de las variables que pueden resultar más relevantes para explicar la puntuación final obtenida?**

```
# Gráfico inicial
datacomp = melt(ejer06, id.vars='Taste')
# Gráfico respecto de cad predictora
ggplot(datacomp) +
  geom_jitter(aes(value,Taste, colour=variable),) + 
  geom_smooth(aes(value,Taste, colour=variable), 
              method= mgcv::gam, se=FALSE) +
  facet_wrap(~variable, scales="free_x") +
  labs(x = "", y = "Taste") +
  theme_bw()
```

## Estimación del modelo

Construimos el mejor modelo posible en función de las variables predictoras consideradas. ¿Cuáles son las predictoras involucradas en el modelo final? ¿Cuál es la capacidad explicativa de dicho modelo? ¿Cuáles son los coeficientes del modelo obtenido? ¿Tienen sentido o deberíamos modificar nuestro modelo? ¿Cuál es tu propuesta de mejora?

```
# Ajuste del modelo
ajuste <- lm(Taste ~ Acetic + H2S + Lactic, 
             data = ejer06)
# Selección del modelo
ajuste <- step(ajuste)
# Inferencia
tidy(ajuste)
# Bondad del ajuste
glance(ajuste)
```

Construimos el nuevo modelo y lo comparamos con el anterior. ¿Podemos considerarlo mejor que el anterior? ¿Cómo interpretamos el comportamiento de cada predictora en este nuevo modelo? 

```
# Ajuste del modelo
ajuste2 <- lm(Taste ~ Acetic + H2S + Lactic - 1, 
              data = ejer06)
# Selección del modelo
ajuste2 <- step(ajuste2)
# Inferencia
tidy(ajuste2)
# Bondad del ajuste
glance(ajuste2)
```

## Diagnóstico del modelo

Procedemos con el diagnóstico del último modelo ajustado. Comenzamos con el análisis gráfico y de influencia. 

**¿Qué podemos concluir del análisis realizado?**


```
ajuste <- ajuste2
# Diagnóstico del modelo
fitinf <- fortify(ajuste)
# Análisis gráfico
diagnosis <- fitinf[,c(".fitted", "H2S", "Lactic", "Acetic", ".stdresid")] 
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

Realizamos los tests diagnósticos. 

**¿Es válido el modelo construido? Justifica tu respuesta.**

```
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```


## Predicción

Construimos un grid de valores para el ácido láctico y tomamos los valores medios para las otras dos predictoras. 

**¿Cuál es la predicción de la puntuación para para los valores medios de las tres predictoras? ¿Qué variable crees que influirá más en la predicción final? ¿Cómo podríamos comprobar este hecho?**


```
newdata <-expand.grid(H2S = mean(ejer06$H2S), 
                      Lactic = seq(min(ejer06$Lactic),
                                   max(ejer06$Lactic), 
                                   by = 0.2),
                      Acetic = mean(ejer06$Acetic))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))
# Gráfico para cada valor de weight
ggplot(newdata, aes(x = Lactic, y = fit)) + 
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr), alpha = 1/5) +
  labs(x = "Lactic", y ="Taste") +
  theme_bw()
```


[Volver a la página principal](https://jmsocuellamos.github.io)
