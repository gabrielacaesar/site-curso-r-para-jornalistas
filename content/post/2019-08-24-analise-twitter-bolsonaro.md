---
title: Análise - Twitter Bolsonaro
author: Gabriela Caesar
date: '2019-08-24'
slug: analise-twitter-bolsonaro
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 5
---
Nesta primeira análise, nós vamos mexer com os dados do Twitter do Bolsonaro. Os dados foram extraídos do perfil do presidente na rede social.

#### instalar as bibliotecas
Abaixo, nós instalamos (caso seja necessário) os pacotes que serão usados nesta análise com a função `install.packages()`.
```{r}
install.packages("tidyverse")
install.packages("data.table")
```

#### ler as bibliotecas
Em seguida, nós carregamos os pacotes com a função `library()`. Sempre quando começar uma nova análise, precisamos carregar os pacotes.

```{r}
library(tidyverse)
library(data.table)
```

#### ler o arquivo
Para ler o arquivo (que nesta vez está em um link), nós usamos a função `fread()`, que faz parte do pacote data.table. Poderíamos ter lido o arquivo com outras funções, mas escolhemos a `fread()`.

```{r}
bolsonaro_fav <- fread("https://github.com/gabrielacaesar/curso-r-para-jornalistas/raw/master/data/jairbolsonaro-twitter/BolsonaroFavorites.csv",
                       encoding = "UTF-8")
```
#### qual tweet 'favoritado' teve mais RTs?
Abaixo, nós criamos um novo arquivo chamado "fav_RTs" que é feito com base no arquivo que criamos anteriormente. 

O arquivo "fav_RTs" é ordenado, em ordem decrescente, considerando os números da coluna "retweets_count" (que informa o número de vezes em que o tweet foi retuítado). Para isso usamos, as funções `arrange(desc)`.

Depois, nós apenas perguntamos qual é o conteúdo do tweet (coluna "tweet") desse arquivo "fav_RTs" na primeira linha. E também qual é o usuário (coluna "username") desse mesmo arquivo na primeira linha.

```{r}
fav_RTs <- bolsonaro_fav %>%
  arrange(desc(retweets_count))

fav_RTs$tweet[1]
fav_RTs$username[1]
```
#### qual tweet 'favoritado' teve mais replies?

Agora, nós queremos descobrir qual foi o tweet 'favoritado' que teve mais replies. Para isso, nós criamos outro arquivo, chamado de "fav_Replies", que é o arquivo original com uma nova ordenação. Desta vez, o arquivo é ordenado considerando a coluna "replies_count", em ordem decrescente. 

Da mesma forma, queremos saber, em seguida, qual é o primeiro tweet do nosso arquivo e qual é o usuário responsável.

```{r}
fav_Replies <- bolsonaro_fav %>%
  arrange(desc(replies_count))

fav_Replies$tweet[1]
fav_Replies$username[1]
```
#### qual tweet 'favoritado' teve mais likes?

Fazemos agora o mesmo procedimento só que com a coluna "likes_count".

```{r}
fav_Likes <- bolsonaro_fav %>%
  arrange(desc(likes_count))

fav_Likes$tweet[1]
fav_Likes$username[1]
```
#### quais foram as pessoas mais 'favoritadas'?
Por fim, agora queremos saber quais são as pessoas mais favoritados por Bolsonaro. Por isso, criamos um novo arquivo chamado "fav_Users", que é feito com base no arquivo original. Primeiro, agrupamos os dados por usuário (coluna "username"), com a função `group_by()`. 

Em seguida, contamos o número de vezes em que cada usuário aparece, com a função `summarise()` e `n()`. 

Por fim, colocamos tudo em ordem decrescente (`arrange(desc)`), considerando a coluna que conta o número de vezes em que cada usuário aparece (coluna "int"). Também pedimos para ver apenas as cinco primeiras linhas do arquivo, com a função `head()`.

```{r}
fav_Users <- bolsonaro_fav %>%
  group_by(username) %>%
  summarise(int = n()) %>%
  arrange(desc(int))

head(fav_Users)
```
