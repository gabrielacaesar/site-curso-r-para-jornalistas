---
title: Análise - Voos FAB
author: Gabriela Caesar
date: '2019-10-27'
slug: analise-voos-fab
categories: []
tags: [r, rstudio, base, tidyverse, rvest, downloader]
weight: 8
---
Esta análise fará uso da técnica de webscraping para coletar todas as URLs da página que reúne os PDFs de voos de autoridades com aviões da FAB. O objetivo é automatizar o download dos PDFs e depois empilhá-los para, enfim, converter um PDF maior em arquivo editável, como um XLSX.

#### instalar as bibliotecas
Caso você ainda não tenha instalado os pacotes abaixo, você precisa rodar esse código com a função `install.packages()`.

```{r}
install.packages("tidyverse")
install.packages("rvest")
install.packages("downloader")
```
#### carregar as bibliotecas
Em seguida, precisamos carregar os pacotes que serão usados na análise com a função `library()`.
```{r}
library(tidyverse)
library(rvest)
library(downloader)
```
#### criar pasta e defini-la como local de trabalho
Nesta vez, nós vamos criar uma pasta vazia chamada "PDFvoosFAB". E nós queremos que essa nova pasta esteja dentro de "Downloads". O caminho definido será, portanto, "~/Downloads/PDFvoosFAB".

Não há arquivo na pasta que acabamos de criar. Mas os arquivos que vamos baixar ficarão nesta pasta.

```{r}
dir.create("~/Downloads/PDFvoosFAB")
setwd("~/Downloads/PDFvoosFAB")
```
#### ler URL da página de interesse
Agora, nós informamos qual é o nosso link de interesse. O link deve estar entre aspas. E nós vamos dar o nome de "url".
```{r}
url <- "http://www.fab.mil.br/voos"
```
#### coleta de URLs da página de interesse
Neste momento, nós vamos criar um novo arquivo chamado "urls", que é formado a partir do arquivo "url". Nós vamos primeiro usar a função `read_html()` para ler o HTML da página. Depois, nós procuramos por elementos com a tag "a", com a função `html_nodes()`. E informamos que queremos o atributo "href" dentro dessas tags, com a função `html_attr()`. 

Por fim, para visualizar, nós pedimos para ver o arquivo como dataframe, com a função `as.data.frame()`. Queremos que o cabeçalho desse arquivo receba o nome "link".

```{r}
urls <- url %>%
  read_html() %>%
  html_nodes("a") %>%
  html_attr("href") %>%
  as.data.frame() %>%
  `colnames<-`("link")
```
#### filtrar por URLs que contenham o PDF
No código anterior, nós coletamos todos os links dentro do site. Mas nós queremos apenas os links dos PDF que reúnem dados sobre os voos da FAB. Por isso, agora nós vamos filtrar para manter apenas os links que contenham o texto "fab.mil.br/cabine/voos/".

```{r}
urls_voo <- urls %>%
  filter(str_detect(link, "fab.mil.br/cabine/voos/"))
```
#### criar iteração para baixar PDFs
Esta etapa é crucial. Criamos uma estrutura de repetição no código abaixo para acessar cada link e fazer o download do PDF. O nome do PDF é o nome original dado ao arquivo pela FAB. Estabelecemos alguns segundos de intervalo entre as requisições para não sobrecarregar o site. 

A variável "i" começa com o valor "1". E, por isso, primeiro nós vamos o primeiro PDF da lista. Depois de baixar esse PDF, o código vai acessar o segundo link e baixar o PDF. Isso será feito até o link 141, porque nós informamos isso na estrutura de repetição. Depois de fazer isso, nós podemos já conferir naquela pasta se os arquivos estão lá.

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
#### identificar arquivos com formato PDF na pasta
Neste momento, queremos saber quais são os arquivos que têm o formato PDF e que estão no nosso atual diretório/pasta. 

```{r}
files <- list.files(pattern = "*.pdf") 
```
#### empilhar PDFs e, depois, converter em site
Como eu não consegui descobrir bons pacotes de R que façam a conversão de PDF para CSV/XLSX, nós vamos empilhar os PDFs em um único arquivo para, depois, converter esse arquivo em um site de conversão. Não temos plano e, por isso, não podemos subir um arquivo PDF muito grande, com muitas páginas. Então, vamos criar três PDFs e subir cada um no site. 
https://www.pdftoexcel.com/
```{r}
teste_merged_1 <- pdf_combine(files[1:50], output = "joined_1.pdf")
teste_merged_2 <- pdf_combine(files[50:100], output = "joined_2.pdf")
teste_merged_3 <- pdf_combine(files[101:141], output = "joined_3.pdf")
```

