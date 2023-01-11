# Video-games-Analysis-report

## Gaming industry 

Video games are big business: the global gaming market is projected to be worth more than $300 billion by 2027 
According to [mordorintelligence](https://www.mordorintelligence.com/industry-reports/global-gaming-market).  
With so much money at stake, the major game publishers are hugely incentivized to create the next big hit.     
But are games getting better, or has the golden age of video games already passed?  
So we will try to answer some question to get a more closer and catsh insgits about the gaming industry   
To know when is the golden age of video games ??


## Project plan

## 1. look at the data 
we get the data from [kaggel](https://www.kaggle.com/datasets/holmjason2/videogamedata) we'll work with the top 400 best-selling video games created between 1977 and 2020

-- Select all information for the top ten best-selling games  
-- Order the results from best-selling game down to tenth best-selling

```sql
select * from game_sales 
order by games_sold desc
limit 10 
```

| game | platform| publisher | developer| games_sold | year|
| ---- | ------- | --------- | -------  | ---------  | ----| 
| Wii Sports for Wii	 | Wii| Nintendo | Nintendo EAD| 82.90    | 2006|
| Super Mario Bros. for NES	 | NES	| Nintendo | Nintendo EAD	| 40.24 | 1985|
| Counter-Strike: Global Offensive for PC	 | PC| Valve | Valve Corporation	| 40.00 | 2012|
|Mario Kart Wii for Wii |	Wii|	Nintendo|	Nintendo EAD|	37.32 |	2008|
|PLAYERUNKNOWN'S BATTLEGROUNDS for PC|	PC|	PUBG Corporation|	PUBG Corporation|	36.60|	2017|
|Minecraft for PC |	PC |	Mojang |	Mojang AB |	33.15|	2010|
|Wii Sports Resort for Wii|	Wii	|Nintendo|	Nintendo EAD|	33.13|	2009|
|Pokemon Red / Green / Blue Version for GB|	GB|	Nintendo|	Game Freak|	31.38	|1998|
|New Super Mario Bros. for DS|	DS|	Nintendo|	Nintendo EAD|	30.80|	2006|
|New Super Mario Bros. Wii for Wii|	Wii|	Nintendo|	Nintendo EAD|	30.30|	2009| 


+   the best-selling video games were released between 1985 to 2017! 


## 2. Missing review scores 
we can use the data of the reviews so we can gain more insight on the best years fro video games   
its very important to know the limitations of our data

-- Join games_sales and reviews  
-- Select a count of the number of games where both critic_score and user_score are null

```sql
SELECT count(*) as games_count 
from game_sales 
left join reviews 
on reviews.game = game_sales.game 
where critic_score is null and user_score is null
```

| games_count | 
| ---- | 
|31 | 


- like we said at first we work with the top 400  the best-selling video games after we make join between <code>game_sales</code> , <code>reviews</code> tables    
- We learned that around 10% of games have no reviews. There might be a variety of reasons for this.

## 3. Years that video game critics loved

-- Select release year and average critic score for each year, rounded and aliased    
-- Group by release year    
-- Order the data from highest to lowest avg_critic_score and limit to 10 results    

```sql
select year , round (avg (critic_score),2) as avg_critic_score
from game_sales 
left join reviews 
on reviews.game = game_sales.game
group by year
order by avg(critic_score) desc
limit 10
```
| year | avg_critic_score|
| ---- | --------|
|1990 |   9.80   |   
|1992	|9.67|
|1998	|9.32|
|2020	|9.20|
|1993	|9.10|
|1995	|9.07| 
|2004	|9.03|
|1982	|9.00|
|2002	|8.99|
|1999	|8.93|

+ According to critic reviews, the years between 1982 and 2020 were fantastic!
+ What happened in 1990 received the highest reviews compared to other years?
+ Did the video game industry start to grow in 1982?

## 4. Was 1982 really that great?

-- We update the previous query to add a count of games released in each year called num_games .    
-- And returns years that have more than four reviewed games .    

```sql
select year , round (avg (critic_score),2) as avg_critic_score , count (game_sales.game) as num_games
from game_sales
game_sales join reviews
on reviews.game = game_sales.game
group by year
having count(game_sales.game)>4
order by avg(critic_score) desc
limit 10
```

| year | avg_critic_score|num_games|
| ---- | --------|-----------------|
|1998	|9.32	|10|
|2004	|9.03	|11|
|2002	|8.99	|9|
|1999	|8.93	|11|
|2001	|8.82	|13|
|2011	|8.76	|26|
|2016	|8.67	|13|
|2013	|8.66	|18|
|2008	|8.63 |20|
|2012	|8.62	|12| 

+ As we previously said, the Values seem to be strangely round figures for averages. Particularly suspicious is the 1982 value.
+ Perhaps there weren't many video games in our dataset that came out in this year.
+ Additionally, in 1990 there was a 9.80 review of the games because it was the only one game included in our data set so, building calculations on this information would not be accurate.

## 5. Years that dropped off the critics' favorites list
-- In this section, we'll take information from tables <code>top critic years</code> and <code>top_critic_years_more_than_four_games</code> . 
to identify whether the best years had four or more reviewed games.  
-- To return all the rows in the first table that are not in the second table , we will use an operator named EXCEPT.

```sql
select year , avg_critic_score
from top_critic_years 
EXCEPT
select year , avg_critic_score
from  top_critic_years_more_than_four_games
order by avg_critic_score desc
```


| year | avg_critic_score|
| ---- | --------|
|1990|	9.80|
|1992	|9.67|
|2020	|9.20|
|1993	|9.10|
|1995	|9.07|
|1982	|9.00|


+ If we take alook in the table above we will see that the early 1990s it seems the golden age for the video games based on the <code>critic_score</code> alone
+ IF we consider the players' opinions in our insgits makes a difference? lets find out .

## 6. Years video game players loved

-- Let's move on to looking at the opinions of players! To begin, let's use the previous query once again , except this one will look at <code>user_score</code> averages by year rather than <code>critic_score</code> averages.</p>


```sql
select year , round (avg (user_score),2) as avg_user_score , count (game_sales.game) as num_games
from game_sales
join reviews
on reviews.game = game_sales.game
group by year
having count(game_sales.game)>= 4
order by avg_user_score desc
limit 10
```
| year | avg_user_score|num_games|
| ---- | --------|----|
|1997	|9.50	| 8  |
|1998	|9.40	| 10 |
|2010	|9.24 | 23 |
|2009	|9.18 | 20 |
|2008	|9.03	| 20 |
|1996	|9.00 | 5  |
|2005	|8.95 | 13 |
|2006|	8.95| 16 |
|2000|	8.80| 8  |
|1999|	8.80| 11 |

+ Alright, we've got a list of the top ten years according to both critic reviews and user reviews .
+ Now we need to combare the 2 tables to find out if there any years that showed up on both tables? If so, those years would certainly be excellent ones!


## 7. Years that both players and critics loved
-- we will use the operator INTERSECT to retrive the years that are existing in the both tables <code>critic_score</code> and <code>user_score</code> 

```sql
select year 
from 
top_critic_years_more_than_four_games
INTERSECT  
select year 
from 
top_user_years_more_than_four_games
```

|year|
|-----|
|1998|
|2008|
|2002|


+ Looks like we've got three years that both users and critics agreed were in the top ten

## 8. Sales in the best video game years

-- What about video game developers? Were sales up? We may use sales data to determine if this  three years were the best or not. 


```sql
select  year , sum (games_sold) as  total_games_sold 
from game_sales
where year in (select year 
from 
top_critic_years_more_than_four_games
INTERSECT  
select year 
from 
top_user_years_more_than_four_games)
group by year
order by total_games_sold desc
```

 year | total_games_sold|
| ---- | --------|
|2008	|175.07	|
|1998	|101.52	|
|2002	|58.67 |

+ this years were the best year for sales game  

=====================================================

+ At the end of our analysis, we address all of the questions that we discovered.   
+ We analyzed the data from our data set to learn more about the video game business.
+ Because we discovered inaccurate data, we should not base any calculations on it, as we did in the 1990 review with only one game!   
+ We discovered that the golden age of the video game business began in the early 1990s. 
+ We should build our insights on a certain category and type of data, similar to how we do with user scores, critic scores, and games sales.


