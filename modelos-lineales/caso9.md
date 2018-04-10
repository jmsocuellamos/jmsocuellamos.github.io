---
layout: default
---

***
# Caso 9
***

Los datos que se presentan son los pesos al nacer (en gramos) y las edades gestacionales estimadas (en semanas) de 12 bebés hombres y mujeres nacidos en un determinado hospital. Las edades medias son casi las mismas para ambos sexos. Se está interesado en estimar el peso de los bebes a partir de su sexo y su edad gestacional. Tenemos una variable predictora de tipo numérico (edad gestacional) y otra de tipo categórico (sexo). Consideramos un modelo ANCOVA con un regresor y un factor. Como el sexo viene codificado como 1 (Boy) y 2 (Girl), construimos el factor asociado antes de la representación gráfica de los datos.

```
# Lectura de datos
previo <- read_csv("https://goo.gl/B3yoLJ", col_types = "ddc")
# Recodificación del factor
ejer10 <- previo %>% mutate(sex=fct_recode(sex,"Boy"="1","Girl"="2"))
```

Para la representación gráfica de los datos consideramos las tres posibilidades de modelización para estos datos:

- Una única recta de regresión entre peso y edad. Se considera irrelevante la asociación entre el sexo y edad, y el posible efecto del sexo del bebé en su peso final. 
- Dos rectas de regresión paralelas (una por cada sexo). Se considera irrelevante la asociación entre el sexo y edad, pero se considera un posible efecto del sexo del bebé en su peso final. 
- Dos rectas distintas (una por cada sexo). Se considera relevante la asociación entre el sexo y edad para el peso final del bebé. 

**¿Hay evidencias de cuál puede ser el mejor modelo de los tres planteados? Justifica tu respuesta.**

```
# Representación gráfica de los modelos de regresión
# Modelo con una única recta
M0 <- lm(weight ~ age,data = ejer10)
# M1: modelo con rectas paralelas
M1 <- lm(weight ~ sex + age,data = ejer10)
# M2: modelo con rectas no paralelas
M2 <- lm(weight ~ sex * age,data = ejer10)
# grid de valores para construir los modelos
grid <- ejer10 %>% data_grid(sex,age) %>% 
  gather_predictions(M0,M1,M2)
# Gráfico
ggplot(ejer10,aes(age,weight,colour = sex)) + 
  geom_point() + 
  geom_line(data = grid, aes(y = pred)) +
  facet_wrap(~ model) +
  labs(x = "Edad Gestacional", y = "Peso") + 
  theme_bw()
```

## Estimación del modelo

Comparamos los modelos construidos en el análisis preliminar para decidirnos entre uno de ellos. Para ello partimos del modelo más complejo y realizamos el proceso de selección de efectos. 

**¿Qué efectos están presentes en el modelo según el proceso de selección automático? Si comparamos este resultado con la tabla ANOVA resultante ¿el modelo seleccionado sería el mismo? ¿Qué opción te parece más adecuada? ¿qué implicaciones experimentales tienen ambos modelos? Si consideramos como válido el proporcionado por el procedimiento automático de selección ¿Cómo interpretamos los coeficientes de dicho modelo? ¿Podríamos eliminar la interceptación de este modelo?**

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

Realizamos el diagnóstico del modelo que contiene los efectos principales de edad y sexo del bebé. Realizamos tanto los gráficos de residuos como los tests de diagnóstico. 

**¿Se detecta algún problema con las hipótesis del modelo? ¿Cómo afecta eso al modelo construido?**

```
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

## Predicción

Creamos un grid de valores para la edad gestacional y construimos los modelos de predicción asociados con cada nivel del factor. 

**¿Qué conclusiones podemos extraer de los modelos de predicción construidos? ¿Qué diferencia de peso estimamos para una edad gestacional de 38 semanas entre ambos sexos? ¿Podemos tomar este modelo de predicción como válido para futuros embarazos? ¿Cómo procederíamos y que dificultades nos podríamos encontrar?**

```
newdata <- data.frame(age = rep(seq(min(ejer10$age), 
                                    max(ejer10$age), .01), 
                                each=2), 
                      sex = factor(c("Boy", "Girl")))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, 
                                       interval="confidence"))
# Gráfico de la predicción
ggplot(newdata, aes(x = age, y = fit, color = sex)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr, fill = sex), alpha = 1/5) +
  labs(x = "Edad gestacional", y = "Peso al nacer", 
       title = "Bandas de predicción") +
  theme_bw()
```


[Volver a la página principal](https://jmsocuellamos.github.io)
