---
title: Análise - Voos FAB
author: Gabriela Caesar
date: '2019-08-24'
slug: analise-voos-fab
categories: []
tags: [r, rstudio, base, tidyverse, rvest, downloader]
weight: 8
---

instalar as bibliotecas
```{r}
install.packages("tidyverse")
install.packages("rvest")
install.packages("downloader")
```
carregar as bibliotecas
```{r}
library(tidyverse)
library(rvest)
library(downloader)
```
criar pasta e defini-la como local de trabalho
```{r}
dir.create("~/Downloads/PDFvoosFAB")
setwd("~/Downloads/PDFvoosFAB")
```
ler URL da página de interesse
```{r}
url <- "http://www.fab.mil.br/voos"
```
coleta de URLs da página de interesse
```{r}
urls <- url %>%
  read_html() %>%
  html_nodes("a") %>%
  html_attr("href") %>%
  as.data.frame() %>%
  `colnames<-`("link")
```
filtrar por URLs que contenham o PDF
```{r}
urls_voo <- urls %>%
  filter(str_detect(link, "fab.mil.br/cabine/voos/"))
```
criar iteração para baixar PDFs
```{r}
i <- 1

while(i < 142) {
  tryCatch({
    pdf_file <- as.character(urls_voo$link[i], stringsAsFactors = FALSE)
    Sys.sleep(2)
    download.file(pdf_file, basename(pdf_file), mode = "wb")
    Sys.sleep(5)
  }, error = function(e) return(NULL)
  )
  i <- i + 1
}
```

identificar arquivos com formato PDF na pasta
```{r}
files <- list.files(pattern = "*.pdf") 
```
empilhar PDFs e, depois, converter em site
https://www.pdftoexcel.com/
```{r}
teste_merged_2 <- pdf_combine(files[1:50], output = "joined.pdf")
teste_merged_2 <- pdf_combine(files[50:100], output = "joined.pdf")
teste_merged_2 <- pdf_combine(files[101:141], output = "joined.pdf")
```

