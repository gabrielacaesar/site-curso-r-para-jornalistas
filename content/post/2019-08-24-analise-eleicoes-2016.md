---
title: Análise - Eleições 2016
author: Gabriela Caesar
date: '2019-08-24'
slug: analise-eleicoes-2016
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 7
---

instalar as bibliotecas
```{r}
install.packages("tidyverse")
install.packages("data.table")
```
carregar as bibliotecas
```{r}
library(tidyverse)
library(data.table)
```
pasta em que estão os dados
```{r}
setwd("~/Downloads/consulta_cand_2016_15ago2019")
```
dados completos de candidaturas

```{r}
consulta_cand_BR <- fread("consulta_cand_2016_BRASIL.csv")
```
dados de prefeito
```{r}
consulta_prefeito <- consulta_cand_BR %>%
  filter(DS_CARGO == "PREFEITO") %>%
  select(SG_UF, SG_UE, NM_UE, NM_CANDIDATO,
         NM_URNA_CANDIDATO, SG_PARTIDO, DS_SIT_TOT_TURNO,
         DS_COMPOSICAO_COLIGACAO) %>%
  rename("NM_PREFEITO" =  NM_CANDIDATO,
         "NM_URNA_PREFEITO" = NM_URNA_CANDIDATO,
         "SG_PARTIDO_PREFEITO" = SG_PARTIDO,
         "DS_SIT_TOT_TURNO_PREFEITO" = DS_SIT_TOT_TURNO)
```
dados de vice-prefeito
```{r}
consulta_vice_prefeito <- consulta_cand_BR %>%
  filter(DS_CARGO == "VICE-PREFEITO") %>%
  select(SG_UF, SG_UE, NM_UE, NM_CANDIDATO,
         NM_URNA_CANDIDATO, SG_PARTIDO, DS_SIT_TOT_TURNO,
         DS_COMPOSICAO_COLIGACAO) %>%
  rename("NM_VICE_PREFEITO" =  NM_CANDIDATO,
         "NM_URNA_VICE_PREFEITO" = NM_URNA_CANDIDATO,
         "SG_PARTIDO_VICE_PREFEITO" = SG_PARTIDO,
         "DS_SIT_TOT_TURNO_VICE_PREFEITO" = DS_SIT_TOT_TURNO)
```
cruzamento do dados de prefeito e vice-prefeito
```{r}
consulta_merged <- consulta_prefeito %>%
  left_join(consulta_vice_prefeito, 
            by = c("SG_UF", "NM_UE", "DS_COMPOSICAO_COLIGACAO"))
```
quais são as chapas mais frentes?
identificamos que chapas 'puro sangue' são as mais frequentes
```{r}
consulta_merged_partido <- consulta_merged %>%
  select(SG_PARTIDO_PREFEITO, SG_PARTIDO_VICE_PREFEITO) %>%
  unite(c(SG_PARTIDO_PREFEITO, SG_PARTIDO_VICE_PREFEITO), col = "chapa", sep = " - ") %>%
  group_by(chapa) %>%
  summarise(int = n()) %>%
  arrange(desc(int))
```
a quais chapas o PSL fez parte?
identificamos ainda PT-PSL 15 vezes e PSL-PT 3 vezes
```{r}
consulta_partido_psl <- consulta_merged_partido %>%
  filter(str_detect(chapa, "PSL"))
```
quais foram as chapas PSL-PT e PT-PSL?
```{r}
consulta_chapa_psl <- consulta_merged %>%
  filter(SG_PARTIDO_PREFEITO == "PSL" & SG_PARTIDO_VICE_PREFEITO == "PT" |
         SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "PSL")
```
em quais casos o PT é cabeça de chapa?
identificamos PT-PSDB 12 vezes e PT-DEM 5 vezes
```{r}
consulta_partido_pt <- consulta_merged_partido %>%
  filter(str_detect(chapa, "PT - "))
```
quais foram as chapas PT-PSDB e PT-DEM?
```{r}
consulta_chapa_pt <- consulta_merged %>%
  filter(SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "PSDB" |
           SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "DEM")
```

