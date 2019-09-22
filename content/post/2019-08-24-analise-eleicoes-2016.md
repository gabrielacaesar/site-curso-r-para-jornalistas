---
title: Análise - Eleições 2016
author: Gabriela Caesar
date: '2019-08-24'
slug: analise-eleicoes-2016
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 7
---

Esta análise usará os dados eleições de 2016 para mostrar algumas possibilidades do R. O download do arquivo usado abaixo pode ser feito no repositório de dados do TSE. O caminho neste link é "Candidatos > 2016 > Candidatos (formato ZIP)".

#### instalar as bibliotecas
Caso você ainda não tenha instalado os pacotes abaixo, você precisa rodar esse código com a função `install.packages()`.

```{r}
install.packages("tidyverse")
install.packages("data.table")
```
#### carregar as bibliotecas
Em seguida, precisamos carregar os pacotes que serão usados na análise com a função `library()`.

```{r}
library(tidyverse)
library(data.table)
```
#### pasta em que estão os dados
Para importar o arquivo do TSE, informamos onde está o arquivo. Neste caso, o arquivo está no caminho "Downloads > consulta_cand_2016_15ago2019". 

Isso vai de computador para computador. O nome da pasta "consulta_cand_15ago2019" foi escolhido por mim, e o arquivo CSV está lá dentro.
```{r}
setwd("~/Downloads/consulta_cand_2016_15ago2019")
```
#### dados completos de candidaturas
Depois, usamos a função `fread()` para importar o arquivo. O nome do arquivo precisa estar entre aspas, como mostramos abaixo, e também deve informar o formato da extensão do arquivo (no caso, CSV).

```{r}
consulta_cand_BR <- fread("consulta_cand_2016_BRASIL.csv")
```
#### dados de prefeito
Como não queremos trabalhar com um arquivo muito grande, nós criamos um novo arquivo chamado "consulta_prefeito" em que:
- filtramos a coluna "DS_CARGO" por "PREFEITO" (ou seja, não entram os vice-prefeitos e os vereadores);
- selecionamos quais colunas queremos manter no arquivo;
- renomeamos algumas colunas do arquivo (o novo nome aparece primeiro e precisa estar entre aspas).

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
#### dados de vice-prefeito
E vamos o mesmo processo novamente, mas desta vez queremos manter os dados referentes aos vice-prefeitos e, por isso, já filtramos a coluna "DS_CARGO" por "VICE-PREFEITO". 

Da mesma forma, selecionamos apenas algumas colunas para diminuir o tamanho do arquivo. Renomeamos também as colunas, mas considerando que agora queremos informar já no cabeçalho que são dados de vice-prefeitos.

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
#### cruzamento do dados de prefeito e vice-prefeito
Esta etapa é bem importante. Nós agora vamos cruzar, com `left_join()`, os dois arquivos criados anteriormente ("consulta_prefeito" e "consulta_vice_prefeito"). O cruzamento desses arquivos se chamará "consulta_merged". 

Para o cruzamento, não vamos levar em conta apenas uma coluna, mas sim três colunas. Para haver correspondência de um arquivo com o outro, ambos devem ter valores iguais nas colunas "SG_UF", "NM_UE", "DS_COMPOSICAO_COLIGACAO". 

Assim, teremos em apenas uma coluna informações sobre os candidatos a prefeito e vice-prefeito que estão na mesma chapa, na mesma cidade e na mesma UF.

```{r}
consulta_merged <- consulta_prefeito %>%
  left_join(consulta_vice_prefeito, 
            by = c("SG_UF", "NM_UE", "DS_COMPOSICAO_COLIGACAO"))
```
#### quais são as chapas mais frentes?
Nós estamos interessados nos partidos dos candidatos às prefeituras (colunas "SG_PARTIDO_PREFEITO" e "SG_PARTIDO_VICE_PREFEITO"). Por isso, nós queremos um arquivo que tenha apenas esses dados. 

E, com esses dados, vamos criar uma coluna chama "chapa", que unirá esses dados de ambas as colunas, separando os partidos por "-".

Em seguida, vamos agrupar os dados por "chapa" e também contar quantas vezes cada chapa apareceu no arquivo. Queremos esses dados estejam ordenados, em ordem decrescente, pela coluna que mostra o número de ocorrências (coluna "int").

```{r}
consulta_merged_partido <- consulta_merged %>%
  select(SG_PARTIDO_PREFEITO, SG_PARTIDO_VICE_PREFEITO) %>%
  unite(c(SG_PARTIDO_PREFEITO, SG_PARTIDO_VICE_PREFEITO), col = "chapa", sep = " - ") %>%
  group_by(chapa) %>%
  summarise(int = n()) %>%
  arrange(desc(int))
```
#### a quais chapas o PSL fez parte?
Agora que sabemos quais foram as chapas mais frequentes em 2016, nós queremos saber em quais casos o PSL aparece. Há alguma chapa do PSL com o PT, por exemplo? O PSL aparece mais como candidato a prefeito nesses casos? Ou como candidato a vice-prefeito?

```{r}
consulta_partido_psl <- consulta_merged_partido %>%
  filter(str_detect(chapa, "PSL"))
```
#### quais foram as chapas PSL-PT e PT-PSL?
Para saber quais foram as chapas que apresentam a dobradinha PSL-PT ou PT-PSL, nós faremos um filtro no arquivo "consulta_merge". 

Queremos saber quais são as chapas em que há PSL como candidato a prefeito e PT como candidato a vice-prefeito. Ou PT como candidato a prefeito e PSL como candidato a vice-prefeito.
```{r}
consulta_chapa_psl <- consulta_merged %>%
  filter(SG_PARTIDO_PREFEITO == "PSL" & SG_PARTIDO_VICE_PREFEITO == "PT" |
         SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "PSL")
```
#### em quais casos o PT é cabeça de chapa?
Se quisermos saber quais foram as chapas em que o PT foi cabeça de chapa na disputa municipal de 2016, nós podemos usar novamente a função `str_detect()`. 

```{r}
consulta_partido_pt <- consulta_merged_partido %>%
  filter(str_detect(chapa, "PT - "))
```
#### quais foram as chapas PT-PSDB e PT-DEM?
Da mesma forma, podemos usar novamente o `filter()` para identificar chapas em que o PT tenha formado uma chapa com o PSDB ou com o DEM. 

```{r}
consulta_chapa_pt <- consulta_merged %>%
  filter(SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "PSDB" |
           SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "DEM")
```

