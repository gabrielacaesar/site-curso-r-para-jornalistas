---
title: Importação de arquivos
author: Gabriela Caesar
date: '2019-10-27'
slug: importacao-de-arquivos
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 2
---
  
#### identificar em qual pasta/diretório estamos
A função `getwd()` informa em qual pasta/diretório o computador está. 
Essa função é do pacote `::base`, que reúne funções básicas, e já vem instalada no R.

```{r}
getwd()
```
#### mudar a pasta em que vamos trabalhar
A função `setwd()` muda a pasta/diretório para o que você informar.

```{r}
setwd("~/Downloads")
```
#### listar os arquivos que estão nessa pasta
A função `list.files()` mostra todos os arquivos que estão na atual pasta/diretório.
Essa função é do pacote `::base`, que reúne funções básicas, e já vem instalada no R.

```{r}
list.files()
```

#### ler arquivos com que vamos trabalhar
Há várias funções para ler os arquivos. A função vai depender, principalmente, do formato do arquivo. É possível ler diversos formatos, como TXT, HTML, XML, DBF etc.

Também é possível ler os dados diretamente de um link.

```{r}
bolsonaro_fav <- fread("https://github.com/gabrielacaesar/curso-r-para-jornalistas/raw/master/data/jairbolsonaro-twitter/BolsonaroFavorites.csv")
```
#### importar arquivo já informando a codificação
UTF-8, Latin-1 e ISO-8859-1 são exemplos de codificações - ou "encoding". Ao informar a codificação certa, você evita ter um arquivo com os acentos errados, o que dificulta a leitura e o entedimento das informações.

A codificação do arquivo também pode ser informada já na importação do arquivo.

```{r}
bolsonaro_fav <- fread("https://github.com/gabrielacaesar/curso-r-para-jornalistas/raw/master/data/jairbolsonaro-twitter/BolsonaroFavorites.csv",
                       encoding = "UTF-8")
```

#### importar arquivo já definindo as colunas 
Este caso é importante, principalmente, em arquivos que são muito pesados. Ao já definir a importação de apenas algumas colunas, o tamanho do arquivo é reduzido.

Nos dados do Inep, por exemplo, há um arquivo de dicionário de dados, que informa os nomes de todas as colunas, bem como a que se referem os valores preenchidos.

Esse arquivo é importante para, antes da importação, já sabermos com quais colunas vamos trabalhar.

```{r}
enem <- fread("MICRODADOS_ENEM_2018.csv", select = c("NU_NOTA_COMP1",
                                                     "NU_NOTA_COMP2",
                                                     "NU_NOTA_COMP3",
                                                     "NU_NOTA_COMP4",
                                                     "NU_NOTA_COMP5",
                                                     "NU_NOTA_REDACAO"))
```

#### importar arquivo já informando o separador de colunas
O separador de um arquivo CSV (sigla para "comma-separated values") é a vírgula. Mas há outros formatos que usam outros separadores.

Aqui é possível informar qual é o separador usado no arquivo.

```{r}
consulta_cand_BR <- fread("consulta_cand_2010_BR.txt", sep = ";")
```

