---
title: Análise - Mega Sena
author: Gabriela Caesar
date: '2019-10-27'
slug: analise-mega-sena
categories: []
tags: [r, rstudio, openxlx, base, tidyverse]
weight: 6
---

O objetivo desta análise é identificar quais foram os números mais frequentes nos sorteios da Mega Sena.

#### instalar as bibliotecas
Caso você ainda não tenha instalado os pacotes abaixo, você precisa rodar esse código abaixo, que usa a função `install.packages()`.

```{r}
install.packages("tidyverse")
install.packages("openxlsx")
```
#### carregar as bibliotecas
Em seguida, precisamos carregar os pacotes que serão usados na análise com a função `library()`.

```{r}
library(tidyverse)
library(openxlsx)
```

#### baixar os dados
link para o download: http://www1.caixa.gov.br/loterias/_arquivos/loterias/D_megase.zip    
converter os dados em algum site para CSV ou Excel    
como este: http://www.convertcsv.com/html-table-to-csv.htm    
abrir no Excel para deletar erros, como linhas apenas com o nome da UF

#### definir o diretório/pasta
Para importar o arquivo do TSE, informamos onde está o arquivo. Neste caso, o arquivo está na pasta “Downloads”.

Isso vai de computador para computador. O arquivo XLSX está na pasta "Downloads".
```{r}
setwd("~/Downloads/")
```
#### ler o arquivo
Importamos o nosso arquivo XLSX.

```{r}
megasena <- read.xlsx("megasena.xlsx")
```
#### quando foi o primeiro sorteiro do nosso arquivo?
Criamos um novo arquivo chamado "megasena_df", criado com base no arquivo "megasena". Logo depois, usamos a função `separate()` para separar o conteúdo da coluna "Data.Sorteio" em mais colunas, considerando o separador "/". 

As novas colunas criadas recebem os nomes de "dia", "mes" e "ano". Como queremos manter a coluna "Data.Sorteio" também em seu formato original, nós adicionamos "remove = FALSE".

Por fim, usamos o `arrange()` para ordenar os dados considerando as colunas "mes" e "ano".

```{r}
megasena_df <- megasena %>%
  separate(Data.Sorteio, c("dia", "mes", "ano"), sep = "/", remove = FALSE) %>%
  arrange(mes, ano)
```
#### quando foi o último sorteio do nosso arquivo?
Agora apenas reordenamos o arquivo em ordem decrescente, considerando a coluna "ano".

```{r}
megasena_df <- megasena_df %>%
  arrange(desc(ano))
```
#### em qual concurso mais pessoas foram premiadas?
Desta vez, queremos transformar os dados da coluna "Ganhadores_Sena" em número e usamos as funções `mutate()` e `as.numeric()`. Depois também reordenamos com `arrange()` e `desc()`.
```{r}
megasena_df <- megasena_df %>%
  mutate(Ganhadores_Sena = as.numeric(Ganhadores_Sena)) %>%
  arrange(desc(Ganhadores_Sena))
```
#### quantas vezes a mega-sena acumulou?
Para descobrirmos quantas vezes a Mega Sena acumulou, nós vamos agrupar os dados considerando a coluna "Acumulado", com a função `group_by()`. Também queremos contar quantas linhas aparecem em cada variável da coluna "Acumulado" e, por isso, usamos também `summarise()` e `n()`.

```{r}
megasena_acumulada <- megasena_df %>%
  group_by(Acumulado) %>%
  summarise(int = n())
```
#### ver quais foram números mais sorteados na primeira bola
A partir de agora, começamos a missão para descobrir quais foram os números mais sorteados nos concursos da Mega Sena. Vamos agrupar os dados apenas da coluna "1ª.Dezena", que tem os primeiros números sorteados em cada concurso. Depois, vamos contar quantas vezes cada número apareceu. E depois vamos renomear a coluna para "n_sorteado".
```{r}
megasena_frequentes_1d <- megasena_df %>%
  group_by(`1ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `1ª.Dezena`)
```
#### ver quais foram números mais sorteados na segunda bola
Faremos o mesmo procedimento anterior, só que com as demais colunas. Ou seja, com os dados da segunda bola sorteada. Depois, com os dados da terceira bola sorteada, da quarta bola sorteada etc. 

```{r}
megasena_frequentes_2d <- megasena_df %>%
  group_by(`2ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `2ª.Dezena`)
```
#### ver quais foram números mais sorteados na terceira bola
```{r}
megasena_frequentes_3d <- megasena_df %>%
  group_by(`3ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `3ª.Dezena`)
```
#### ver quais foram números mais sorteados na quarta bola
```{r}
megasena_frequentes_4d <- megasena_df %>%
  group_by(`4ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `4ª.Dezena`)
```
#### ver quais foram números mais sorteados na quinta bola
```{r}
megasena_frequentes_5d <- megasena_df %>%
  group_by(`5ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `5ª.Dezena`)
```
#### ver quais foram números mais sorteados na sexta bola
```{r}
megasena_frequentes_6d <- megasena_df %>%
  group_by(`6ª.Dezena`) %>%
  summarise(int = n()) %>%
  rename(n_sorteado = `6ª.Dezena`)
```
#### empilhar todos os arquivos acima
Agora que nós já temos os números mais frequentes sorteados na primeira, na segunda, na terceira, na quarta, na quinta e na sexta bola do concurso. Os cabeçalhos desses arquivos são iguais. E agora vamos empilhar esses arquivos com a função `rbind()`. 

```{r}
megasena_frequentes <- rbind(megasena_frequentes_1d, 
                             megasena_frequentes_2d, 
                             megasena_frequentes_3d, 
                             megasena_frequentes_4d,
                             megasena_frequentes_5d, 
                             megasena_frequentes_6d)
```
#### agrupar por número sorteado e somar a frequência
O nosso novo arquivo tem os dados de números mais frequentes em cada bola. Mas queremos saber no geral quais foram os números mais frequentes. Por isso, vamos agrupar os dados novamente, com a função `group_by()`. 

Depois, usamos ainda `summarise()` e `sum()`. Usamos aqui o `sum()` porque queremos somar os valores da coluna int, e não apenas contar o número de linhas. Depois, reordenamos os dados, com a função `arrange()`, para mostrar em ordem decrescente, com a função `desc()`.
```{r}
megasena_frequentes <- megasena_frequentes %>%
  group_by(n_sorteado) %>%
  summarise(int = sum(int)) %>%
  arrange(desc(int))
```


