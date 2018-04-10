---
layout: default
---

***
# Caso 10
***

Se lleva a cabo una investigación sobre diversas malformaciones del sistema nervioso central registradas en nacidos vivos en Gales del Sur, Reino Unido. El estudio fue diseñado para determinar el efecto de la dureza del agua sobre la incidencia de tales malformaciones. La información registrada son: NoCNS = recuento de nacimientos sin problema CNS; An = conteo de nacimientos de Anencephalus; Sp = conteo de nacimientos de espina bífida; Otro = recuento de otros nacimientos del SNC; Agua = endurecimiento del agua; Trabajo = un factor con niveles Manual o No manual en función del tipo de trabajo realizado por los padres.

```
# Lectura de datos
previo <- read_csv("https://goo.gl/bNOSxt", col_types = "cdddddc")
# Calculamos el número total de malformaciones para utilizarla como variable
ejer11 <- previo %>% mutate(CNS=An+Sp+Other)
```

Dado que estamos interesados en establecer un modelo para el número total de malformaciones, deberíamos construir en primer lugar una variable que contenga dichos valores como la suma de todos los tipos de malformaciones registradas. Esta nueva variable se utilizará como respuesta en el modelo propuesto. Como posibles variables predictoras tenemos el tipo de trabajo de los padres y la dureza del agua. Planteamos un modelo ANCOVA con un regresor y un factor. No consideramos el distrito origen de los sujetos para este modelo. Procedemos con el caso anterior para representar las diferentes opciones de modelización para estos datos. 

**¿Qué información proporcionan los gráficos obtenidos de cara a la elección del posible modelo final sobre estos datos?**

```
# Representación gráfica de los modelos de regresión
# Modelo con una única recta
M0 <- lm(CNS ~ Water,data = ejer11)
# M1: modelo con rectas paralelas
M1 <- lm(CNS ~ Work + Water,data = ejer11)
# M2: modelo con rectas no paralelas
M2 <- lm(CNS ~ Work * Water,data = ejer11)
# grid de valores para construir los modelos
grid <- ejer11 %>% data_grid(Work,Water) %>% 
  gather_predictions(M0,M1,M2)
# Gráfico
ggplot(ejer11,aes(Water,CNS,colour = Work)) + 
  geom_point() + 
  geom_line(data = grid, aes(y = pred)) +
  facet_wrap(~ model) +
  labs(x = "Dureza del agua", y = "Número malformaciones") + 
  theme_bw()
```

## Estimación del modelo

Seleccionamos el mejor modelo para estos datos partiendo del modelo más complejo (interacción entre dureza del agua y tipo de trabajo). 
**¿Qué tipo de modelo se ha estimado finalmente? ¿Cuales son las rectas de regresión estimadas? ¿Cómo interpretamos los coeficientes de dichos modelos? ¿Qué sentido tiene la interceptación del modelo?**

```
# Selección del modelo
ajuste <- M2
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Coeficientes del modelo
tidy(ajuste)
```

## Diagnóstico del modelo

Realizamos el diagnóstico gráfico y basado en los tests de diagnóstico para este tipo de modelos. 

**¿Qué indica el gráfico de residuos versus ajustados para cada nivel del factor? ¿Cómo interpretamos los resultados de los tests de diagnóstico? ¿Cómo procedemos a partir de ahora?**

```
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

Probamos con las transformaciones de Box-Cox. 

**¿Existe una transformación adecuada para este modelo? Transformamos la respuesta y reajustamos el modelo.**

```
boxcox(ajuste) 
```

**¿Qué modelo resulta del nuevo proceso de estimación? ¿Cómo interpretamos los coeficientes de dicho modelo?**

```
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

**¿Qué ocurre con las hipótesis de este modelo?**

```
# Nuevo diagnóstico
fitinf <- fortify(ajuste)
# Varianza constante
bartlett.test(.stdresid ~ Work, data = fitinf)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Work == "Manual"])
shapiro.test(fitinf$.stdresid[fitinf$Work == "NonManual"])
```

## Predicción

Construimos los intervalos de confianza para la predicción correspondiente a cada uno de los niveles del factor. Por el momento seguimos trabajando con la variable transformada. 

**¿Cómo obtendríamos los valores de predicción en la escala original del número de malformaciones?** 

```
# Secuencia de valores de predicción
newdata <- data.frame(Work = unique(ejer11$Work))
# Predicción para la media de la respuesta
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))  
# Gráfico resultante (predicción y bandas de predicción). 
# Ordenamos las clases de acuerdo al valor de fit
ggplot(newdata, aes(x = fct_reorder(Work,fit), y= fit)) + 
    geom_errorbar(aes(ymin=lwr, ymax=upr), width=.1) +
    geom_line() +
    geom_point() +
    labs(x = "Tipo de trabajo", y = "Logaritmo número de malformaciones") +
    coord_flip() +
    theme_bw()
```


[Volver a la página principal](https://jmsocuellamos.github.io)
