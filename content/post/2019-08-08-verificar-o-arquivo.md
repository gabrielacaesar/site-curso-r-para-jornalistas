---
title: Verificação de arquivo
author: Gabriela Caesar
date: '2019-10-27'
slug: verificacao-de-arquivo
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 4
---

#### ler o arquivo
A `fread()` é uma das funções possíveis para ler o arquivo. 

```{r}
library(data.table)
arquivo <- fread("https://raw.githubusercontent.com/gabrielacaesar/curso-r-para-jornalistas/master/data/jairbolsonaro-twitter/BolsonaroFollowing.csv")
```
  
#### mostrar cabeçalho do arquivo
A função `colnames()` mostra o cabeçalho do arquivo que já foi lido no tutorial anterior. 

```{r}
colnames(arquivo)
```
#### mostrar as cinco linhas iniciais do arquivo
A função `head()` mostra, por padrão, as cinco primeiras linhas do arquivo lido.

```{r}
head(arquivo)
```

#### mostrar as linhas iniciais do arquivo - no caso, 15
Também podemos definir no `head()` o número de linhas que desejamos que apareça. No caso abaixo, 15 linhas.

```{r}
head(arquivo, 15)
```

#### mostrar as cinco linhas finais do arquivo
A função `tail()` mostra, por padrão, as cinco últimas linhas do arquivo lido.

```{r}
tail(arquivo)
```

#### mostrar as linhas finais do arquivo, no caso 15
Também na função `tail()` podemos definir quantas linhas no fim do arquivo desejamos ver.

```{r}
tail(arquivo, 15)
```

#### abrir o arquivo no RStudio
A função `View()` abre o arquivo em uma das abas do RStudio e te permite, por exemplo, olhar o arquivi e também buscar por termos. 

```{r}
View(arquivo)
```

#### mostrar resumo do arquivo
A função `glimpse()` é do pacote `dplyr` e te dá informações sobre o arquivo.
```{r}
library(dplyr)
glimpse(arquivo)
```

#### mostrar resumo do arquivo
A função `str()` também dá algumas informações sobre o arquivo.
```{r}
str(arquivo)
```

#### mostrar dados matemáticos das colunas
A função `summary()` é outra função para conseguir algumas informações sobre o arquivo.
```{r}
summary(arquivo)
```

#### mostrar as correspondências únicas
A função `unique()` mostra as correspondências únicas de determinada coluna.
```{r}
unique(arquivo$username)
```

#### mostrar o tamanho
A função `length()` mostra o tamanho de determinada coluna.
```{r}
length(arquivo$username)
```

#### mostrar 
A função `typeof()` xxxx
```{r}
typeof(arquivo)
```

#### mostrar conteúdo de linha informada
Se quisermos xxx, basta colocarmos o nome do arquivo e, logo depois, o número da linha entre colchetes. Fica assim: `df[1]`
```{r}
arquivo[1]
```

#### mostrar conteúdo de coluna informada
Na mesma forma, podemos repetir esse procedimento e ainda delimitar também a coluna. Fica assim: `df$column[1]`
```{r}
arquivo$username[1]
```

#### fazer o download em CSV
A função `write.csv()` faz o download do arquivo em formato CSV.
```{r}
download.csv(arquivo, "arquivo.csv")
```

