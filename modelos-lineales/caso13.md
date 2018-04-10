---
layout: default
---

***
# Caso 13
***

El banco de datos presenta la información referida al nacimiento y mortalidad infantil de 800 niños nacidos en el estado de Carolina del Norte. Las variables consideradas en el estudio son:

- plural: Número de hijos nacidos del embarazo.
- sex: Sexo del bebe.
- mage: Edad de la madre.
- weeks: Semanas completas de gestación.
- marital: Estado matrimonial (“married”=1; “not married”=2).
- racemom: Raza de la madre (“other non white”=0,“White”=1,“Black”=2,“America indian”=3,“Chinese”=4,“Hawaiian”=5,“Filipino”=6,“Other asian”=7).
hispmom: Madre de origen hispánico (“Cuban”=C,“Mexican”=M,“Non-Hispanic”=N,“Other”=O,“Puerto Rican”=P,“Central/South american”=S,“Not classificable”=U).
- gained: Peso ganado durante el embarazo (en libras).
- smoke: Madre fumadora (“Yes”=1,“No”=0).
- drink: Madre bebedora (“Yes”=1,“No”=0).
- tounces: Peso del bebe (en onzas).
- tgrams: Peso del bebe (en gramos).
- low: Bebe de poco peso (“Yes”=1,“No”=0).
- premie: Bebe prematuro (“Yes”=1,“No”=0).

**Obtén un modelo que trate de explicar el peso del bebé al nacer (en gramos) en función del sexo del bebe, de la edad de la madre, de las semansa completas de gestación, de si la madre es bebedora o fumadora, y de si el bebé ha sido prematuro.**

A continuación tienes el código para la lectura de datos y la codificación de los factores.

```
ncbirth <- read_csv("https://goo.gl/mB9Jcn", col_types = "dcddcccdccddcc")
str(ncbirth)
# Recodificación del factor
ncbirth <- ncbirth %>% 
  mutate(sex=fct_recode(sex,"male"="1","female"="2"),
         marital=fct_recode(marital,"married"="1","not married"="2"),
         racemom=fct_recode(racemom,"other non white"="0",
                            "White"="1",
                            "Black"="2",
                            "America indian"="3",
                            "Chinese"="4",
                            "Hawaiian"="5",
                            "Filipino"="6",
                            "Other asian"="7",
                            "Other"="8"),
         hispmom=fct_recode(hispmom,"Cuban"="C",
                            "Mexican"="M",
                            "Non-Hispanic"="N",
                            "Other"="O",
                            "Puerto Rican"="P",
                            "Central/South american"="S",
                            "U"="Not classificable"),
         smoke=fct_recode(smoke,"Yes"="1","No"="0"),
         drink=fct_recode(drink,"Yes"="1","No"="0"),
         low=fct_recode(low,"Yes"="1","No"="0"), 
         premie=fct_recode(premie,"Yes"="1","No"="0"))
```


[Volver a la página principal](https://jmsocuellamos.github.io)
