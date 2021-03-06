---
title: How to select a satisfying board game? - Part I
author: James Diu
date: '2020-07-10'
slug: how-to-select-a-satisfying-board-game
categories:
  - boardgame
  - R
tags: []
backtotop: no
# toc: no
---

<!-- ![](/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/catan_GOT.png) -->

Being a boardgame enthusiasts, I always introduce board games to my friends. Some of them might be another enthusiasts, while some might have an idea that board game is equivalent to chess. Most of time I use my instinct and my understanding on the taste of my friends to decide which game would be provide the greatest fun for us. However, it can be very biased due to my personal preference. On the other hand, I love to collect *different* board games. I hope my collection can be diverse enough to serve all kind of my friends background and preference. So how should I choose a game to meet general preference? And which game should I select, such that it will enrich my collection and make sure it could be fun? (It is quite upset when you find out a new game is boring...) Perhaps learning more about the general preference statistically is not a bad idea. 

## Data description
Regarding the data of board games, [BoardGameGeek(BGG)](https://boardgamegeek.com/) is undoubtely the biggest source. However, I am not going to pull the data directly. Instead, I will use the data from an interesting [post](https://dvatvani.github.io/BGG-Analysis-Part-1.html) about the trend and rating bias on BGG in 2018, which also come from BGG. Although it is not up-to-date data, I think it can still give us some insights and suggestions.


```r
games_Jan2018 <- read_csv("data/games_Jan2018.csv")
df <- games_Jan2018
```


In this dataset, we have total 95777 board games, including the base games(80105) and its expansion(15672). The information can also be separarted into 2 groups: games features and  responses on BGG. Since some information is useless or duplicated, below are the useful columns: 
Features: `type`, `yearpublished`, `minplayers`, `maxplayers`, `minplaytime`, `maxplaytime`, `minage`, `types`, `categories`, `mechanics`, `desginers` 
Responses: `users_rated`, `bayes_average_rating`, `total_owners`, `total_trader`, `total_wanters`, `total_wisher`, `total_comments`, `average_weight`

One of the most important data is `bayes_average_rating`, which is only avaliable for the games with at least 30 users rating. It is used instead of the average player rating in order to exclude those games which few ratings, so that to prevent the bias from small smaple size. After filtering out those data without 30 users rating, we still have 15026 games and 4714 expansion and we will place our foucs on them.

```r
df %>% filter(users_rated>=30) %>% group_by(type) %>% summarise(n=n())
```

```
## # A tibble: 2 x 2
##    type     n
##   <dbl> <int>
## 1     0 15026
## 2     1  4714
```

```r
df <- df %>% filter(users_rated>=30)
```

Now we have a rough idea about what cards are on our hand. Let's find out so further information from them. At the beginning, we will start with the numerical data.

### Correlation
Below is the correlation table among different numerical features. We can see the BGG rating has a positive correlation with the average weight and minimum age, and also slightly positive correlated to the year of publish. Meanwhile, we can also notice that the rating is somehow independent on the number of players and the duration of playing time, but both features show some dependence on both features. Therefore we will go through all these features one by one.
<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />

### Weight
It obvious to notice that the weight of games has positive skewness, with the mode at 2. Meanwhile, we should be reminded that this is a score provided by the users, which means this is a relative value to other games. Due to the same reason, perharps we should consider to use other fixed feature to replace this subjective features.
<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />

### Year of publish
Since some of the games in our list do not have a clear publish date. Therefore we will start with the games published on or after 1985, which has already cover 90% of the games. For the rest of the data I will set them to NA as irrelevant due to small sample size.

```r
sort(df$yearpublished)[round(nrow(df)/10)]
```

```
## [1] 1985
```

From below plot, we can see that the average BGG rating raises slightly over the years. However it is hard to conclude whether the games puhlished are getting better or the baseline of users rating is going upwards. One interesting things to notice is the ratings of game are spreading futher to the higher rating, and so does the best rating. This would be the result of the increase of games published.
<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />
<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />
<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-8-1.png" width="672" />

### Minimum age

We can know that minimum age is positivly correlated to weight and rating. It is reasonable because most of the BGG users are not children and I have sure them are not that interested into those games for children. This fact also implies the evaluation to children's games may be invalid. Besides, we can notice that the minimum age mainly fall into 5 ages: 8,10,12-14. WE can notice that the distrbution is very inbalance. In general the purpose of minimum age is an index for the complexity and content and the judgement is highly depends on the publisher (noted that games specially for 18+ is rare), therefore I would also prefer to transfer minimum age into 5 groups: <6, 6-8, 9-11, 12-14, 15+


```r
df <- df %>% mutate(age_group = ifelse(minage>14, "15+",
                                        ifelse(minage>11, "12-14",
                                               ifelse(minage>8, "9-11",
                                                      ifelse(minage>5, "6-8","<6")))),
                     age_group = factor(age_group, levels = c("<6", "6-8", "9-11","12-14","15+")))
```
<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-10-1.png" width="672" />

According to the plot, it is easy to see that <6 & 15+ has lower rating then other. It reviews that the correlation between minimum age and rating may not be linear.

<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-11-1.png" width="672" />

### Number of players
Most of the game can affort 2-4 players without any surprise.

```
## Scale for 'colour' is already present. Adding another scale for 'colour',
## which will replace the existing scale.
```

<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-12-1.png" width="672" />

### Playing time
We notice that the playing time can be simplified into 4 groups:
-1. short: 30 min or less
-2. meidum: 30 - 60 min
-3. long: 60 - 90 min
-4. extreme: 120 min or longer
In fact, from my experience the playing time can be varied a lot and normally longer than its stated. However, it still provides a segment of time length for the games.
Similiar to minimum age, I would prefer to use above groups as categorical value for the playing time instead of numeric value.

<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-13-1.png" width="672" />


```r
# Assign new group
df <- df %>% mutate(playingtime = ifelse(maxplaytime >= 120, "Extreme",
                                         ifelse(maxplaytime >=90, "Long",
                                                ifelse(maxplaytime >=60, "Normal",
                                                       ifelse(is.na(maxplaytime),NA,"Short")))),
                    playingtime = factor(playingtime, levels =c("Short","Normal","Long","Extreme")))
```

```
## Warning: Factor `playingtime` contains implicit NA, consider using
## `forcats::fct_explicit_na`
```

<img src="/post/2020-07-07-how-to-select-a-satisfying-board-game.en_files/figure-html/unnamed-chunk-15-1.png" width="672" />






