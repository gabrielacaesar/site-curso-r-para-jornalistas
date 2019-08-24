---
title: Análise - Twitter Bolsonaro
author: Gabriela Caesar
date: '2019-08-24'
slug: analise-twitter-bolsonaro
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 5
---

instalar as bibliotecas
```{r}
install.packages("tidyverse")
install.packages("data.table")
```

ler as bibliotecas
```{r}
library(tidyverse)
library(data.table)
```

ler o arquivo
```{r}
bolsonaro_fav <- fread("https://github.com/gabrielacaesar/curso-r-para-jornalistas/raw/master/data/jairbolsonaro-twitter/BolsonaroFavorites.csv",
                       encoding = "UTF-8")
```

qual tweet 'favoritado' teve mais RTs?
```{r}
fav_RTs <- bolsonaro_fav %>%
  arrange(desc(retweets_count))

fav_RTs$tweet[1]
fav_RTs$username[1]
```
qual tweet 'favoritado' teve mais replies?
```{r}
fav_Replies <- bolsonaro_fav %>%
  arrange(desc(replies_count))

fav_Replies$tweet[1]
fav_Replies$username[1]
```
qual tweet 'favoritado' teve mais likes?
```{r}
fav_Likes <- bolsonaro_fav %>%
  arrange(desc(likes_count))

fav_Likes$tweet[1]
fav_Likes$username[1]
```
quais foram as pessoas mais 'favoritadas'?
```{r}
fav_Users <- bolsonaro_fav %>%
  group_by(username) %>%
  summarise(int = n()) %>%
  arrange(desc(int))

head(fav_Users)
```
