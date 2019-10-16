---
title: Verificação de arquivo
author: Gabriela Caesar
date: '2019-10-16'
slug: verificacao-de-arquivo
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 2
---

#### Ler o arquivo
A `fread()` é uma das funções possíveis para ler o arquivo. 

```{r}
library(data.table)
arquivo <- fread("https://raw.githubusercontent.com/gabrielacaesar/curso-r-para-jornalistas/master/data/jairbolsonaro-twitter/BolsonaroFollowing.csv")
```
  
#### Mostrar cabeçalho do arquivo
A função `colnames()` mostra o cabeçalho do arquivo que já foi lido no tutorial anterior. 

```{r}
colnames(arquivo)
```
#### Mostrar as cinco linhas iniciais do arquivo
A função `head()` mostra as cinco primeiras linhas do arquivo lido.

```{r}
head(arquivo)
```

#### Mostrar as linhas iniciais do arquivo - no caso, 15
Também podemos definir no `head()` o número de linhas que desejamos que apareça. No caso abaixo, 15 linhas.

```{r}
head(arquivo, 15)
```

#### Mostrar as cinco linhas finais do arquivo
A função `tail()` mostra as cinco últimas linhas do arquivo lido.

```{r}
tail(arquivo)
```

#### Mostrar as linhas finais do arquivo, no caso 15
Também na função `tail()` podemos definir quantas linhas no fim do arquivo desejamos ver.

```{r}
tail(arquivo, 15)
```

#### Abrir o arquivo
A função View()

```{r}
View(arquivo)
```

#### Mostrar resumo do arquivo
A função glimpse()
```{r}
library(dplyr)
glimpse(arquivo)
```

#### Mostrar resumo do arquivo
A função str()
```{r}
str(arquivo)
```

#### Mostrar dados matemáticos das colunas
A função summary()
```{r}
summary(arquivo)
```

#### Mostrar as correspondências únicas
A função unique()
```{r}
unique(arquivo)
```

#### Mostrar o tamanho
A função length()
```{r}
length(arquivo)
```

#### Mostrar
A função which()
```{r}

```

#### Mostrar 
A função typeof()
```{r}
typeof(arquivo)
```

#### Mostrar o número de linhas
A função nrow()
```{r}

```

#### Mostrar conteúdo de linha informada
Se quisermos xxx, basta colocarmos o nome do arquivo e, logo depois, o número da linha entre colchetes. Fica assim: df[1]
```{r}
arquivo[1]
```

#### Mostrar conteúdo de coluna informada
Na mesma forma, podemos repetir esse procedimento e ainda delimitar também a coluna. Fica assim: df$column[1]
```{r}
arquivo$username[1]
```

#### Fazer o download em CSV
A função write.csv()
```{r}

```

#### Fazer o download em XLSX
A função write.xlsx2()
```{r}

```

