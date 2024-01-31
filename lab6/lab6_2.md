---
title: "dplyr Superhero"
date: "2024-01-30"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
---

## Learning Goals  
*At the end of this exercise, you will be able to:*    
1. Develop your dplyr superpowers so you can easily and confidently manipulate dataframes.  
2. Learn helpful new functions that are part of the `janitor` package.  

## Instructions
For the second part of lab today, we are going to spend time practicing the dplyr functions we have learned and add a few new ones. This lab doubles as your homework. Please complete the lab and push your final code to GitHub.  

## Load the libraries

```r
library("tidyverse")
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.3     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.0
## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
## 
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- read_csv("data/heroes_information.csv", na = c("", "-99", "-"))
```

```
## Rows: 734 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (8): name, Gender, Eye color, Race, Hair color, Publisher, Skin color, A...
## dbl (2): Height, Weight
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
superhero_powers <- read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## Rows: 667 Columns: 168
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr   (1): hero_names
## lgl (167): Agility, Accelerated Healing, Lantern Power Ring, Dimensional Awa...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here. Before you do anything, first have a look at the names of the variables. You can use `rename()` or `clean_names()`.    

```r
superhero_info <- clean_names(superhero_info)
```

## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data.  

```r
tabyl(superhero_info, alignment)
```

```
##  alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

1. Who are the publishers of the superheros? Show the proportion of superheros from each publisher. Which publisher has the highest number of superheros?  

```r
tabyl(superhero_info, publisher)
```

```
##          publisher   n     percent valid_percent
##        ABC Studios   4 0.005449591   0.005563282
##          DC Comics 215 0.292915531   0.299026426
##  Dark Horse Comics  18 0.024523161   0.025034771
##       George Lucas  14 0.019073569   0.019471488
##      Hanna-Barbera   1 0.001362398   0.001390821
##      HarperCollins   6 0.008174387   0.008344924
##     IDW Publishing   4 0.005449591   0.005563282
##        Icon Comics   4 0.005449591   0.005563282
##       Image Comics  14 0.019073569   0.019471488
##      J. K. Rowling   1 0.001362398   0.001390821
##   J. R. R. Tolkien   1 0.001362398   0.001390821
##      Marvel Comics 388 0.528610354   0.539638387
##          Microsoft   1 0.001362398   0.001390821
##       NBC - Heroes  19 0.025885559   0.026425591
##          Rebellion   1 0.001362398   0.001390821
##           Shueisha   4 0.005449591   0.005563282
##      Sony Pictures   2 0.002724796   0.002781641
##         South Park   1 0.001362398   0.001390821
##          Star Trek   6 0.008174387   0.008344924
##               SyFy   5 0.006811989   0.006954103
##       Team Epic TV   5 0.006811989   0.006954103
##        Titan Books   1 0.001362398   0.001390821
##  Universal Studios   1 0.001362398   0.001390821
##          Wildstorm   3 0.004087193   0.004172462
##               <NA>  15 0.020435967            NA
```

```r
# Marvel Comics has the largest number of superheros
```

2. Notice that we have some neutral superheros! Who are they? List their names below.  

```r
superhero_info %>% 
  filter(alignment == "neutral") %>% 
  select("name", "alignment")
```

```
## # A tibble: 24 × 2
##    name         alignment
##    <chr>        <chr>    
##  1 Bizarro      neutral  
##  2 Black Flash  neutral  
##  3 Captain Cold neutral  
##  4 Copycat      neutral  
##  5 Deadpool     neutral  
##  6 Deathstroke  neutral  
##  7 Etrigan      neutral  
##  8 Galactus     neutral  
##  9 Gladiator    neutral  
## 10 Indigo       neutral  
## # ℹ 14 more rows
```

## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?

```r
superhero_info %>% 
  select("name","alignment", "race")
```

```
## # A tibble: 734 × 3
##    name          alignment race             
##    <chr>         <chr>     <chr>            
##  1 A-Bomb        good      Human            
##  2 Abe Sapien    good      Icthyo Sapien    
##  3 Abin Sur      good      Ungaran          
##  4 Abomination   bad       Human / Radiation
##  5 Abraxas       bad       Cosmic Entity    
##  6 Absorbing Man bad       Human            
##  7 Adam Monroe   good      <NA>             
##  8 Adam Strange  good      Human            
##  9 Agent 13      good      <NA>             
## 10 Agent Bob     good      Human            
## # ℹ 724 more rows
```

## Not Human
4. List all of the superheros that are not human.

```r
superhero_info %>% 
  filter(race != "Human")
```

```
## # A tibble: 222 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abe … Male   blue      Icth… No Hair       191 Dark Hor… blue       good     
##  2 Abin… Male   blue      Unga… No Hair       185 DC Comics red        good     
##  3 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  4 Abra… Male   blue      Cosm… Black          NA Marvel C… <NA>       bad      
##  5 Ajax  Male   brown     Cybo… Black         193 Marvel C… <NA>       bad      
##  6 Alien Male   <NA>      Xeno… No Hair       244 Dark Hor… black      bad      
##  7 Amazo Male   red       Andr… <NA>          257 DC Comics <NA>       bad      
##  8 Angel Male   <NA>      Vamp… <NA>           NA Dark Hor… <NA>       good     
##  9 Ange… Female yellow    Muta… Black         165 Marvel C… <NA>       good     
## 10 Anti… Male   yellow    God … No Hair        61 DC Comics <NA>       bad      
## # ℹ 212 more rows
## # ℹ 1 more variable: weight <dbl>
```


## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".

```r
#Good Guys
good_guys <- superhero_info %>% 
  filter(alignment == "good")
```

```r
#Bad Guys
bad_guys <- superhero_info %>% 
  filter(alignment == "bad")
```

6. For the good guys, use the `tabyl` function to summarize their "race".

```r
good_guys %>% 
  tabyl(race)
```

```
##               race   n     percent valid_percent
##              Alien   3 0.006048387   0.010752688
##              Alpha   5 0.010080645   0.017921147
##             Amazon   2 0.004032258   0.007168459
##            Android   4 0.008064516   0.014336918
##             Animal   2 0.004032258   0.007168459
##          Asgardian   3 0.006048387   0.010752688
##          Atlantean   4 0.008064516   0.014336918
##         Bolovaxian   1 0.002016129   0.003584229
##              Clone   1 0.002016129   0.003584229
##             Cyborg   3 0.006048387   0.010752688
##           Demi-God   2 0.004032258   0.007168459
##              Demon   3 0.006048387   0.010752688
##            Eternal   1 0.002016129   0.003584229
##     Flora Colossus   1 0.002016129   0.003584229
##        Frost Giant   1 0.002016129   0.003584229
##      God / Eternal   6 0.012096774   0.021505376
##             Gungan   1 0.002016129   0.003584229
##              Human 148 0.298387097   0.530465950
##    Human / Altered   2 0.004032258   0.007168459
##     Human / Cosmic   2 0.004032258   0.007168459
##  Human / Radiation   8 0.016129032   0.028673835
##         Human-Kree   2 0.004032258   0.007168459
##      Human-Spartoi   1 0.002016129   0.003584229
##       Human-Vulcan   1 0.002016129   0.003584229
##    Human-Vuldarian   1 0.002016129   0.003584229
##      Icthyo Sapien   1 0.002016129   0.003584229
##            Inhuman   4 0.008064516   0.014336918
##    Kakarantharaian   1 0.002016129   0.003584229
##         Kryptonian   4 0.008064516   0.014336918
##            Martian   1 0.002016129   0.003584229
##          Metahuman   1 0.002016129   0.003584229
##             Mutant  46 0.092741935   0.164874552
##     Mutant / Clone   1 0.002016129   0.003584229
##             Planet   1 0.002016129   0.003584229
##             Saiyan   1 0.002016129   0.003584229
##           Symbiote   3 0.006048387   0.010752688
##           Talokite   1 0.002016129   0.003584229
##         Tamaranean   1 0.002016129   0.003584229
##            Ungaran   1 0.002016129   0.003584229
##            Vampire   2 0.004032258   0.007168459
##     Yoda's species   1 0.002016129   0.003584229
##      Zen-Whoberian   1 0.002016129   0.003584229
##               <NA> 217 0.437500000            NA
```

7. Among the good guys, Who are the Vampires?

```r
good_guys %>% 
  filter(race == "Vampire")
```

```
## # A tibble: 2 × 10
##   name  gender eye_color race   hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr>  <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Angel Male   <NA>      Vampi… <NA>           NA Dark Hor… <NA>       good     
## 2 Blade Male   brown     Vampi… Black         188 Marvel C… <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```

```r
# Vampires are Angel and Blade
```

8. Among the bad guys, who are the male humans over 200 inches in height?

```r
bad_guys %>% 
  filter(gender == "Male") %>% 
  filter(height >200.0)
```

```
## # A tibble: 22 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  2 Alien Male   <NA>      Xeno… No Hair       244 Dark Hor… black      bad      
##  3 Amazo Male   red       Andr… <NA>          257 DC Comics <NA>       bad      
##  4 Apoc… Male   red       Muta… Black         213 Marvel C… grey       bad      
##  5 Bane  Male   <NA>      Human <NA>          203 DC Comics <NA>       bad      
##  6 Dark… Male   red       New … No Hair       267 DC Comics grey       bad      
##  7 Doct… Male   brown     Human Brown         201 Marvel C… <NA>       bad      
##  8 Doct… Male   brown     <NA>  Brown         201 Marvel C… <NA>       bad      
##  9 Doom… Male   red       Alien White         244 DC Comics <NA>       bad      
## 10 Kill… Male   red       Meta… No Hair       244 DC Comics green      bad      
## # ℹ 12 more rows
## # ℹ 1 more variable: weight <dbl>
```

9. Are there more good guys or bad guys with green hair?  

```r
good_guys %>% 
  filter(hair_color == "Green") 
```

```
## # A tibble: 7 × 10
##   name   gender eye_color race  hair_color height publisher skin_color alignment
##   <chr>  <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Beast… Male   green     Human Green         173 DC Comics green      good     
## 2 Capta… Male   red       God … Green          NA Marvel C… <NA>       good     
## 3 Doc S… Male   blue      Huma… Green         198 Marvel C… <NA>       good     
## 4 Hulk   Male   green     Huma… Green         244 Marvel C… green      good     
## 5 Lyja   Female green     <NA>  Green          NA Marvel C… <NA>       good     
## 6 Polar… Female green     Muta… Green         170 Marvel C… <NA>       good     
## 7 She-H… Female green     Human Green         201 Marvel C… <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```

```r
# 7 good guys with green hair
```

```r
bad_guys %>% 
  filter(hair_color == "Green")
```

```
## # A tibble: 1 × 10
##   name  gender eye_color race  hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Joker Male   green     Human Green         196 DC Comics white      bad      
## # ℹ 1 more variable: weight <dbl>
```

```r
# 1 bad guy with green hair
```

10. Let's explore who the really small superheros are. In the `superhero_info` data, which have a weight less than 50? Be sure to sort your results by weight lowest to highest.  

```r
superhero_info %>% 
  filter(weight < 50) %>% 
  arrange(weight)
```

```
## # A tibble: 19 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Iron… Male   blue      <NA>  No Hair        NA Marvel C… <NA>       bad      
##  2 Groot Male   yellow    Flor… <NA>          701 Marvel C… <NA>       good     
##  3 Jack… Male   blue      Human Brown          71 Dark Hor… <NA>       good     
##  4 Gala… Male   black     Cosm… Black         876 Marvel C… <NA>       neutral  
##  5 Yoda  Male   brown     Yoda… White          66 George L… green      good     
##  6 Fin … Male   red       Kaka… No Hair       975 Marvel C… green      good     
##  7 Howa… Male   brown     <NA>  Yellow         79 Marvel C… <NA>       good     
##  8 Kryp… Male   blue      Kryp… White          64 DC Comics <NA>       good     
##  9 Rock… Male   brown     Anim… Brown         122 Marvel C… <NA>       good     
## 10 Dash  Male   blue      Human Blond         122 Dark Hor… <NA>       good     
## 11 Long… Male   blue      Human Blond         188 Marvel C… <NA>       good     
## 12 Robi… Male   blue      Human Black         137 DC Comics <NA>       good     
## 13 Wiz … <NA>   brown     <NA>  Black         140 Marvel C… <NA>       good     
## 14 Viol… Female violet    Human Black         137 Dark Hor… <NA>       good     
## 15 Fran… Male   blue      Muta… Blond         142 Marvel C… <NA>       good     
## 16 Swarm Male   yellow    Muta… No Hair       196 Marvel C… yellow     bad      
## 17 Hope… Female green     <NA>  Red           168 Marvel C… <NA>       good     
## 18 Jolt  Female blue      <NA>  Black         165 Marvel C… <NA>       good     
## 19 Snow… Female white     <NA>  Blond         178 Marvel C… <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```


11. Let's make a new variable that is the ratio of height to weight. Call this variable `height_weight_ratio`.    

```r
superhero_info %>% 
  mutate(height_weight_ratio = height/weight)
```

```
## # A tibble: 734 × 11
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 A-Bo… Male   yellow    Human No Hair       203 Marvel C… <NA>       good     
##  2 Abe … Male   blue      Icth… No Hair       191 Dark Hor… blue       good     
##  3 Abin… Male   blue      Unga… No Hair       185 DC Comics red        good     
##  4 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  5 Abra… Male   blue      Cosm… Black          NA Marvel C… <NA>       bad      
##  6 Abso… Male   blue      Human No Hair       193 Marvel C… <NA>       bad      
##  7 Adam… Male   blue      <NA>  Blond          NA NBC - He… <NA>       good     
##  8 Adam… Male   blue      Human Blond         185 DC Comics <NA>       good     
##  9 Agen… Female blue      <NA>  Blond         173 Marvel C… <NA>       good     
## 10 Agen… Male   brown     Human Brown         178 Marvel C… <NA>       good     
## # ℹ 724 more rows
## # ℹ 2 more variables: weight <dbl>, height_weight_ratio <dbl>
```

12. Who has the highest height to weight ratio?  

```r
superhero_info %>% 
  mutate(height_weight_ratio = height/weight) %>% 
  arrange(desc(height_weight_ratio))
```

```
## # A tibble: 734 × 11
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Groot Male   yellow    Flor… <NA>          701 Marvel C… <NA>       good     
##  2 Gala… Male   black     Cosm… Black         876 Marvel C… <NA>       neutral  
##  3 Fin … Male   red       Kaka… No Hair       975 Marvel C… green      good     
##  4 Long… Male   blue      Human Blond         188 Marvel C… <NA>       good     
##  5 Jack… Male   blue      Human Brown          71 Dark Hor… <NA>       good     
##  6 Rock… Male   brown     Anim… Brown         122 Marvel C… <NA>       good     
##  7 Dash  Male   blue      Human Blond         122 Dark Hor… <NA>       good     
##  8 Howa… Male   brown     <NA>  Yellow         79 Marvel C… <NA>       good     
##  9 Swarm Male   yellow    Muta… No Hair       196 Marvel C… yellow     bad      
## 10 Yoda  Male   brown     Yoda… White          66 George L… green      good     
## # ℹ 724 more rows
## # ℹ 2 more variables: weight <dbl>, height_weight_ratio <dbl>
```

```r
# Groot has the highest height to weight ratio
```


## `superhero_powers`
Have a quick look at the `superhero_powers` data frame.  

13. How many superheros have a combination of agility, stealth, super_strength, stamina?

```r
superhero_powers <- clean_names(superhero_powers)
```


```r
superhero_powers %>% 
  filter(agility == "TRUE" & stealth == "TRUE" & super_strength=="TRUE" & stamina == "TRUE" )
```

```
## # A tibble: 40 × 168
##    hero_names  agility accelerated_healing lantern_power_ring
##    <chr>       <lgl>   <lgl>               <lgl>             
##  1 Alex Mercer TRUE    TRUE                FALSE             
##  2 Angel       TRUE    TRUE                FALSE             
##  3 Ant-Man II  TRUE    FALSE               FALSE             
##  4 Aquaman     TRUE    TRUE                FALSE             
##  5 Batman      TRUE    FALSE               FALSE             
##  6 Black Flash TRUE    FALSE               FALSE             
##  7 Black Manta TRUE    FALSE               FALSE             
##  8 Brundlefly  TRUE    FALSE               FALSE             
##  9 Buffy       TRUE    TRUE                FALSE             
## 10 Cable       TRUE    TRUE                FALSE             
## # ℹ 30 more rows
## # ℹ 164 more variables: dimensional_awareness <lgl>, cold_resistance <lgl>,
## #   durability <lgl>, stealth <lgl>, energy_absorption <lgl>, flight <lgl>,
## #   danger_sense <lgl>, underwater_breathing <lgl>, marksmanship <lgl>,
## #   weapons_master <lgl>, power_augmentation <lgl>, animal_attributes <lgl>,
## #   longevity <lgl>, intelligence <lgl>, super_strength <lgl>,
## #   cryokinesis <lgl>, telepathy <lgl>, energy_armor <lgl>, …
```

## Your Favorite
14. Pick your favorite superhero and let's see their powers!  

```r
superhero_powers %>% 
  filter(hero_names == "Abraxas") %>% 
  summary(hero_names)
```

```
##   hero_names         agility        accelerated_healing lantern_power_ring
##  Length:1           Mode :logical   Mode :logical       Mode :logical     
##  Class :character   FALSE:1         FALSE:1             FALSE:1           
##  Mode  :character                                                         
##  dimensional_awareness cold_resistance durability       stealth       
##  Mode:logical          Mode :logical   Mode :logical   Mode :logical  
##  TRUE:1                FALSE:1         FALSE:1         FALSE:1        
##                                                                       
##  energy_absorption  flight        danger_sense    underwater_breathing
##  Mode :logical     Mode:logical   Mode :logical   Mode :logical       
##  FALSE:1           TRUE:1         FALSE:1         FALSE:1             
##                                                                       
##  marksmanship    weapons_master  power_augmentation animal_attributes
##  Mode :logical   Mode :logical   Mode :logical      Mode :logical    
##  FALSE:1         FALSE:1         FALSE:1            FALSE:1          
##                                                                      
##  longevity       intelligence   super_strength cryokinesis     telepathy      
##  Mode :logical   Mode:logical   Mode:logical   Mode :logical   Mode :logical  
##  FALSE:1         TRUE:1         TRUE:1         FALSE:1         FALSE:1        
##                                                                               
##  energy_armor    energy_blasts   duplication     size_changing  density_control
##  Mode :logical   Mode :logical   Mode :logical   Mode:logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         TRUE:1         FALSE:1        
##                                                                                
##   stamina        astral_travel   audio_control   dexterity      
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##   omnitrix       super_speed    possession      animal_oriented_powers
##  Mode :logical   Mode:logical   Mode :logical   Mode :logical         
##  FALSE:1         TRUE:1         FALSE:1         FALSE:1               
##                                                                       
##  weapon_based_powers electrokinesis  darkforce_manipulation death_touch    
##  Mode :logical       Mode :logical   Mode :logical          Mode :logical  
##  FALSE:1             FALSE:1         FALSE:1                FALSE:1        
##                                                                            
##  teleportation  enhanced_senses telekinesis     energy_beams     magic        
##  Mode:logical   Mode :logical   Mode :logical   Mode :logical   Mode:logical  
##  TRUE:1         FALSE:1         FALSE:1         FALSE:1         TRUE:1        
##                                                                               
##  hyperkinesis       jump         clairvoyance    dimensional_travel
##  Mode :logical   Mode :logical   Mode :logical   Mode:logical      
##  FALSE:1         FALSE:1         FALSE:1         TRUE:1            
##                                                                    
##  power_sense     shapeshifting   peak_human_condition immortality   
##  Mode :logical   Mode :logical   Mode :logical        Mode:logical  
##  FALSE:1         FALSE:1         FALSE:1              TRUE:1        
##                                                                     
##  camouflage      element_control  phasing        astral_projection
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical    
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1          
##                                                                   
##  electrical_transport fire_control    projection      summoning      
##  Mode :logical        Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1              FALSE:1         FALSE:1         FALSE:1        
##                                                                      
##  enhanced_memory  reflexes       invulnerability energy_constructs
##  Mode :logical   Mode :logical   Mode:logical    Mode :logical    
##  FALSE:1         FALSE:1         TRUE:1          FALSE:1          
##                                                                   
##  force_fields    self_sustenance anti_gravity     empathy       
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  power_nullifier radiation_control psionic_powers  elasticity     
##  Mode :logical   Mode :logical     Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1           FALSE:1         FALSE:1        
##                                                                   
##  substance_secretion elemental_transmogrification technopath_cyberpath
##  Mode :logical       Mode :logical                Mode :logical       
##  FALSE:1             FALSE:1                      FALSE:1             
##                                                                       
##  photographic_reflexes seismic_power   animation       precognition   
##  Mode :logical         Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1               FALSE:1         FALSE:1         FALSE:1        
##                                                                       
##  mind_control    fire_resistance power_absorption enhanced_hearing
##  Mode :logical   Mode :logical   Mode :logical    Mode :logical   
##  FALSE:1         FALSE:1         FALSE:1          FALSE:1         
##                                                                   
##  nova_force       insanity       hypnokinesis    animal_control 
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  natural_armor   intangibility   enhanced_sight  molecular_manipulation
##  Mode :logical   Mode :logical   Mode :logical   Mode:logical          
##  FALSE:1         FALSE:1         FALSE:1         TRUE:1                
##                                                                        
##  heat_generation adaptation       gliding        power_suit     
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  mind_blast      probability_manipulation gravity_control regeneration   
##  Mode :logical   Mode :logical            Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1                  FALSE:1         FALSE:1        
##                                                                          
##  light_control   echolocation    levitation      toxin_and_disease_control
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical            
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1                  
##                                                                           
##    banish        energy_manipulation heat_resistance natural_weapons
##  Mode :logical   Mode:logical        Mode :logical   Mode :logical  
##  FALSE:1         TRUE:1              FALSE:1         FALSE:1        
##                                                                     
##  time_travel     enhanced_smell  illusions       thirstokinesis 
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  hair_manipulation illumination    omnipotent       cloaking      
##  Mode :logical     Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1           FALSE:1         FALSE:1         FALSE:1        
##                                                                   
##  changing_armor  power_cosmic   biokinesis      water_control  
##  Mode :logical   Mode:logical   Mode :logical   Mode :logical  
##  FALSE:1         TRUE:1         FALSE:1         FALSE:1        
##                                                                
##  radiation_immunity vision_telescopic toxin_and_disease_resistance
##  Mode :logical      Mode :logical     Mode :logical               
##  FALSE:1            FALSE:1           FALSE:1                     
##                                                                   
##  spatial_awareness energy_resistance telepathy_resistance molecular_combustion
##  Mode :logical     Mode :logical     Mode :logical        Mode :logical       
##  FALSE:1           FALSE:1           FALSE:1              FALSE:1             
##                                                                               
##  omnilingualism  portal_creation magnetism       mind_control_resistance
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical          
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1                
##                                                                         
##  plant_control     sonar         sonic_scream    time_manipulation
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical    
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1          
##                                                                   
##  enhanced_touch  magic_resistance invisibility    sub_mariner    
##  Mode :logical   Mode :logical    Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1          FALSE:1         FALSE:1        
##                                                                  
##  radiation_absorption intuitive_aptitude vision_microscopic  melting       
##  Mode :logical        Mode :logical      Mode :logical      Mode :logical  
##  FALSE:1              FALSE:1            FALSE:1            FALSE:1        
##                                                                            
##  wind_control    super_breath    wallcrawling    vision_night   
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  vision_infrared grim_reaping    matter_absorption the_force      
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1           FALSE:1        
##                                                                   
##  resurrection    terrakinesis    vision_heat     vitakinesis    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  radar_sense     qwardian_power_ring weather_control vision_x_ray   
##  Mode :logical   Mode :logical       Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1             FALSE:1         FALSE:1        
##                                                                     
##  vision_thermal  web_creation    reality_warping odin_force     
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1         FALSE:1        
##                                                                 
##  symbiote_costume speed_force     phoenix_force   molecular_dissipation
##  Mode :logical    Mode :logical   Mode :logical   Mode :logical        
##  FALSE:1          FALSE:1         FALSE:1         FALSE:1              
##                                                                        
##  vision_cryo     omnipresent     omniscient     
##  Mode :logical   Mode :logical   Mode :logical  
##  FALSE:1         FALSE:1         FALSE:1        
## 
```

15. Can you find your hero in the superhero_info data? Show their info!  

```r
superhero_info %>% 
  filter(name == "Abraxas")
```

```
## # A tibble: 1 × 10
##   name   gender eye_color race  hair_color height publisher skin_color alignment
##   <chr>  <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Abrax… Male   blue      Cosm… Black          NA Marvel C… <NA>       bad      
## # ℹ 1 more variable: weight <dbl>
```

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.  
