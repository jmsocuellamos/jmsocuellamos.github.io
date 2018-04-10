---
layout: default
---

***
# Caso 8
***

Se lleva a cabo un estudio sobre el contenido promedio de grasa (en porcentaje) en la leche del ganado de cinco razas distintas canadienses. Para ello se consideran veinte ejemplares de pura raza (diez de dos años y diez maduras de más de cuatro años de cada una de cinco razas. 

```
ejer09 <- read_csv("https://goo.gl/J2ZKWK", col_types = "dcc")
```

Nos encontramos ante un diseño experimental con dos posibles variables predictoras de tipo categórico. Debemos plantear un modelo ANOVA con dos vías de clasificación. Recordemos que en estas situaciones el estudio principal se centra en primer lugar en determinar si existe interacción entre los factores considerados, es decir, si la media del contenido de grasa en la leche depende simultáneamente de la raza de la oveja y de su edad. Cargamos los datos y realizamos el gráfico de cajas conjunto y el de interacción de las medias de cada combinación de ambos factores. 

**¿Qué conclusiones podemos extraer del gráfico de cajas conjunto? ¿Qué consideras más relevante del gráfico obtenido? ¿Qué podemos decir a la vista del gráfico de interacción? ¿Qué tipo de modelo parecería más adecuado a la vista de estos datos?**

```
# Diagrama de cajas
ggplot(ejer09,aes(x = Bread, y = Butterfat, color = Age)) + 
  geom_boxplot() + 
  theme_bw()
# Gráfico de interacción de medias
ggplot(ejer09, aes(x = Bread, y = Butterfat, 
                   group = Age, 
                   color = Age)) +
  stat_summary(fun.y = mean, geom = "point") +
  stat_summary(fun.y = mean, geom = "line") +
  theme_bw()
```

## Estimación del modelo

Procedemos con la estimación del modelo completo considerando la posible interacción entre los factores, a pesar de que el gráfico de interacción parece indicar que dicho efecto es irrelevante. 

**¿Qué efectos son relevantes para explicar el contenido promedio de grasa (en porcentaje) en la leche del ganado? Justifica tu respuesta. ¿Cuál es el modelo estimado? ¿Consideras que se ha finalizado con el proceso de estimación del modelo? ¿Qué otra información deberíamos extraer?**

```
# Ajuste del modelo
ajuste <- lm(Butterfat ~ Bread*Age, 
             data = ejer09)
# Selección del modelo
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

Realizamos le test de comparaciones múltiples. 

**¿Qué conclusiones podemos extraer del análisis realizado? ¿Cómo podrías representar la información obtenida para una mejor visualización de los resultados?**

```
# Comparaciones múltiples
TukeyHSD(aov(Butterfat ~ Bread, 
             data = ejer09))
```

## Diagnóstico del modelo

Comenzamos con el diagnóstico del modelo ajustado. 

**¿Qué conclusiones extraemos del diagnóstico realizado? ¿Cómo propones que deberíamos modificar nuestro modelo?** 

```
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

**¿Qué transformación debemos realizar sobre la variable respuesta? Transformamos la variable y ajustamos de nuevo el modelo completo.**

```
# Transformación de boxcox
boxcox(ajuste) 
```

**¿Qué modelo resulta tras la transformación de la respuesta? ¿Cuáles son las ecuaciones del modelo ajustado? ¿Cómo interpretamos los coeficientes de este modelo? A la vista de la tabla ANOVA ¿cuál debería ser el modelo a utilizar?**

```
# Creamos la variable transformada
ejer09 <- ejer09 %>% mutate(Butterfatinv = 1/Butterfat)
#####################################
# Realizamos el ajuste de nuevo
ajuste <- lm(Butterfatinv ~ Bread*Age, 
             data = ejer09)
# Selección del modelo
ajuste <- step(ajuste)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```

 Realizamos el diagnóstico para este nuevo modelo. 
 
 **¿Es adecuado el modelo obtenido? Justifica tu respuesta.**

```
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

## Predicción 

Construimos los intervalos de confianza para la predicción correspondiente al modelo ajustado. 

**¿Qué podemos concluir a la vista del gráfico de predicción? ¿Sería posible clasificar una oveja sin mirarla conociendo el contenido promedio de grasa de su leche? Justifica como procederías para contestar a esta cuestión.**

```
# Predicción
# Secuencia de valores de predicción
newdata <- data.frame(Bread = c("Ayrshire","Canadian","Guernsey"
                                ,"Holstein-Fresian","Jersey"))
# Predicción para la media de la respuesta
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))  
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


[Volver a la página principal](https://jmsocuellamos.github.io)
