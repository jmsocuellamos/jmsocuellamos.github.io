---
layout: default
---

***
# Caso 7
***

Se realiza una investigación para conocer los niveles de fosfato inorgánico en plasma (mg / dl) una hora después de una prueba de tolerancia a la glucosa estándar para sujetos obesos, con o sin hiperinsulinemia (H-O, y N-O respectivamente), y controles (C). Los datos corresponden con la tabla 6.18 de @Dobson02.

```
# Lectura de datos
ejer08 <- read_csv("https://goo.gl/3L4EtK", col_types = "cd")
```

En este caso tenemos un modelo ANOVA de un vía (grupo de pacientes) para explicar el nivel de fosfato inorgánico en plasma. Cargamos los datos y representamos de la forma habitual. 

**¿Qué conclusión podemos extraer de este gráfico? ¿Consideras adecuado plantear un modelo para tratar de conocer si el nivel medio de fosfato es diferente entre los grupos considerados?**

```
# Gráfico de cajas
ggplot(ejer08,aes(x = Group, y = phosphate)) + 
  geom_boxplot() + 
  theme_bw()
```

## Estimación del modelo

Construimos el modelo correspondiente a esta situación. 

**¿Qué conclusión podemos extraer de la tabla ANOVA obtenida? ¿Cuál es la media estimada del nivel de fosfato para cada grupo? ¿Podemos considerar que las medias en todos los grupos son diferentes?** 

```
# Ajuste del modelo
ajuste <- lm(phosphate ~ Group, data = ejer08)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

Realizamos el test de comparación múltiples. 

**¿Qué conclusiones podemos extraer del análisis realizado? ¿tendrá interés el diseño experimental llevado a cabo? Justifica tu respuesta.**

```
# Comparaciones múltiples
TukeyHSD(aov(phosphate ~ Group, data = ejer08))
```

## Diagnóstico del modelo

En este caso hemos de tener en cuenta que al tratarse de un modelo ANOVA la verificación de las hipótesis son un poco más laboriosas. Nos centramos únicamente en los tests diagnósticos. 

**¿Qué podemos concluir del proceso de diagnóstico del modelo? ¿Podemos utilizar el modelo construido para estimar la media del nivel de fosfato en función del grupo al que pertenecen los sujetos?**

```
# Diagnóstico
fitinf <- fortify(ajuste)
# Varianza constante
bartlett.test(phosphate ~ Group, data = ejer08)
# Normalidad (Obtenemos los pvalores del test de normalidad)
shapiro.test(fitinf$.stdresid[fitinf$Group == "C"])
shapiro.test(fitinf$.stdresid[fitinf$Group == "H-O"])
shapiro.test(fitinf$.stdresid[fitinf$Group == "N-O"])
```

## Predicción

En esta caso la predicción se basa únicamente en el cálculo de los intervalos de confianza de la media para cada uno de los grupos considerados. 

**¿Qué podemos decir de los intervalos de predicción obtenidos? ¿Podríamos utilizar esta predicción como diagnóstico médico? Justifica tu respuesta.**

```
# Predicción
# Secuencia de valores de predicción
newdata <- data.frame(Group = unique(ejer08$Group))
# Predicción para la media de la respuesta
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence")) 
# Gráfico resultante (predicción y bandas de predicción). 
# Ordenamos las clases de acuerdo al valor de fit
ggplot(newdata, aes(x = fct_reorder(Group,fit), y= fit)) + 
    geom_errorbar(aes(ymin=lwr, ymax=upr), width=.1) +
    geom_line() +
    geom_point() +
    labs(x = "Grupo", y = "Nivel de fósfato") +
    coord_flip() +
    theme_bw()
```


[Volver a la página principal](https://jmsocuellamos.github.io)
