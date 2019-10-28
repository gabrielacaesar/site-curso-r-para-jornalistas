---
title: Análise - Eleições 2016
author: Gabriela Caesar
date: '2019-10-27'
slug: analise-eleicoes-2016
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 7
---

Esta análise usará os dados eleições de 2016 para mostrar algumas possibilidades do R. O download do arquivo usado abaixo pode ser feito no [repositório de dados do TSE](http://www.tse.jus.br/eleicoes/estatisticas/repositorio-de-dados-eleitorais-1/repositorio-de-dados-eleitorais). O caminho neste link é "Candidatos > 2016 > Candidatos (formato ZIP)".

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
#### pasta em que estamos agora
Queremos saber em qual pasta estamos agora para, se necessário, mudar a pasta. Por isso, vamos usar a função `setwd()`. 
```{r}
getwd()
```

#### pasta que queremos acessar
Para importar o arquivo do TSE, informamos onde está o arquivo. Neste caso, o arquivo está no caminho "Downloads > consulta_cand_2016_24out2019". Queremos acessar essa pasta e usamos a função `setwd()`.

Isso vai de computador para computador. O nome da pasta "consulta_cand_24out2019" foi escolhido por mim, e o arquivo CSV está lá dentro.
```{r}
setwd("~/Downloads/consulta_cand_2016_24out2019")
```

#### lista de arquivos na pasta
Usamos a função `list.files()` para checar quais arquivos estão na pasta em que queremos trabalhar.
```{r}
list.files()
```

#### importar o arquivo
Depois, usamos a função `fread()` para importar o arquivo. O nome do arquivo precisa estar entre aspas, como mostramos abaixo, e também deve informar o formato da extensão do arquivo (no caso, CSV).

```{r}
consulta_cand_BR <- fread("consulta_cand_2016_BRASIL.csv", encoding = "Latin-1")
```
#### abrir o arquivo no RStudio
A função `View()` abre o arquivo em uma das abas do RStudio e te permite, por exemplo, olhar o arquivi e também buscar por termos. 

```{r}
View(consulta_cand_BR)
```

#### ver as cincos primeiras linhas
A função `head()` mostra, por padrão, as cinco primeiras linhas do arquivo lido. Também possível informar que queremos ver, por exemplo, as oito primeiras linhas do arquivo lido, com `head(consulta_cand_BR, 8)`.

```{r}
head(consulta_cand_BR)
```
#### conferir o arquivo
A função `summary()` é outra função para conseguir algumas informações sobre o arquivo.

```{r}
summary(consulta_cand_BR)
```

#### mostrar as correspondências únicas
A função `unique()` mostra as correspondências únicas de determinada coluna.
```{r}
unique(consulta_cand_BR$DS_CARGO)
```

#### mostrar o tamanho
A função `length()` mostra o tamanho de determinada coluna.
```{r}
length(consulta_cand_BR$DS_CARGO)

length(unique(consulta_cand_BR$DS_CARGO))
```

#### filtrar o arquivo
Usamos a função `filter()` para filtrar o arquivo, já que só queremos os dados referentes a candidatos a prefeito. Abaixo, filtramos a coluna "DS_CARGO" por "PREFEITO" (ou seja, não entram os vice-prefeitos e os vereadores);
```{r}
consulta_prefeito <- consulta_cand_BR %>%
  filter(DS_CARGO == "PREFEITO")
```

#### selecionar as colunas
Como não queremos trabalhar com um arquivo muito grande, nós selecionamos quais colunas queremos manter no arquivo. Para isso, usamos a função `select()`. 
```{r}
consulta_prefeito <- consulta_prefeito %>%
  select(SG_UF, SG_UE, NM_UE, NM_CANDIDATO,
         NM_URNA_CANDIDATO, SG_PARTIDO, DS_SIT_TOT_TURNO,
         DS_COMPOSICAO_COLIGACAO)
```
#### renomear cabeçalho
Também renomeamos os nomes de algumas colunas do arquivo (o novo nome aparece primeiro e precisa estar entre aspas). Usamos a função `rename()`.

```{r}
consulta_prefeito <- consulta_prefeito %>%
  rename("NM_PREFEITO" =  NM_CANDIDATO,
         "NM_URNA_PREFEITO" = NM_URNA_CANDIDATO,
         "SG_PARTIDO_PREFEITO" = SG_PARTIDO,
         "DS_SIT_TOT_TURNO_PREFEITO" = DS_SIT_TOT_TURNO)
```

#### contar o número de candidatos a prefeito por partido
Usamos as funções `group_by()` e `summarise()` para contabilizar quantos candidatos a prefeito cada partido teve. Por isso, inserimos a coluna "SG_PARTIDO_PREFEITO" dentro de `group_by()`. E pedimos, com `n()`, para contar em quantas linhas cada partido aparece.

```{r}
cand_prefeito_por_partido <- consulta_prefeito %>%
  group_by(SG_PARTIDO_PREFEITO) %>%
  summarise(int = n())
```
#### contar o número de candidatos a prefeito, vice-prefeito e vereador por partido

Usamos as funções `group_by()` e `summarise()` para contabilizar quantos candidatos a cada cargo cada partido teve. Agora, repare, inserimos tanto "DS_CARGO" quanto "SG_PARTIDO" dentro de `group_by()`. Novamente, usamos o `summarise()` e o `n()` para contar o número de linhas, já que cada linha é um candidato.

```{r}
cand_por_partido <- consulta_cand_BR %>%
  group_by(DS_CARGO, SG_PARTIDO) %>%
  summarise(int = n())
  
View(cand_por_partido)
```

#### spread
Agora, queremos que cada linha seja um partido e que os cargos eletivos sejam as colunas. Os valores das colunas devem ser a soma da quantidade de candidatos. Usamos a função `spread()`.
```{r}
cand_por_partido_new <- cand_por_partido %>%
  spread(DS_CARGO, int)

View(cand_por_partido_new)  
```

#### gather
Já se quiséssemos voltar a como estava antes noós usamos a função `gather()`.
```{r}
cand_por_partido_new <- cand_por_partido_new %>%
  gather("VEREADOR", "PREFEITO", "VICE-PREFEITO")
  
View(cand_por_partido_new)
```

#### tirar acentuação de nomes
Também podemos criar uma nova coluna no arquivo que contenha os nomes dos candidatos (NM_CANDIDATO) sem os acentos. Para isso, usamos a função `rm_accent()` do pacote `abjutils`. Usamos o `mutate()` para criar a nova coluna.
```{r}
library(abjutils)

novo_consulta_cand_BR <- consulta_cand_BR %>%
  mutate(NM_CANDIDATO_SEM_ACENTO = rm_accent(NM_CANDIDATO))

```
#### criar nova coluna
Aqui, novamente usamos o `mutate()` e informamos que a nossa nova coluna se chama "coluna_zoada" e tem o conteúdo "    Eu me chamo Gabriela. " em todas as linhas.
```{r}
novo_consulta_cand_BR <- novo_consulta_cand_BR %>%
  mutate(coluna_zoada = "    Eu me chamo Gabriela. ")

novo_consulta_cand_BR$coluna_zoada
```

#### tirar espaços excedentes
A coluna "coluna_zoada" tem vários espaços desnecessários que atrapalham o seu conteúdo. Para resolver esse problema, vamos usar a função `str_trim()`.
```{r}
novo_consulta_cand_BR <- novo_consulta_cand_BR %>%
  mutate(coluna_zoada = str_trim(coluna_zoada))

novo_consulta_cand_BR$coluna_zoada

```
#### deletar coluna
Para apagar a coluna, podemos fazer assim:
```{r}
novo_consulta_cand_BR$coluna_zoada <- NULL
```
#### substituir texto
Também podemos usar `str_replace()` ou `str_replace_all()` para fazer substituições no arquivo. Por exemplo, queremos substituir "AVANTE" por "Avante". 
```{r}
novo_consulta_cand_BR <- novo_consulta_cand_BR %>%
  mutate(SG_PARTIDO = str_replace_all(SG_PARTIDO, "AVANTE", "Avante"))

unique(novo_consulta_cand_BR$SG_PARTIDO)
```
#### remover texto
Nessa mesma coluna (SG_PARTIDO), nós queremos apagar os espacços que aparecem em alguns partidos, como "PC do B" e "PT do B". Por isso, vamos usar `str_remove_all()`. 
```{r}
novo_consulta_cand_BR <- novo_consulta_cand_BR %>%
  mutate(SG_PARTIDO = str_remove_all(SG_PARTIDO, " "))

unique(novo_consulta_cand_BR$SG_PARTIDO)
```

#### detectar texto
Usamos a função `str_detect()` para encontrar determinado texto. Por exemplo, na coluna "DS_COMPOSICAO_COLIGACAO" nós queremos filtrar apenas as linhas que contenham o texto "PSL". O "PSL" não aparece sozinho nas linhas, então o `str_detect()` nos ajuda nisso.

```{r}
consulta_cand_BR_PSL <- novo_consulta_cand_BR %>%
  filter(str_detect(DS_COMPOSICAO_COLIGACAO, "PSL"))

unique(consulta_cand_BR_PSL$DS_COMPOSICAO_COLIGACAO)
```

#### separar texto considerando delimitador
Se a gente quisesse saber quais candidatos nasceram em outubro, a gente poderia pegar o conteúdo da coluna DT_NASCIMENTO, que está no formato "dd/mm/YY", e pedir para separar as informações considerando o separador "/". A função para isso se chama `separate()`. Se quisermos manter a coluna original, podemos incluir `remove = FALSE`.

```{r}
novo_consulta_cand_BR_out <- novo_consulta_cand_BR %>%
  separate(DT_NASCIMENTO, c("dia",  "mes", "ano"), sep = "/", remove = FALSE) %>%
  filter(mes == "10")
  
unique(novo_consulta_cand_BR_out$mes)
  
```

#### unir texto considerando delimitador
Da mesma forma que nós podemos separar, nós também podemos unir. A função se chama `unite()`. Por exemplo, queremos uma coluna que tenha tanto o nome do munícipio quanto a sigla da UF. Queremos algo assim: "Angra dos Reis - RJ"; "Conde - BA"; "Santos - SP".
```{r}

novo_consulta_cand_BR_cidade <- novo_consulta_cand_BR %>%
  unite("cidade_e_UF", c("NM_UE", "SG_UF"), sep = " - ")

unique(novo_consulta_cand_BR_cidade$cidade_e_UF)

```
#### cruzar arquivos considerando colunas em comum
Esta etapa é bem importante. Nós agora vamos cruzar, com `left_join()`, os dois arquivos criados abaixo ("consulta_prefeito" e "consulta_vice_prefeito"). O cruzamento desses arquivos se chamará "consulta_merged". 

Para o cruzamento, não vamos levar em conta apenas uma coluna, mas sim três colunas. Para haver correspondência de um arquivo com o outro, ambos devem ter valores iguais nas colunas "SG_UF", "NM_UE", "DS_COMPOSICAO_COLIGACAO". 

Assim, teremos em apenas uma coluna informações sobre os candidatos a prefeito e vice-prefeito que estão na mesma chapa, na mesma cidade e na mesma UF.

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

consulta_vice_prefeito <- consulta_cand_BR %>%
  filter(DS_CARGO == "VICE-PREFEITO") %>%
  select(SG_UF, SG_UE, NM_UE, NM_CANDIDATO,
         NM_URNA_CANDIDATO, SG_PARTIDO, DS_SIT_TOT_TURNO,
         DS_COMPOSICAO_COLIGACAO) %>%
  rename("NM_VICE_PREFEITO" =  NM_CANDIDATO,
         "NM_URNA_VICE_PREFEITO" = NM_URNA_CANDIDATO,
         "SG_PARTIDO_VICE_PREFEITO" = SG_PARTIDO,
         "DS_SIT_TOT_TURNO_VICE_PREFEITO" = DS_SIT_TOT_TURNO)

consulta_merged <- consulta_prefeito %>%
  left_join(consulta_vice_prefeito, 
            by = c("SG_UF", "NM_UE", "DS_COMPOSICAO_COLIGACAO"))

consulta_chapa_psl_pt <- consulta_merged %>%
  filter(SG_PARTIDO_PREFEITO == "PSL" & SG_PARTIDO_VICE_PREFEITO == "PT" |
         SG_PARTIDO_PREFEITO == "PT" & SG_PARTIDO_VICE_PREFEITO == "PSL")

consulta_chapa_psl_pt_eleita <- consulta_chapa_psl_pt %>%
  filter(DS_SIT_TOT_TURNO_PREFEITO == "ELEITO")
```

#### fazer o download de CSV
Para baixar a versão final do nosso arquivo, nós podemos fazer assim, com a função `download.csv()`:
```{r}
download.csv(consulta_chapa_psl_pt_eleita, "consulta_chapa_psl_pt_eleita-27out2019.csv")
```

