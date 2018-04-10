---
layout: default
---

***
# Caso 12
***

El banco de datos de Puromycin contiene 23 mediciones sobre la velocidad de reacción enzimática frente a la concentración de sustrato para células tratadas o no tratadas con Puromicina. Las variables registradas son:

- conc: Concentración de sustrato en partes por millón (ppm).
- rate: Velocidad instántanea de reacción (recuentos/min/min).
- state: Estado (Tratatado o no tratado con Puromicina).

```
# Carga de datos de librería de R
data("Puromycin")
```

Pretendemos construir un modelo que nos permita conocer la velocidad de reacción en función de la concentración del sustrato y de si ha sido tratado o no con Puromicina. Se trata de un modelo ANCOVA con una factor y una variable numérica. Comenzamos con la representación gráfica del modelo de interacción. ¿Consideras adecuado un modelo lineal para este diseño experimnetal? ¿Qué opciones de modelización podríamos plantear?

```
# Gráfico 
ggplot(Puromycin) +
  geom_point(aes(conc,rate, colour = state)) + 
  geom_smooth(aes(conc,rate, colour = state), 
              method= mgcv::gam, se=FALSE) +
  labs(x = "Concengración", y = "Velocidad de reacción") +
  theme_bw()
```

Realizamos un nuevo gráfico con las ecuaciones de suavizado

```
# Gráfico 
ggplot(Puromycin) +
  geom_point(aes(conc,rate, colour = state)) + 
  geom_smooth(aes(conc,rate, colour = state), 
              method= loess, se=FALSE) +
  labs(x = "Concentración", y = "Velocidad de reacción") +
  theme_bw()
```



## Estimación del modelo

Construimos el modelo de suavizado para las predictoras consideradas. 

**¿Cuáles son las principales consecuencias del modelo ajustado? ¿Como interpretamos el coeficiente asociado con si el sujeto ha sido o no tratado? ¿Consideras necesaria la eliminación de algún efecto en el modelo porpuesto? ¿Podemos justificar asumir este modelo frente a otro donde no consideramos la interacción entre el estado y la concentración? Justifica tu respuesta.**

```
ajuste <- gam(rate ~ state + s(conc, k = 10, m = 2, bs = "ps", by = state), 
              data = Puromycin)
summary(ajuste)
```

Ajustamos ambos modelos y los comparamos. **¿Qué modelo debemos asumir como definitivo?**

```
ajuste <- gam(rate ~ state + s(conc, k = 10, m = 2, bs = "ps", by = state), 
              data = Puromycin)
ajuste2 <- gam(rate ~ state + s(conc, k = 10, m = 2, bs = "ps"), 
              data = Puromycin)
data.frame(ajuste$gcv.ubre,ajuste2$gcv.ubre)
```


## Diagnóstico del modelo

Procedemos con el diagnóstico del modelo. Comenzamos con el análisis gráfico y de influencia. 

**¿Qué podemos concluir del análisis realizado?**

```
gam.check(ajuste)
```

## Predicción

Obtenemos el gráfico de predicción asociado con el modelo ajustado. 

**¿Cómo valoras la predicción obtneida? Si el modelo cumple con las hipótesis ¿por qué la predicción parece funcionar tan mal? ¿qué podemos hacer ahora?**

```
# Creamos grid de predicción
# Utlizamos la opción each para indicar el número de repeticiones (tantas como niveles del factor)
newdata <- data.frame(conc = rep(seq(min(Puromycin$conc), max(Puromycin$conc), .01),each=2), state = factor(c("treated", "untreated")))
# Obtenemos la predicción para el modelo ajusatdo
newdata <- data.frame(newdata, predict(ajuste, newdata, type = "response", se.fit=TRUE))
# Calculamos intervalos de confianza
newdata <- newdata %>% mutate(
  upr = fit + (1.96 * se.fit),
  lwr = fit - (1.96 * se.fit)
)
# Gráfico de la predicción
ggplot(newdata, aes(x = conc, y = fit, color = state)) +
  geom_line() +
  geom_ribbon(aes(ymax = upr, ymin = lwr, fill = state), alpha = 1/5) +
  labs(x = "Concentración", y = "Velocidad de reacción", title = "Bandas de predicción") +
  theme_bw()
```

**¿Qué tipo de modelo hemos ajusatdo? ¿Cómo valoras la capacidad explicativa de este modelo? Justifica tu respuesta.**

```
ajuste <- lm(rate ~ state * (conc + I(conc^2)),
             data = Puromycin)
ajuste <- step(ajuste)
tidy(ajuste)
glance(ajuste)
```

**¿Cómo interpretamos los resultados de los tests de diagnóstico? ¿Cómo procedemos a partir de ahora? ¿Dispones de modelos adecuados para este tipo de datos?**

```
# Varianza constante
bptest(ajuste)
# Normalidad
shapiro.test(fitinf$.stdresid)
# Independencia
dwtest(ajuste)
```

**¿Qué ocurre si trnasformamos tabto concentración como velocidad meiante el logaritmo y tratamoe de ajustar un modelo a esos datos?**



[Volver a la página principal](https://jmsocuellamos.github.io)
