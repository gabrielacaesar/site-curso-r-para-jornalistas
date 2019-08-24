---
title: Análise - Mega Sena
author: ~
date: '2019-08-24'
slug: analise-mega-sena
categories: []
tags: [r, rstudio, openxlx, base, tidyverse]
weight: 6
---

instalar as bibliotecas
```{r}
install.packages("tidyverse")
install.packages("openxlsx")
```
carregar as bibliotecas
```{r}
library(tidyverse)
library(openxlsx)
```

baixar os dados
link para o download: http://www1.caixa.gov.br/loterias/_arquivos/loterias/D_megase.zip
converter os dados em algum site para CSV ou Excel
como este: http://www.convertcsv.com/html-table-to-csv.htm
abrir no Excel para deletar erros, como linhas apenas com o nome da UF

definir o diretório/pasta
```{r}
setwd("~/Downloads/")
```
ler o arquivo
```{r}
megasena <- read.xlsx("megasena.xlsx")
```
quando foi o primeiro sorteiro do nosso arquivo?
```{r}
megasena_df <- megasena %>%
  separate(Data.Sorteio, c("dia", "mes", "ano"), sep = "/", remove = FALSE) %>%
  arrange(mes, ano)
```
quando foi o último sorteio do nosso arquivo?
```{r}
megasena_df <- megasena_df %>%
  arrange(desc(ano))
```
em qual concurso mais pessoas foram premiadas?
```{r}
megasena_df <- megasena_df %>%
  mutate(Ganhadores_Sena = as.numeric(Ganhadores_Sena)) %>%
  arrange(desc(Ganhadores_Sena))
```
quantas vezes a mega-sena acumulou?
```{r}
megasena_acumulada <- megasena_df %>%
  group_by(Acumulado) %>%
  summarise(int = n())
```
ver quais foram números mais sorteados na primeira bola
```{r}
megasena_frequentes_1d <- megasena_df %>%
  group_by(`1ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `1ª.Dezena`)
```
ver quais foram números mais sorteados na segunda bola
```{r}
megasena_frequentes_2d <- megasena_df %>%
  group_by(`2ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `2ª.Dezena`)
```
ver quais foram números mais sorteados na terceira bola
```{r}
megasena_frequentes_3d <- megasena_df %>%
  group_by(`3ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `3ª.Dezena`)
```
ver quais foram números mais sorteados na quarta bola
```{r}
megasena_frequentes_4d <- megasena_df %>%
  group_by(`4ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `4ª.Dezena`)
```
ver quais foram números mais sorteados na quinta bola
```{r}
megasena_frequentes_5d <- megasena_df %>%
  group_by(`5ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `5ª.Dezena`)
```
ver quais foram números mais sorteados na sexta bola
```{r}
megasena_frequentes_6d <- megasena_df %>%
  group_by(`6ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `6ª.Dezena`)
```
empilhar todos os arquivos acima
```{r}
megasena_frequentes <- rbind(megasena_frequentes_1d, 
                             megasena_frequentes_2d, 
                             megasena_frequentes_3d, 
                             megasena_frequentes_4d,
                             megasena_frequentes_5d, 
                             megasena_frequentes_6d)
```
agrupar por número sorteado e somar a frequência
```{r}
megasena_frequentes <- megasena_frequentes %>%
  group_by(n_sorteado) %>%
  summarise(int = sum(int)) %>%
  arrange(desc(int))
```


