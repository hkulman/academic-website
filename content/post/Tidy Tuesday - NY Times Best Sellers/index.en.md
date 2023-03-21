---
title: TEST
author: R package build
date: '2023-03-21'
slug: test
categories: []
tags:
  - Data Analysis
  - Visualizations
  - TidyTuesday
subtitle: ''
summary: ''
authors: []
lastmod: '2023-03-21T18:54:23-04:00'
featured: no
draft: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
## Loading Packages / Data


```r
library("pacman") 
p_load(dplyr, tidyverse, extrafont, ggplot2, lubridate, tidyr, data.table, rlist, xlsx, tidytuesdayR, ggpubr, jpeg)


tuesdata <- tidytuesdayR::tt_load('2022-05-10')
```

```
## --- Compiling #TidyTuesday Information for 2022-05-10 ----
```

```
## --- There are 2 files available ---
```

```
## --- Starting Download ---
```

```
## 
## 	Downloading file 1 of 2: `nyt_titles.tsv`
```

```
## Only 10 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## 	Downloading file 2 of 2: `nyt_full.tsv`
```

```
## Only 9 Github queries remaining until 2023-03-21 07:44:00 PM EDT.
```

```
## Warning: One or more parsing issues, call `problems()` on your data frame for details,
## e.g.:
##   dat <- vroom(...)
##   problems(dat)
```

```
## --- Download complete ---
```

```r
tuesdata <- tidytuesdayR::tt_load(2022, week = 19)
```

```
## Only 8 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## Only 8 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## --- Compiling #TidyTuesday Information for 2022-05-10 ----
```

```
## Only 8 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## --- There are 2 files available ---
```

```
## Only 7 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## --- Starting Download ---
```

```
## Only 7 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## 	Downloading file 1 of 2: `nyt_titles.tsv`
```

```
## Only 6 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## 	Downloading file 2 of 2: `nyt_full.tsv`
```

```
## Only 5 Github queries remaining until 2023-03-21 07:44:01 PM EDT.
```

```
## Warning: One or more parsing issues, call `problems()` on your data frame for details,
## e.g.:
##   dat <- vroom(...)
##   problems(dat)
```

```
## --- Download complete ---
```

```r
nyt_titles <- tuesdata$nyt_titles
```
## Visualizing Best Sellers Since 2000 for at least 1 year


```r
# Filtering Data for books that have been best sellers for at least a year

year_bestsellers <- nyt_titles %>%
  filter(total_weeks >= 52) %>%
  filter(year > 2000) %>%
  mutate(total_years  = round(total_weeks / 52, digits = 2)) 

# Clean the titles up:
year_bestsellers$title <- str_to_title(year_bestsellers$title)
year_bestsellers <- year_bestsellers %>% 
  mutate(by_author = paste("by:", author)) %>% 
  mutate(booktitle = paste(title, by_author, sep = "\n"))

img <- readJPEG("test.jpg")
ggplot(year_bestsellers, aes(y = total_years, x = reorder(booktitle, total_years), fill =booktitle)) + 
  background_image(img) + 
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("#cb997e", "#ddbea9", "#ffe8d6", 
                                "#CDCFb3", "#eff1f3", "#d77a61", 
                               "#dbd3d8", "#b7b7a4", "#648981", 
                               "#bfaf82", "#cfb895", "#CD8485", 
                               "#f1d19b", "#897d5e", "#9C8170", 
                               "#e0c2a5")) + 
  geom_text(aes(label = booktitle), color = "black", size = 2.3, angle = 270, position=position_stack(vjust=0.5)) + 
  labs(y = "Years", x = "", title = "NY Times Best Selling Books Since 2000",
       subtitle = "The books depicted below were listed as best sellers for at least 52 consecutive weeks.") +
  theme(axis.text.x = element_blank(),
        axis.title.x = element_blank(),
        axis.ticks.x = element_blank(), 
        legend.position = "none", 
        plot.title = element_text(hjust = 0.5),
        plot.subtitle = element_text(hjust = 0.5)
  )
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />


## References

[1] Kays R, Dunn RR, Parsons AW, Mcdonald B, Perkins T, Powers S, Shell L, McDonald JL, Cole H, Kikillus H, Woods L, Tindle H, Roetman P (2020) The small home ranges and large local ecological impacts of pet cats. Animal Conservation. doi:10.1111/acv.12563

[2] McDonald JL, Cole H (2020) Data from: The small home ranges and large local ecological impacts of pet cats [United Kingdom]. Movebank Data Repository. doi:10.5441/001/1.pf315732
