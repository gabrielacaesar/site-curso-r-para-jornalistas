---
title: Verificação de arquivo
author: Gabriela Caesar
date: '2019-08-20'
slug: verificacao-de-arquivo
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 2
---
  
#### Mostrar cabeçalho do arquivo
A função `colnames()` mostra o cabeçalho do arquivo que já foi lido no tutorial anterior. 

```{r}
colnames()
```
#### Mostrar as cinco linhas iniciais do arquivo
A função `head()` mostra as cinco primeiras linhas do arquivo lido.

```{r}
head()
```

#### Mostrar as x linhas iniciais do arquivo - no caso, 15
Também podemos definir no `head()` o número de linhas que desejamos que apareça. No caso abaixo, 15 linhas.

```{r}
head()
```

#### Mostrar as cinco linhas finais do arquivo
A função `tail()` mostra as cinco últimas linhas do arquivo lido.

```{r}
tail()
```

#### Mostrar as x linhas finais do arquivo, no caso 15
Também na função `tail()` podemos definir quantas linhas no fim do arquivo desejamos ver.

```{r}
tail()
```

