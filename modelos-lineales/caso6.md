---
layout: default
---

***
# Caso 6
***

En una investigación para la reducción de peso corporal se registran los pesos, en kilogramos, de veinte hombres antes y después de la participación en un programa de "pérdida de cintura". Los datos corresponden con la tabla 2.8 de @Dobson02. Para analizar adecuadamente estos datos juntamos las columnas correspondientes a antes y después en una columna con un factor que indica a cuando corresponde la observación. 

```
# Lectura de datos
previo <- read_csv("https://goo.gl/jWGurk", col_types = "idd")
# Para construir modelos trasformamos los datos originales en la forma habitual  
ejer07 <- previo %>% gather(`before`,`after`,
                            key = "Time", 
                            value = "Weight")
```

Representamos los datos en la forma habitual. 

**¿Qué conclusión podemos extraer de este gráfico? ¿Qué resultado esperas que proporcione el modelo que puedas ajustar?**

```
# Gráfico de cajas
ggplot(ejer07,aes(x = Time, y = Weight)) + 
  geom_boxplot() + 
  theme_bw()
```


## Estimación del modelo

**Atendiendo a la tabla ANOVA resultante para este modelo ¿Cuál es la conclusión del modelo obtenido? ¿Es necesario seguir trabajando sobre este modelo? Justifica tu respuesta.**

```
# Ajuste del modelo
ajuste <- lm(Weight ~ Time, data = ejer07)
# Tabla ANOVA
anova(ajuste)
# Análsis inferencial sobre los coeficientes del modelo
tidy(ajuste)
```


[Volver a la página principal](https://jmsocuellamos.github.io)
