---
title: Tidy Tuesday Visualizations - NY Times Best Sellers
author: Hana
date: '2023-03-21'
slug: Tidy Tuesday - Visualization
categories: []
tags:
  - Data Analysis
  - Visualizations
  - TidyTuesday
subtitle: 'May 11, 2022 - Tidy Tuesday Data Visualization'
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

<script src="{{< blogdown/postref >}}index.en_files/htmlwidgets/htmlwidgets.js"></script>
<script src="{{< blogdown/postref >}}index.en_files/plotly-binding/plotly.js"></script>
<script src="{{< blogdown/postref >}}index.en_files/typedarray/typedarray.min.js"></script>
<script src="{{< blogdown/postref >}}index.en_files/jquery/jquery.min.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/crosstalk/js/crosstalk.min.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.en_files/plotly-main/plotly-latest.min.js"></script>

As an avid reader, I was particularly interested in this dataset. I am constantly looking for a new *best* book to read so this dataset was particularly fun to work with.

## Loading Packages / Data

    ## --- Compiling #TidyTuesday Information for 2022-05-10 ----

    ## --- There are 2 files available ---

    ## --- Starting Download ---

    ## 
    ## 	Downloading file 1 of 2: `nyt_titles.tsv`
    ## 	Downloading file 2 of 2: `nyt_full.tsv`

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## --- Download complete ---

    ## --- Compiling #TidyTuesday Information for 2022-05-10 ----

    ## --- There are 2 files available ---

    ## --- Starting Download ---

    ## 
    ## 	Downloading file 1 of 2: `nyt_titles.tsv`
    ## 	Downloading file 2 of 2: `nyt_full.tsv`

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## --- Download complete ---

## Visualizing Books in the Best Sellers List for 1 Year Since 2000

The Best Sellers list contains books starting in the year 1931 to 2020. I decided to filter the data to only include best sellers from the 21st century. To really get the cream of the crop, I filtered the data again, so that all books included in this analysis were listed as a best seller for at least 52 weeks (1 year).

As shown in the visualization below, the number one bestseller within this time range was The Da Vinci Code by Dan Brown, a book that I still need to read. Of the 16 books listed, my person favorite book that I’ve read thus far, was “The Silent Patient”, there’s nothing better than a good thriller!

``` r
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
        plot.subtitle = element_text(hjust = 0.5),
        plot.background = element_rect(colour = "#ddbea9"),
  )
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-2-1.png" width="672" />

## Examining Trends in Best Sellers that were Ranked \#1 AND were Best Sellers For the Most Weeks that Year

I also examined the books that were ranked \#1 for the greatest total number of books for each year. The data was further grouped by what the debut ranking was (i.e., what rank the book received when it first hit the bestsellers list)!

This plot is **interactive**! Hover over a bar to see the title and author for each top ranked book!

``` r
library(plotly)
```

    ## 
    ## Attaching package: 'plotly'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     last_plot

    ## The following object is masked from 'package:stats':
    ## 
    ##     filter

    ## The following object is masked from 'package:graphics':
    ## 
    ##     layout

``` r
ranked_books <- nyt_titles %>%
  filter(best_rank == 1) %>%
  arrange(desc(total_weeks)) %>%
  group_by(year) %>%
  slice(1) %>% 
  mutate(debut_rank_cat = case_when(
    debut_rank <= 5 ~ "Ranked 1-5", 
    debut_rank > 5 & debut_rank <= 10 ~ "Ranked 5-10",
    debut_rank > 10 & debut_rank <= 15 ~ "Ranked 10-15", 
    TRUE ~ "Ranked 16-20"
  ))
ranked_books$debut_rank <- factor(ranked_books$debut_rank, levels = c("Ranked 1-5", "Ranked 5-10", "Ranked 10-15", "Ranked 16-20"))
ranked_books$title <- str_to_title(ranked_books$title)


p <- ggplot(ranked_books, aes(x = year, y = total_weeks, fill = debut_rank_cat, label1 = title, label2 = author)) + 
  geom_bar(stat = "identity", position = "dodge") + 
  scale_fill_manual(values = c("#796535", "#8d8233", "#da8936", "#d6695a")) +
  labs(title= "#1 Ranked Best Selling Books for Each Year (1931-2020)", y = "Total Weeks as a Best Seller", fill = "What was the book\nranked when\nit first debuted?", y = "Year") + 
  theme(panel.background = element_rect(fill = "white"))

ggplotly(p)
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-1" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"orientation":"v","width":[0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091],"base":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],"x":[1931,1932,1933,1935,1936,1937,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1950,1963,1966,1972,1973,1977,1978,1979,1981,1985,1986,1987,1988,1991,1999,2000,2001,2005,2010,2014,2017],"y":[10,18,86,31,78,60,35,35,31,111,51,58,43,41,40,62,56,51,65,70,39,59,61,47,47,48,50,56,41,34,57,31,35,24,79,132,24],"text":["year: 1931<br />total_weeks:  10<br />debut_rank_cat: Ranked 1-5<br />title: The Good Earth<br />author: Pearl S. Buck","year: 1932<br />total_weeks:  18<br />debut_rank_cat: Ranked 1-5<br />title: The Fountain<br />author: Charles Morgan","year: 1933<br />total_weeks:  86<br />debut_rank_cat: Ranked 1-5<br />title: Anthony Adverse<br />author: Hervey Allen","year: 1935<br />total_weeks:  31<br />debut_rank_cat: Ranked 1-5<br />title: Green Light<br />author: Lloyd C. Douglas","year: 1936<br />total_weeks:  78<br />debut_rank_cat: Ranked 1-5<br />title: Gone With The Wind<br />author: Margaret Mitchell","year: 1937<br />total_weeks:  60<br />debut_rank_cat: Ranked 1-5<br />title: The Citadel<br />author: A. J. Cronin","year: 1939<br />total_weeks:  35<br />debut_rank_cat: Ranked 1-5<br />title: The Grapes Of Wrath<br />author: John Steinbeck","year: 1940<br />total_weeks:  35<br />debut_rank_cat: Ranked 1-5<br />title: How Green Was My Valley<br />author: Richard Llewellyn","year: 1941<br />total_weeks:  31<br />debut_rank_cat: Ranked 1-5<br />title: The Keys Of The Kingdom<br />author: A. J. Cronin","year: 1942<br />total_weeks: 111<br />debut_rank_cat: Ranked 1-5<br />title: The Robe<br />author: Lloyd C. Douglas","year: 1943<br />total_weeks:  51<br />debut_rank_cat: Ranked 1-5<br />title: A Tree Grows In Brooklyn<br />author: Betty Smith","year: 1944<br />total_weeks:  58<br />debut_rank_cat: Ranked 1-5<br />title: Forever Amber<br />author: Kathleen Winsor","year: 1945<br />total_weeks:  43<br />debut_rank_cat: Ranked 1-5<br />title: Captain From Castille<br />author: Samuel Shellaburger","year: 1946<br />total_weeks:  41<br />debut_rank_cat: Ranked 1-5<br />title: This Side Of Innocence<br />author: Taylor Caldwell","year: 1947<br />total_weeks:  40<br />debut_rank_cat: Ranked 1-5<br />title: House Divided<br />author: Ben Ames Williams","year: 1948<br />total_weeks:  62<br />debut_rank_cat: Ranked 1-5<br />title: The Big Fisherman<br />author: Lloyd C. Douglas","year: 1950<br />total_weeks:  56<br />debut_rank_cat: Ranked 1-5<br />title: The Cardinal<br />author: Henry Morton Robinson","year: 1963<br />total_weeks:  51<br />debut_rank_cat: Ranked 1-5<br />title: The Group<br />author: Mary McCarthy","year: 1966<br />total_weeks:  65<br />debut_rank_cat: Ranked 1-5<br />title: Valley Of The Dolls<br />author: Jacqueline Susann","year: 1972<br />total_weeks:  70<br />debut_rank_cat: Ranked 1-5<br />title: Jonathan Livingston Seagull<br />author: Richard Bach","year: 1973<br />total_weeks:  39<br />debut_rank_cat: Ranked 1-5<br />title: Burr<br />author: Gore Vidal","year: 1977<br />total_weeks:  59<br />debut_rank_cat: Ranked 1-5<br />title: The Silmarillion<br />author: J.R.R. Tolkien","year: 1978<br />total_weeks:  61<br />debut_rank_cat: Ranked 1-5<br />title: War And Remembrance<br />author: Herman Wouk","year: 1979<br />total_weeks:  47<br />debut_rank_cat: Ranked 1-5<br />title: Sophie's Choice<br />author: William Styron","year: 1981<br />total_weeks:  47<br />debut_rank_cat: Ranked 1-5<br />title: Noble House<br />author: James Clavell","year: 1985<br />total_weeks:  48<br />debut_rank_cat: Ranked 1-5<br />title: Lake Wobegon Days<br />author: Garrison Keillor","year: 1986<br />total_weeks:  50<br />debut_rank_cat: Ranked 1-5<br />title: Red Storm Rising<br />author: Tom Clancy","year: 1987<br />total_weeks:  56<br />debut_rank_cat: Ranked 1-5<br />title: The Bonfire Of The Vanities<br />author: Tom Wolfe","year: 1988<br />total_weeks:  41<br />debut_rank_cat: Ranked 1-5<br />title: The Cardinal Of The Kremlin<br />author: Tom Clancy","year: 1991<br />total_weeks:  34<br />debut_rank_cat: Ranked 1-5<br />title: Scarlett<br />author: Alexandra Ripley","year: 1999<br />total_weeks:  57<br />debut_rank_cat: Ranked 1-5<br />title: Harry Potter And The Chamber Of Secrets<br />author: J. K. Rowling","year: 2000<br />total_weeks:  31<br />debut_rank_cat: Ranked 1-5<br />title: The Brethren<br />author: John Grisham","year: 2001<br />total_weeks:  35<br />debut_rank_cat: Ranked 1-5<br />title: Skipping Christmas<br />author: John Grisham","year: 2005<br />total_weeks:  24<br />debut_rank_cat: Ranked 1-5<br />title: The Mermaid Chair<br />author: Sue Monk Kidd","year: 2010<br />total_weeks:  79<br />debut_rank_cat: Ranked 1-5<br />title: The Girl Who Kicked The Hornet’s Nest<br />author: Stieg Larsson","year: 2014<br />total_weeks: 132<br />debut_rank_cat: Ranked 1-5<br />title: All The Light We Cannot See<br />author: Anthony Doerr","year: 2017<br />total_weeks:  24<br />debut_rank_cat: Ranked 1-5<br />title: Origin<br />author: Dan Brown"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(121,101,53,1)","line":{"width":1.88976377952756,"color":"transparent"}},"name":"Ranked 1-5","legendgroup":"Ranked 1-5","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091],"base":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],"x":[1951,1952,1954,1956,1957,1958,1959,1960,1961,1967,1968,1989,1990,1995,1996,2007,2008,2009,2015,2016,2019,2020],"y":[123,68,79,76,41,78,102,44,83,52,64,44,178,37,29,49,59,108,77,37,56,34],"text":["year: 1951<br />total_weeks: 123<br />debut_rank_cat: Ranked 10-15<br />title: The Caine Mutiny<br />author: Herman Wouk","year: 1952<br />total_weeks:  68<br />debut_rank_cat: Ranked 10-15<br />title: The Silver Chalice<br />author: Thomas B. Costain","year: 1954<br />total_weeks:  79<br />debut_rank_cat: Ranked 10-15<br />title: Not As A Stranger<br />author: Morton Thompson","year: 1956<br />total_weeks:  76<br />debut_rank_cat: Ranked 10-15<br />title: Peyton Place<br />author: Grace Metalious","year: 1957<br />total_weeks:  41<br />debut_rank_cat: Ranked 10-15<br />title: By Love Possessed<br />author: James Gould Cozzens","year: 1958<br />total_weeks:  78<br />debut_rank_cat: Ranked 10-15<br />title: Exodus<br />author: Leon Uris","year: 1959<br />total_weeks: 102<br />debut_rank_cat: Ranked 10-15<br />title: Advise And Consent<br />author: Allen Drury","year: 1960<br />total_weeks:  44<br />debut_rank_cat: Ranked 10-15<br />title: The Last Of The Just<br />author: Andre Schwarz-Bart","year: 1961<br />total_weeks:  83<br />debut_rank_cat: Ranked 10-15<br />title: The Agony And The Ecstasy<br />author: Irving Stone","year: 1967<br />total_weeks:  52<br />debut_rank_cat: Ranked 10-15<br />title: Topaz<br />author: Leon Uris","year: 1968<br />total_weeks:  64<br />debut_rank_cat: Ranked 10-15<br />title: Airport<br />author: Arthur Hailey","year: 1989<br />total_weeks:  44<br />debut_rank_cat: Ranked 10-15<br />title: Clear And Present Danger<br />author: Tom Clancy","year: 1990<br />total_weeks: 178<br />debut_rank_cat: Ranked 10-15<br />title: Oh, The Places You'll Go!<br />author: Dr. Seuss","year: 1995<br />total_weeks:  37<br />debut_rank_cat: Ranked 10-15<br />title: The Horse Whisperer<br />author: Nicholas Evans","year: 1996<br />total_weeks:  29<br />debut_rank_cat: Ranked 10-15<br />title: The Deep End Of The Ocean<br />author: Jacquelyn Mitchard","year: 2007<br />total_weeks:  49<br />debut_rank_cat: Ranked 10-15<br />title: A Thousand Splendid Suns<br />author: Khaled Hosseini","year: 2008<br />total_weeks:  59<br />debut_rank_cat: Ranked 10-15<br />title: The Host<br />author: Stephenie Meyer","year: 2009<br />total_weeks: 108<br />debut_rank_cat: Ranked 10-15<br />title: The Help<br />author: Kathryn Stockett","year: 2015<br />total_weeks:  77<br />debut_rank_cat: Ranked 10-15<br />title: The Girl On The Train<br />author: Paula Hawkins","year: 2016<br />total_weeks:  37<br />debut_rank_cat: Ranked 10-15<br />title: The Underground Railroad<br />author: Colson Whitehead","year: 2019<br />total_weeks:  56<br />debut_rank_cat: Ranked 10-15<br />title: The Silent Patient<br />author: Alex Michaelides","year: 2020<br />total_weeks:  34<br />debut_rank_cat: Ranked 10-15<br />title: American Dirt<br />author: Jeanine Cummins"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(141,130,51,1)","line":{"width":1.88976377952756,"color":"transparent"}},"name":"Ranked 10-15","legendgroup":"Ranked 10-15","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[0.900000000000091,0.900000000000091],"base":[0,0],"x":[1955,1962],"y":[112,48],"text":["year: 1955<br />total_weeks: 112<br />debut_rank_cat: Ranked 16-20<br />title: Auntie Mame<br />author: Patrick Dennis","year: 1962<br />total_weeks:  48<br />debut_rank_cat: Ranked 16-20<br />title: Seven Days In May<br />author: Fletcher Knebel and Charles W. Bailey II"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(218,137,54,1)","line":{"width":1.88976377952756,"color":"transparent"}},"name":"Ranked 16-20","legendgroup":"Ranked 16-20","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091,0.900000000000091],"base":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],"x":[1934,1938,1949,1953,1964,1965,1969,1970,1971,1974,1975,1976,1980,1982,1983,1984,1992,1993,1994,1997,1998,2002,2003,2004,2006,2011,2012,2013,2018],"y":[27,42,59,54,56,71,67,62,65,60,39,77,41,44,46,38,164,48,165,61,82,65,165,14,35,86,80,60,114],"text":["year: 1934<br />total_weeks:  27<br />debut_rank_cat: Ranked 5-10<br />title: So Red The Rose<br />author: Stark Young","year: 1938<br />total_weeks:  42<br />debut_rank_cat: Ranked 5-10<br />title: The Yearling<br />author: Marjorie Kinnan Rawlings","year: 1949<br />total_weeks:  59<br />debut_rank_cat: Ranked 5-10<br />title: The Egyptian<br />author: Mika Waltari","year: 1953<br />total_weeks:  54<br />debut_rank_cat: Ranked 5-10<br />title: Desiree<br />author: Annemarie Selinko","year: 1964<br />total_weeks:  56<br />debut_rank_cat: Ranked 5-10<br />title: The Spy Who Came In From The Cold<br />author: John le Carré","year: 1965<br />total_weeks:  71<br />debut_rank_cat: Ranked 5-10<br />title: The Source<br />author: James Michener","year: 1969<br />total_weeks:  67<br />debut_rank_cat: Ranked 5-10<br />title: The Godfather<br />author: Mario Puzo","year: 1970<br />total_weeks:  62<br />debut_rank_cat: Ranked 5-10<br />title: Love Story<br />author: Erich Segal","year: 1971<br />total_weeks:  65<br />debut_rank_cat: Ranked 5-10<br />title: The Winds Of War<br />author: Herman Wouk","year: 1974<br />total_weeks:  60<br />debut_rank_cat: Ranked 5-10<br />title: Centennial<br />author: James Michener.","year: 1975<br />total_weeks:  39<br />debut_rank_cat: Ranked 5-10<br />title: Ragtime<br />author: E. L. Doctorow","year: 1976<br />total_weeks:  77<br />debut_rank_cat: Ranked 5-10<br />title: Trinity<br />author: Leon Uris","year: 1980<br />total_weeks:  41<br />debut_rank_cat: Ranked 5-10<br />title: Rage Of Angels<br />author: Sidney Sheldon","year: 1982<br />total_weeks:  44<br />debut_rank_cat: Ranked 5-10<br />title: The Parsifal Mosaic<br />author: Robert Ludlum","year: 1983<br />total_weeks:  46<br />debut_rank_cat: Ranked 5-10<br />title: The Name Of The Rose<br />author: Umberto Eco","year: 1984<br />total_weeks:  38<br />debut_rank_cat: Ranked 5-10<br />title: ''. . . And Ladies Of The Club,''<br />author: Helen Hooven Santmyer","year: 1992<br />total_weeks: 164<br />debut_rank_cat: Ranked 5-10<br />title: The Bridges Of Madison County<br />author: Robert James Waller","year: 1993<br />total_weeks:  48<br />debut_rank_cat: Ranked 5-10<br />title: The Client<br />author: John Grisham","year: 1994<br />total_weeks: 165<br />debut_rank_cat: Ranked 5-10<br />title: The Celestine Prophecy<br />author: James Redfield","year: 1997<br />total_weeks:  61<br />debut_rank_cat: Ranked 5-10<br />title: Cold Mountain<br />author: Charles Frazier","year: 1998<br />total_weeks:  82<br />debut_rank_cat: Ranked 5-10<br />title: Harry Potter And The Sorcerer's Stone<br />author: J. K. Rowling","year: 2002<br />total_weeks:  65<br />debut_rank_cat: Ranked 5-10<br />title: The Lovely Bones<br />author: Alice Sebold","year: 2003<br />total_weeks: 165<br />debut_rank_cat: Ranked 5-10<br />title: The Da Vinci Code<br />author: Dan Brown","year: 2004<br />total_weeks:  14<br />debut_rank_cat: Ranked 5-10<br />title: The Last Juror<br />author: John Grisham","year: 2006<br />total_weeks:  35<br />debut_rank_cat: Ranked 5-10<br />title: For One More Day<br />author: Mitch Albom","year: 2011<br />total_weeks:  86<br />debut_rank_cat: Ranked 5-10<br />title: A Dance With Dragons<br />author: George R. R. Martin","year: 2012<br />total_weeks:  80<br />debut_rank_cat: Ranked 5-10<br />title: Gone Girl<br />author: Gillian Flynn","year: 2013<br />total_weeks:  60<br />debut_rank_cat: Ranked 5-10<br />title: The Goldfinch<br />author: Donna Tartt","year: 2018<br />total_weeks: 114<br />debut_rank_cat: Ranked 5-10<br />title: Where The Crawdads Sing<br />author: Delia Owens"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(214,105,90,1)","line":{"width":1.88976377952756,"color":"transparent"}},"name":"Ranked 5-10","legendgroup":"Ranked 5-10","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":43.1050228310502},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"#1 Ranked Best Selling Books for Each Year (1931-2020)","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[1926.055,2024.945],"tickmode":"array","ticktext":["1950","1975","2000"],"tickvals":[1950,1975,2000],"categoryorder":"array","categoryarray":["1950","1975","2000"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"year","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-8.9,186.9],"tickmode":"array","ticktext":["0","50","100","150"],"tickvals":[0,50,100,150],"categoryorder":"array","categoryarray":["0","50","100","150"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Total Weeks as a Best Seller","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"title":{"text":"What was the book<br />ranked when<br />it first debuted?","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"694c2baba125":{"x":{},"y":{},"fill":{},"label1":{},"label2":{},"type":"bar"}},"cur_data":"694c2baba125","visdat":{"694c2baba125":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

## References

\[1\] Kays R, Dunn RR, Parsons AW, Mcdonald B, Perkins T, Powers S, Shell L, McDonald JL, Cole H, Kikillus H, Woods L, Tindle H, Roetman P (2020) The small home ranges and large local ecological impacts of pet cats. Animal Conservation. doi:10.1111/acv.12563

\[2\] McDonald JL, Cole H (2020) Data from: The small home ranges and large local ecological impacts of pet cats \[United Kingdom\]. Movebank Data Repository. doi:10.5441/001/1.pf315732
