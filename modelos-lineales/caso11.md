---
layout: default
---

***
# Caso 11
***

Se ha realizado un estudio para establecer la calidad de los vinos de la variedad Pino Noir en función de un conjunto de características analizadas. Las características analizadas son claridad, aroma, cuerpo, olor y matiz. Para medir la calidad se organiza una cata ciega a un conjunto de expertos y se calcula la puntuación final de cada vino a partir de la información de todos ellos. Además se registra la región de procedencia del vino por si puede influir en la calidad del vino.

```
# Lectura de datos
ejer12 <- read_csv("https://goo.gl/OX9wgM", col_types = "ddddddc")
```

En este caso tenemos un modelo ANCOVA con un factor (región de procedencia) y cinco predictoras numéricas, para tratar de explicar la calidad del vino. En este caso no realizamos el análisis gráfico y pasamos directamente a la estimación del modelo. 

**¿Qué tipo de gráfico podríamos construir para representar la información de este modelo? ¿Nos aportaría información a la hora de plantear el posible modelo estadístico?**

## Estimación del modelo

Planteamos el modelo completo y realizamos el correspondiente proceso de selección automática de efectos. 

**¿Qué modelo resulta del proceso de estimación? ¿Cómo podemos interpretar los diferentes efecto de interacción que aparecen en él?** 

```
# Gráfico del modelo díficil de realizar
# Pasamos al ajuste directamente
ajuste <- lm(calidad ~ region * (claridad + aroma + cuerpo + olor + matiz),
             data = ejer12)
ajuste <- step(ajuste)
tidy(ajuste)
```

## Diagmóstico del modelo

**¿Cómo deberíamos proceder para realizar el diagnóstico de este modelo?** 

## Predicción

En este caso el gráfico de predicción resulta muy complejo debido a la gran cantidad de efecto presentes en el modelo. Nos podemos limitar a obtener una tabla de predicciones para valores específicos de las predictoras. 

**¿Qué valores de las predictoras propones para este proceso? ¿Cómo realizaríamos este proceso? La opción más adecuada en esta situación es el establecimiento de un análisis de escenarios para resolver este problema. En un análisis de escenarios propone tres valores de la variable numérica (uno bajo, otro intermedio y otro alto) y predice los valores correspondientes de la respuesta. ¿Como actuaríamos en este problema?**


[Volver a la página principal](https://jmsocuellamos.github.io)
