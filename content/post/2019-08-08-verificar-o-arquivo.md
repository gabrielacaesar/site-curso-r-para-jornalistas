---
title: Verificação de arquivo
author: Gabriela Caesar
date: '2019-08-08'
slug: verificar-o-arquivo
categories: [r, rstudio, ]
tags: [r, rstudio, ]
weight: 3
---

## glimpse()
Olhar o arquivo

```{r}
glimpse()
```

# head()

Por padrão, a função "head( )" mostra as seis primeiras linhas. Se quiser ver mais linhas ou menos linhas, faça assim: "head(cota_senado, 15)" ou "head(cota_senado, 3)".

```{r}
head(cota_senado)
```
# tail()

A função "tail( )" funciona da forma semelhante. Ela mostra as seis últimas linhas do arquivo. Também podemos indicar o número de linhas que queremos ver.
```{r}
tail(cota_senado)
```
[img]

```{r}
tail(cota_senado, 15)
```
# summary()

A função "summary( )" é bastante interessante para usar com arquivos que tenham números. Ela reúne informações básicas sobre a coluna, como valor mínimo, valor máximo, média, mediana, primeiro quartil e terceiro quartil.

Ao mesmo tempo, em colunas de texto, ela mostra o tamanho da coluna, que abaixo tem 4.477 linhas, já que não é possível calcular métricas como média e afins para textos.

```{r}
summary(cota_senado)
```
# colnames()

A função "colnames( )" nos informa quais são os nomes das colunas do nosso arquivo.

```{r}
colnames(cota_senado)
```
# str()

Abaixo, nós vemos a classe e o nome de cada coluna e também temos noções de como é o nosso arquivo. Ele tem 10 colunas e 4.477 linhas.
- int significa “inteiro”, ou seja, número
- chr significa “character”, ou seja, texto

Outras classes básicas possíveis são: numeric, logical e complex. Leia mais na página do Curso-R.

```{r}
str(cota_senado)
```
# typeof()

Abaixo, usamos "typeof( )" para saber qual é a classe da coluna indicada.
A coluna “VALOR_REEMBOLSO” do arquivo “cota_senado” foi considerada uma coluna de texto. Isso é algo em que futuramente precisaremos mexer.

```{r}
typeof(cota_senado$VALOR_REEMBOLSADO)
```
[img]



