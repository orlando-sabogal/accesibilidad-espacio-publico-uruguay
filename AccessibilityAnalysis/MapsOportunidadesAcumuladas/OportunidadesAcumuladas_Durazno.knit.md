---
title: "R Notebook"
output:
  html_document:
    df_print: paged
---


```r
library(tidyverse)
```

```
## Warning: package 'tidyverse' was built under R version 3.6.2
```

```
## -- Attaching packages --------- tidyverse 1.3.0 --
```

```
## v ggplot2 3.2.1     v purrr   0.3.3
## v tibble  2.1.3     v dplyr   0.8.3
## v tidyr   1.0.0     v stringr 1.4.0
## v readr   1.3.1     v forcats 0.4.0
```

```
## -- Conflicts ------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(magrittr)
```

```
## 
## Attaching package: 'magrittr'
```

```
## The following object is masked from 'package:purrr':
## 
##     set_names
```

```
## The following object is masked from 'package:tidyr':
## 
##     extract
```

```r
library(sf)
```

```
## Linking to GEOS 3.6.1, GDAL 2.2.3, PROJ 4.9.3
```

```r
library(tmap)
library(RColorBrewer)
```


```r
PopulationOA <- read_delim("../TravelTimeCalculations/Results/Durazno/MatrixOA_Durazno.csv", 
                           delim = " ")
```

```
## Parsed with column specification:
## cols(
##   .default = col_double(),
##   Node = col_character()
## )
```

```
## See spec(...) for full column specifications.
```

```r
PopulationPolygon <- st_read("../TravelTimeCalculations/Results/Durazno/Pouplation_Durazno.shp")
```

```
## Reading layer `Pouplation_Durazno' from data source `C:\Users\Orlan\Dropbox\AccesibilidadEspacioPublicoUruguay\AccessibilityAnalysis\TravelTimeCalculations\Results\Durazno\Pouplation_Durazno.shp' using driver `ESRI Shapefile'
## Simple feature collection with 837 features and 58 fields
## geometry type:  POLYGON
## dimension:      XY
## bbox:           xmin: -56.55165 ymin: -33.40385 xmax: -56.48988 ymax: -33.33384
## epsg (SRID):    4326
## proj4string:    +proj=longlat +datum=WGS84 +no_defs
```

```r
PopulationPolygon %<>% select(Node) %>% left_join(PopulationOA)
```

```
## Joining, by = "Node"
```

```
## Warning: Column `Node` joining factor and character vector, coercing into
## character vector
```


```r
Oportunity <- PopulationOA
MapOportunity <- PopulationPolygon

MapTitle <- "Oportunidades Acumuladas"

X_Lab <- "Población (Porcentaje Acumulado)"
Y_Lab <- "Cantidad de Espacio Público"

Subtitle <- "Accesibilidad"
Subtitle200 <- paste(Subtitle,"a 200 metros", sep = " ")
Subtitle400 <- paste(Subtitle,"a 400 metros", sep = " ")
Subtitle600 <- paste(Subtitle,"a 600 metros", sep = " ")
Subtitle1000 <- paste(Subtitle,"a 1 Km", sep = " ")

Caption <- "Proyecto OPP"
```


```r
Breaks = c(0, 0.1, 2.1, 4.1, 6.1, 8.1, 10.1, 12.1)
Labels = c("Ninguna","0 - 2", "2 - 4", "4 - 6", "6 - 8", "8 - 10","10 - 12")
```


## 0.2 Km



```r
Gap <- Oportunity %>% select(Population, Gap = Gap_200) %>% 
  group_by(Gap) %>% 
  summarise(Population = sum(Population, na.rm = TRUE)) %>% 
  arrange(Gap) %>% 
  filter(Gap > 0)

Total <- sum(Gap$Population)
Gap$PopAcum <- cumsum(Gap$Population)
Gap$PorAcum <- Gap$PopAcum/Total
```


```r
Gap
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Gap"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["Population"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["PopAcum"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["PorAcum"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"1","2":"34927","3":"34927","4":"0.9878663"},{"1":"2","2":"429","3":"35356","4":"1.0000000"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>



```r
Plot200 <- ggplot(data = Gap) + 
  geom_line(aes(x = PorAcum, y = Gap), col = "#8da0cb", size = 1.5) +
  geom_point(aes(x = PorAcum, y = Gap), col = "#a6d854", size = 3) + 
  labs(subtitle = Subtitle200, caption = Caption) + 
  xlab(X_Lab) + ylab(Y_Lab) +
  scale_x_continuous(breaks = seq(0,1,0.1),
                     labels = paste(seq(0,1,0.1)*100,"%")) +
  scale_y_continuous(breaks = seq(1,10,1)) +
  theme_light()

Plot200
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-7-1.png" width="672" />


```r
Map200 <- tm_shape(MapOportunity) + 
  tm_polygons(col = "Gap_200", title = MapTitle, 
              palette = "-RdYlGn",
              breaks = Breaks, 
              labels = Labels) +
  tm_layout(legend.position = c(0.6 ,0.7)) +
   tm_compass(size = 1.5, position = c(0, 0.9)) + 
   tm_scale_bar(position = c(0.02,0.8, size = 3))

Map200
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-8-1.png" width="672" />

## 0.4 Km


```r
Gap <- Oportunity %>% select(Population, Gap = Gap_400) %>% 
  group_by(Gap) %>% 
  summarise(Population = sum(Population, na.rm = TRUE)) %>% 
  arrange(Gap) %>% 
  filter(Gap > 0)

Total <- sum(Gap$Population)
Gap$PopAcum <- cumsum(Gap$Population)
Gap$PorAcum <- Gap$PopAcum/Total
```


```r
Gap
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Gap"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["Population"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["PopAcum"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["PorAcum"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"1","2":"32836","3":"32836","4":"0.9287250"},{"1":"2","2":"1609","3":"34445","4":"0.9742335"},{"1":"3","2":"595","3":"35040","4":"0.9910623"},{"1":"4","2":"316","3":"35356","4":"1.0000000"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


```r
Plot400 <- ggplot(data = Gap) + 
  geom_line(aes(x = PorAcum, y = Gap), col = "#8da0cb", size = 1.5) +
  geom_point(aes(x = PorAcum, y = Gap), col = "#a6d854", size = 3) + 
  labs(subtitle = Subtitle400, caption = Caption) + 
  xlab(X_Lab) + ylab(Y_Lab) +
  scale_x_continuous(breaks = seq(0,1,0.1),
                     labels = paste(seq(0,1,0.1)*100,"%")) +
  scale_y_continuous(breaks = seq(1,10,1)) +
  theme_light()

Plot400
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-11-1.png" width="672" />


```r
Map400 <- tm_shape(MapOportunity) + 
  tm_polygons(col = "Gap_400", title = MapTitle, 
              palette = "-RdYlGn",
              breaks = Breaks, 
              labels = Labels) +
  tm_layout(legend.position = c(0.6 ,0.7)) +
   tm_compass(size = 1.5, position = c(0, 0.9)) + 
   tm_scale_bar(position = c(0.02,0.8, size = 3))

Map400
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-12-1.png" width="672" />


## 0.6 km


```r
Gap <- Oportunity %>% select(Population, Gap = Gap_600) %>% 
  group_by(Gap) %>% 
  summarise(Population = sum(Population, na.rm = TRUE)) %>% 
  arrange(Gap) %>% 
  filter(Gap > 0)

Total <- sum(Gap$Population)
Gap$PopAcum <- cumsum(Gap$Population)
Gap$PorAcum <- Gap$PopAcum/Total
```


```r
Gap
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Gap"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["Population"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["PopAcum"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["PorAcum"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"1","2":"27392","3":"27392","4":"0.7747483"},{"1":"2","2":"4669","3":"32061","4":"0.9068051"},{"1":"3","2":"1791","3":"33852","4":"0.9574613"},{"1":"4","2":"1332","3":"35184","4":"0.9951352"},{"1":"5","2":"172","3":"35356","4":"1.0000000"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


```r
Plot600 <- ggplot(data = Gap) + 
  geom_line(aes(x = PorAcum, y = Gap), col = "#8da0cb", size = 1.5) +
  geom_point(aes(x = PorAcum, y = Gap), col = "#a6d854", size = 3) + 
  labs(subtitle = Subtitle600, caption = Caption) + 
  xlab(X_Lab) + ylab(Y_Lab) +
  scale_x_continuous(breaks = seq(0,1,0.1),
                     labels = paste(seq(0,1,0.1)*100,"%")) +
  scale_y_continuous(breaks = seq(1,10,1)) +
  theme_light()

Plot600
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-15-1.png" width="672" />


```r
Map600 <- tm_shape(MapOportunity) + 
  tm_polygons(col = "Gap_600", title = MapTitle, 
              palette = "-RdYlGn",
              breaks = Breaks, 
              labels = Labels) +
  tm_layout(legend.position = c(0.6 ,0.7)) +
   tm_compass(size = 1.5, position = c(0, 0.9)) + 
   tm_scale_bar(position = c(0.02,0.8, size = 3))

Map600
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-16-1.png" width="672" />

## 1 Km


```r
Gap <- Oportunity %>% select(Population, Gap = Gap_1000) %>% 
  group_by(Gap) %>% 
  summarise(Population = sum(Population, na.rm = TRUE)) %>% 
  arrange(Gap) %>% 
  filter(Gap > 0)

Total <- sum(Gap$Population)
Gap$PopAcum <- cumsum(Gap$Population)
Gap$PorAcum <- Gap$PopAcum/Total
```


```r
Gap
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Gap"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["Population"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["PopAcum"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["PorAcum"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"1","2":"16404","3":"16404","4":"0.4639665"},{"1":"2","2":"5730","3":"22134","4":"0.6260324"},{"1":"3","2":"4800","3":"26934","4":"0.7617943"},{"1":"4","2":"2010","3":"28944","4":"0.8186446"},{"1":"5","2":"2554","3":"31498","4":"0.8908813"},{"1":"6","2":"2110","3":"33608","4":"0.9505600"},{"1":"7","2":"1537","3":"35145","4":"0.9940321"},{"1":"8","2":"211","3":"35356","4":"1.0000000"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


```r
Plot1000 <- ggplot(data = Gap) + 
  geom_line(aes(x = PorAcum, y = Gap), col = "#8da0cb", size = 1.5) +
  geom_point(aes(x = PorAcum, y = Gap), col = "#a6d854", size = 3) + 
  labs(subtitle = Subtitle1000, caption = Caption) + 
  xlab(X_Lab) + ylab(Y_Lab) +
  scale_x_continuous(breaks = seq(0,1,0.1),
                     labels = paste(seq(0,1,0.1)*100,"%")) +
  scale_y_continuous(breaks = seq(1,10,1)) +
  theme_light()

Plot1000
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-19-1.png" width="672" />



```r
Map1000 <- tm_shape(MapOportunity) + 
  tm_polygons(col = "Gap_1000", title = MapTitle, 
              palette = "-RdYlGn",
              breaks = Breaks, 
              labels = Labels) +
  tm_layout(legend.position = c(0.6 ,0.7)) +
   tm_compass(size = 1.5, position = c(0, 0.9)) + 
   tm_scale_bar(position = c(0.02,0.8, size = 3))

Map1000
```

<img src="OportunidadesAcumuladas_Durazno_files/figure-html/unnamed-chunk-20-1.png" width="672" />


# Imprimir Resultados


```r
# ggsave("ResultDurazno/Ojiva200_Durazno.png", Plot200)
# ggsave("ResultDurazno/Ojiva400_Durazno.png", Plot400)
# ggsave("ResultDurazno/Ojiva600_Durazno.png", Plot600)
# ggsave("ResultDurazno/Ojiva1000_Durazno.png", Plot1000)
# 
# tmap_save(Map200, "ResultDurazno//Map200_Durazno.png")
# tmap_save(Map400, "ResultDurazno//Map400_Durazno.png")
# tmap_save(Map600, "ResultDurazno//Map600_Durazno.png")
# tmap_save(Map1000, "ResultDurazno//Map1000_Durazno.png")
```

