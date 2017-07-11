---
layout: post
title: "Lab01 AD1"
date: 2017-07-11 19:19:43
published: true
tags: [htmlwidgets, r]
---

{% highlight text %}
## 
## Attaching package: 'dplyr'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:stats':
## 
##     filter, lag
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
{% endhighlight %}
#Checkpoint 1

#Importando os dados

Os dados foram coletados pelo imdb, contendo nomes de séries rating entre outros.

No codigo abaixo o csv contendo os dados das séries é importado.

{% highlight r %}
data_all_shows <- read.csv(file="series_from_imdb.csv", header=TRUE, sep=",")
{% endhighlight %}

# 1. Escolha um subconjunto de séries para usar nesse problema 

Dentro do conjunto de dados, são seleciaonadas 3 séries.

{% highlight r %}
data_sub <- filter(data_all_shows, series_name %in% c("13 Reasons Why", "How to Get Away with Murder","Sherlock"))
{% endhighlight %}

# 2. Respondendo perguntas sobre as séries.
## a. Qual das séries que você escolheu é mais bem avaliada no IMDB? A diferença é grande? Pequena? 

Para vermos a diferença de avaliação de cada série podemos calcular a mediana das avaliações em cada episodio da série e comparar las.

Para **13 Reasons Why** temos que a mediana é:


{% highlight r %}
median_reasons_why <- median(filter(data_sub, series_name == "13 Reasons Why")$UserRating)
median_reasons_why
{% endhighlight %}



{% highlight text %}
## [1] 8.5
{% endhighlight %}

Para **How to Get Away with Murder** temos que a mediana é:


{% highlight r %}
median_HtGAwM <- median(filter(data_sub, series_name == "How to Get Away with Murder")$UserRating)
median_HtGAwM
{% endhighlight %}



{% highlight text %}
## [1] 8.7
{% endhighlight %}

Para **Sherlock** temos que a mediana é:


{% highlight r %}
median_sherlock <- median(filter(data_sub, series_name == "Sherlock")$UserRating)
median_sherlock
{% endhighlight %}



{% highlight text %}
## [1] 9
{% endhighlight %}

Comparando as 3 medianas, temos que **Sherlock** é a série melhor avaliada com uma pequena diferença de *0.5* para série que tem a pior avaliação dentre as séries selecionadas.
![plot of chunk unnamed-chunk-7](/figure/source/2017-07-09-problema-um/unnamed-chunk-7-1.png)

## b. Qual das séries que você escolheu tem episódios de qualidade mais irregular segundo o IMDB? A diferença é grande? Pequena?

Para ter uma melhor análise podemos observar como as avaliações são distribuidas em um gráfico BoxPlot.

![plot of chunk unnamed-chunk-8](/figure/source/2017-07-09-problema-um/unnamed-chunk-8-1.png)


A partir desse gráfico podemos ver que a mediana do boxplot de **Sherlok** está acima das outras mas o bloxplot dele é o que apresenta maior espalhamento dos dados,já o de **How to Get Away with Murder** parece ser mais preciso e com notas mais constantes e proximas umas das outras, ou seja, **Sherlok** é o que possui notas mais irregulares dos 3 casos.
Para afirmar que **Sherlok** é a série que mais possui episodios irregulares com mais certeza, podemos utilizar o desvio padrão, que é o quão disperso os dados estão em torno da média, de cada uma das séries e comparalos.

{% highlight r %}
sd(filter(data_sub, series_name == "Sherlock")$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 0.5665266
{% endhighlight %}



{% highlight r %}
sd(filter(data_sub, series_name == "13 Reasons Why")$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 0.4130872
{% endhighlight %}



{% highlight r %}
sd(filter(data_sub, series_name == "How to Get Away with Murder")$UserRating)
{% endhighlight %}



{% highlight text %}
## [1] 0.3520216
{% endhighlight %}

Podemos ver que **Sherlok** possui a maior dipersão dentre as 3 séries selecionadas.

#Checkpoint 4

#As series que tiveram uma queda de desempenho após uma temporada com alto rating?

A primeira pergunta a se fazer é: *quando uma temporada é considerada com alto rating?* Utilizaremos a mediana da temporada e verificaremos se ela é alta, vamos definir que abaixo de 8.7 seja de baixo rating.

Então vamos agrupar as séries por temporada fazendo a mediana de cada temporada.

{% highlight r %}
tv_shows_resume <- group_by(data_all_shows,series_name,season) %>%
  summarize(eps = n(), median = median(UserRating, na.rm = T))

tv_shows_resume
{% endhighlight %}



{% highlight text %}
## Source: local data frame [147 x 4]
## Groups: series_name [?]
## 
## # A tibble: 147 x 4
##               series_name season   eps median
##                    <fctr>  <int> <int>  <dbl>
##  1         13 Reasons Why      1    13   8.50
##  2 Agents of S.H.I.E.L.D.      1    22   8.20
##  3 Agents of S.H.I.E.L.D.      2    22   8.60
##  4 Agents of S.H.I.E.L.D.      3    22   8.65
##  5 Agents of S.H.I.E.L.D.      4    21   8.80
##  6                  Arrow      1    23   8.50
##  7                  Arrow      2    23   8.90
##  8                  Arrow      3    23   8.70
##  9                  Arrow      4    23   8.20
## 10                  Arrow      5    21   8.50
## # ... with 137 more rows
{% endhighlight %}

Agora vamos ver a variação de uma temporada para outra para cada série.

{% highlight r %}
tv_shows_resume <- group_by(data_all_shows,series_name,season) %>%
  summarize(eps = n(), median = median(UserRating, na.rm = T)) %>%
  mutate(variation=(median - lag(median)), last_season = lag(median))

tv_shows_resume
{% endhighlight %}



{% highlight text %}
## Source: local data frame [147 x 6]
## Groups: series_name [31]
## 
## # A tibble: 147 x 6
##               series_name season   eps median variation last_season
##                    <fctr>  <int> <int>  <dbl>     <dbl>       <dbl>
##  1         13 Reasons Why      1    13   8.50        NA          NA
##  2 Agents of S.H.I.E.L.D.      1    22   8.20        NA          NA
##  3 Agents of S.H.I.E.L.D.      2    22   8.60      0.40        8.20
##  4 Agents of S.H.I.E.L.D.      3    22   8.65      0.05        8.60
##  5 Agents of S.H.I.E.L.D.      4    21   8.80      0.15        8.65
##  6                  Arrow      1    23   8.50        NA          NA
##  7                  Arrow      2    23   8.90      0.40        8.50
##  8                  Arrow      3    23   8.70     -0.20        8.90
##  9                  Arrow      4    23   8.20     -0.50        8.70
## 10                  Arrow      5    21   8.50      0.30        8.20
## # ... with 137 more rows
{% endhighlight %}

Assim podemos, por fim filtrar, os dados para terem apenas as series que tinham alto rating depois cairam.


{% highlight r %}
tv_shows_resume <- group_by(data_all_shows,series_name,season) %>%
  summarize(eps = n(), median = median(UserRating, na.rm = T)) %>%
  mutate(variation=(median - lag(median)), last_season = lag(median)) %>%
  filter(variation < 0, last_season >= 8.7, median < 8.7)

tv_shows_resume
{% endhighlight %}



{% highlight text %}
## Source: local data frame [10 x 6]
## Groups: series_name [8]
## 
## # A tibble: 10 x 6
##                    series_name season   eps median variation
##                         <fctr>  <int> <int>  <dbl>     <dbl>
##  1                       Arrow      4    23   8.20     -0.50
##  2                      Dexter      3    12   8.60     -0.25
##  3                      Dexter      6    12   8.40     -0.50
##  4                      Dexter      8    12   7.95     -0.90
##  5                       Flash      3    22   8.55     -0.35
##  6              House of Cards      3    13   8.50     -0.20
##  7 How to Get Away with Murder      3    15   8.60     -0.20
##  8            Once Upon a Time      4    24   8.50     -0.20
##  9                Prison Break      3    13   8.50     -0.20
## 10                    Sherlock      4     4   8.30     -0.75
## # ... with 1 more variables: last_season <dbl>
{% endhighlight %}

Essas são as séries e as temporadas em que houve uma baixa na qualidade e o rating diminuiu.
<br>
Isso levanta outra pergunta:*Qual série teve a maior queda no rating?*
<br>

{% highlight r %}
min(tv_shows_resume$variation)
{% endhighlight %}



{% highlight text %}
## [1] -0.9
{% endhighlight %}
A maior queda foi de -0.9 que foi na 8 temporada de **Dexter**
![plot of chunk unnamed-chunk-14](/figure/source/2017-07-09-problema-um/unnamed-chunk-14-1.png)


#Series com poucos episódios são melhores avaliadas que as com maior numero de episódios?

Para isso temos que dividir as séries em dois grupos,o com muitos episodios e o com poucos episodios. Vamos definir que uma série que tenho mais de 25 episodios se encaixa no grupo com muitos episodios.

{% highlight r %}
big_shows <- group_by(data_all_shows,series_name) %>%
  summarize(eps = n(), median = median(UserRating, na.rm = T)) %>%
  filter(eps > 25)
big_shows
{% endhighlight %}



{% highlight text %}
## # A tibble: 24 x 3
##               series_name   eps median
##                    <fctr> <int>  <dbl>
##  1 Agents of S.H.I.E.L.D.    87   8.60
##  2                  Arrow   113   8.60
##  3           Breaking Bad    62   8.90
##  4              Daredevil    26   8.95
##  5                 Dexter    96   8.70
##  6                  Flash    68   8.70
##  7                Friends   236   8.50
##  8        Game of Thrones    60   8.95
##  9                 Gotham    62   8.40
## 10       Greyâs Anatomy   291   8.20
## # ... with 14 more rows
{% endhighlight %}



{% highlight r %}
small_shows <- group_by(data_all_shows,series_name) %>%
  summarize(eps = n(), median = median(UserRating, na.rm = T)) %>%
  filter(eps <= 25)
small_shows
{% endhighlight %}



{% highlight text %}
## # A tibble: 7 x 3
##       series_name   eps median
##            <fctr> <int>  <dbl>
## 1  13 Reasons Why    13   8.50
## 2    Black Mirror    13   8.30
## 3        Mr Robot    22   8.80
## 4          Narcos    20   8.70
## 5          Sense8    23   9.00
## 6        Sherlock    15   9.00
## 7 Stranger Things     8   8.95
{% endhighlight %}

Podemos comparar as medianas dos dois grupos para vermos qual se dará melhor.

{% highlight r %}
median(big_shows$median)
{% endhighlight %}



{% highlight text %}
## [1] 8.5
{% endhighlight %}



{% highlight r %}
median(small_shows$median)
{% endhighlight %}



{% highlight text %}
## [1] 8.8
{% endhighlight %}
Então sim, séries com menos de 25 episódios são melhores avaliadas que as que possuem mais.
<br>
Gerando outra pergunta:**Mas qual a variancia desses dados e qual está mais espalhado?**
<br>
Podemos tentar ver isso através de um gráfico boxplot

{% highlight text %}
## Joining, by = c("series_name", "eps", "median", "type")
{% endhighlight %}

![plot of chunk unnamed-chunk-17](/figure/source/2017-07-09-problema-um/unnamed-chunk-17-1.png)
Aparentemente amabas as categorias tem o mesmo espalhamento.Podemos ver isso numericamente também.


{% highlight r %}
sd(big_shows$median)
{% endhighlight %}



{% highlight text %}
## [1] 0.360046
{% endhighlight %}



{% highlight r %}
sd(small_shows$median)
{% endhighlight %}



{% highlight text %}
## [1] 0.2692582
{% endhighlight %}

Numericamente as séries maiores possuem maior espalhamento, o que esperado já que são mais temporadas e mais episódios.

Então podemos concluir que séries com menor numero de episódios levam vantagem contra as grandes séries, também vimos quais séries tiveram quedas após uma boa temporada. 

