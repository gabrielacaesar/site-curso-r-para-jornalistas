---
title: Importação de arquivos
author: Gabriela Caesar
date: '2019-08-08'
slug: importacao-de-arquivos
categories: []
tags: [r, rstudio, data.table, base, tidyverse]
weight: 2
---

No caso, nós escolhemos o nome “cota_senado” para o arquivo que hospedamos no site GitHub Gist. Assim, não precisamos trabalhar com um arquivo local, que só estaria disponível para a nossa máquina.

Caso nós fôssemos usar um arquivo que está no nosso computador, nós teríamos que ver onde ele está e informar esse caminho (path) para o RStudio. Ou teríamos de clicar em "File" > “Import Dataset” e achar o arquivo. 

Abaixo, usamos a função data.table::fread() para importar o arquivo.

```{r}
cota_senado <- fread(“https://gist.githubusercontent.com/gcaesar27/5faede8c1c6ffc82c7145dc3ececcbfe/raw/f3192ff17214c3c5d8eca4ebad42ba6f70d409aa/cota-senado-30-abril-2019")
```
Esta não é a única forma de importar o arquivo. Há várias formas. Abaixo, por exemplo, nós já informamos os nomes das colunas ("col.names"), bem como o separador de colunas ("sep"), o cabeçalho ("header") e a codificação ("encoding").

Observação: nós usamos outro nome (cota_senado2) para não sobrescrever o arquivo anterior.

```{r}
cota_senado2 <- fread(“https://gist.githubusercontent.com/gcaesar27/5faede8c1c6ffc82c7145dc3ececcbfe/raw/f3192ff17214c3c5d8eca4ebad42ba6f70d409aa/cota-senado-30-abril-2019", sep = “;”, header = TRUE, encoding = “UTF-8”, col.names = c(“ano”, “mes”, “senador”, “categoria”, “cnpj_cpj”, “empresa”, “n_documento”, “data”, “detalhamento”, “valor_reembolso”))
```
E ainda com outra biblioteca ("read.table"). Perceba que nós estamos importando um arquivo CSV, mas há outras bibliotecas que importam arquivos Excel, por exemplo, ou outros formatos.

```{r}
cota_senado3 <- read.csv(“https://gist.githubusercontent.com/gcaesar27/5faede8c1c6ffc82c7145dc3ececcbfe/raw/f3192ff17214c3c5d8eca4ebad42ba6f70d409aa/cota-senado-30-abril-2019")
```
