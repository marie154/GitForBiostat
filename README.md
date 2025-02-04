---
title: "Hello, Tutorial!"
output: html_document
---

```{r setup, include=FALSE}
library(tidyverse)
library(palmerpenguins)
```

## En rask intro til pipes i dplyr

Pakken tidyverse laster inn

En *pipe* er symbolet %\>% (eller \|\>) med hurtigtast ctrl-shift-m. 

Pipen tar innholdet på venstre side og legger det som første argument til funksjonen på høyre side .


x %>% f(y) er det samme som f(x,y)

x %>% mean(y) er det samme som mean(x$y)


```{r addition}
1 + 1


penguins <- palmerpenguins::penguins
```

### Last inn data

Last inn penguins datasettet og få en oversikt. Først i base R og så i tidy.

Bruk head() og summary()

Prøve basic syntax.

```{r view_data}
#base R
head(penguins)

# Tidy
penguins %>% 
  head()

penguins %>%
  summary()

penguins%>% View()


```

### Reshaping data

```{r Reshape}

penguins %>%
  gather(year, "n", 2:4)


penguins %>% arrange(bill_length_mm) %>% head()


```

### Subset observations

```{r subset}

#filter to subset observations
penguins %>%
  filter(island=="Dream")%>%
  filter(bill_length_mm<200)%>%
  head()

#select to subset variables
penguins %>%
  select(species, island, body_mass_g)%>%
  head()

#Combine and assign
penguins_filt <- penguins %>%
  filter(island=="Dream",
         bill_length_mm<200)%>%
  select(species, island, bill_length_mm, body_mass_g)

head(penguins_filt)





```

## Summarize data

```{r}
# Count penguins for each species / island
penguins %>%
  count(species, island, .drop = FALSE)


penguins %>%
  dplyr::select(body_mass_g, ends_with("_mm")) %>% 
  head()


```


### Bruker "summarise" funksjonene
```{r}

#summarise funksjonen oppsummerer data
penguins %>%
  summarise(n=n(),
            mean_bl = mean(bill_length_mm, na.rm = T),
            mean_bd = mean(bill_depth_mm, na.rm = T)
            )

#Kan legge inn gruppe for å få gruppevis
penguins %>%
  group_by(island)%>%
  summarise(n=n(),
            mean_bl = mean(bill_length_mm, na.rm = T),
            mean_bd = mean(bill_depth_mm, na.rm = T)
            )



penguins %>%
  group_by(island, sex)%>%
  summarise(n=n(),
            mean_bl = mean(bill_length_mm, na.rm = T),
            mean_bd = mean(bill_depth_mm, na.rm = T)
            )


```





## Basic ggplot plotting
Programmet ggplot (grammar of graphics) har tre grunnleggende elementer:

data: en dataframe med input til figur
estetikk (aes()) : spesifiser x, y, farge, form, størrelse
geometri: spesifiser hvordan 

ggplot er modulært så kan bygges på med modifikasjoner i det uendelige.



```{r}

penguins %>%
  ggplot(aes(x=sex, y=bill_length_mm))+
  geom_boxplot()


penguins %>%
  ggplot(aes(x=bill_depth_mm, y=bill_length_mm, color=species))+
  geom_point()

penguins %>%
  ggplot(aes(x=bill_depth_mm, y=bill_length_mm, color=species))+
  geom_point()+
  theme_bw()


```

Kan bruke dlkyr til å pipe data rett i en pca

```{r}

penguin.pca <- penguins %>%
  filter(!is.na(sex))%>%
  select(bill_length_mm:body_mass_g) %>%
  prcomp()


penguin.pca$x %>%
  ggplot(aes(x=PC1, y=PC2, color = penguins %>%  filter(!is.na(sex)) %>%pluck("species") ))+
  geom_point()

penguin.pca$rotation %>%
  ggplot(aes(x=PC1, y=PC2, label=row.names(penguin.pca$rotation)))+
  geom_text()

penguins %>%
  ggplot(aes(x=flipper_length_mm, y=bill_depth_mm
             , colour = species
))+
  geom_point()+
  geom_smooth(method = "lm", se = FALSE)


```

