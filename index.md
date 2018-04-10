---
layout: default
---

# Aplicaciones de modelos estadísticos

Esta web presenta una colección de análisis de casos de diferentes tipos de modelos estadísticos utilizando R y R Studio. Para cada caso se presenta el conjunto de datos a utilizar así como el código necesario para llevar a cabo cada uno de los análisis.  Se presenta también un guión de preguntas para ir analizando cada uno de los casos presentados. Al final de está página se presentan las librerias de R necesarias para el análisis de cada caso, así  como la bibliografía utilizada.

# Tipos de modelos

* [Modelos Lineales](modelos-lineales) 
* Modelos Lineales Generalizados (Binomial)
* Modelos Lineales Generalizados (Poisson)
* Modelos Lineales Generalizados (Tablas de contingencia)
* Modelos de supervivencia 

# Librerías de R

```
# Cargamos las librerías
library(tidyverse)
library(forcats)
library(broom)
library(reshape2)
library(lmtest)
library(mgcv)
library(MASS)
library(modelr)
```

## Bibliografía

- Daniel, Wayne W. 2005. Biostatistics. Eighth Edition. Wiley.
- Dobson, A. 2002. An Introduction to Generalized Linear Models. CRC Press.
- Faraway, JJ. 2006. Extending the Linear Model with R. Chapman & Hall.
- Krzanowski, Wojtek J. 1998. An Introduction to Statistical Modelling. Arnold.
- Mayoral, A.M., and Morales J. 2001. Modelos Lineales Generalizados. Servicio de publicaciones de la UMH.
- Ugarte, MD, AF Militino, and A Arnholt. 2008. Probability and Statistics with R. CRC Press.
- Wickham, Hadley, and Garrett Grolemund. 2016. R for Data Science. O’Reilly.
- Wood, SN. 2006. Generalized Additive Models. Chapman & Hall.
