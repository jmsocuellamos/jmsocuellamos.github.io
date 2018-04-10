---
layout: default
---

***
# Caso 14
***

Un grupo de varones adultos con edades comprendidas entre 30 y 65 años participaron en un estudio para investigar la relación entre el consumo de carne y el colesterol. Los sujetos fueron organizados en tres grupos de acuerdo a tres dietas diferentes con una duración de 20 semanas. Las variables consideradas son:

- SUBJ: Sujeto.
- Dieta: Tipo de dieta (“BEEF” = carne de vaca unicamente, “PORK” = carne de cerdo unicamente, “CHFISH” = carne de pollo y pescado unicamente).
- chol: Nivel de colesterol.

**Obtén un modelo que trate de explicar el nivel de colesterol en función de la dieta seguida.**

A continuación tienes el código para la lectura de datos.

```
serumcho <- read_csv("https://goo.gl/ghxka2", col_types = "iddd")
str(serumcho)
# Reorganizamos los datos
caso14 <- gather(serumcho,`BEEF`,`PORK`,`CHFISH`, key = "dieta", value = colesterol)
```


[Volver a la página principal](https://jmsocuellamos.github.io)
