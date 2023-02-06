## Olympics-Dataset-Project
Inspired by techTFQ YouTube Channel, solved different set of questions in SQL. 

![image](https://user-images.githubusercontent.com/105527885/216901699-043945e7-9676-42e4-b08a-7d26fbca6b87.png)



# **Welcome to my Olympics Dataset Project**


Introduction
The goal of this project is to become acquainted with SQL concepts by solving real-world data questions; we will accomplish this by downloading 120 years of Olympics History dataset from Kaggle and answering each question provided by techTFQ accordingly.

##### olympics_history
| column | type |
| ------ | -----|
|ID|	FLOAT
|Name|	VARCHAR(255)
|Sex|	VARCHAR(255)
|Age|	FLOAT
|Height|	FLOAT
|Weight|	FLOAT
|Team|	VARCHAR(255)
|NOC|	VARCHAR(255)
|Games|	VARCHAR(255)
|Year|	FLOAT
|Season|	VARCHAR(255)
|City|	VARCHAR(255)
|Sport|	VARCHAR(255)
|Event|	VARCHAR(255)
|Medal|	VARCHAR(255)

##### olympics_history_noc_regions
|column|	type|
|-----|-----|
|NOC|	VARCHAR(255)
|region|	VARCHAR(255)
|notes|	VARCHAR(255)


### CASE STUDY QUESTIONS

      1.Write a SQL query to find the total no of Olympic Games held as per the dataset.

      select * from olympics_history;
      select * from olympics_history_noc_regions;

        SELECT COUNT(DISTINCT (Games)) as total_games
        FROM olympics_history;

<details><summary>SHOW ANSWER</summary>
<p>

total_games
51

</p>
</details>

    2.Write a SQL query to list down all the Olympic Games held so far

     SELECT DISTINCT Year, Season, City
          FROM olympics_history
          Order By Year;

<details><summary>SHOW ANSWER</summary>
<p>

|year|season|city|
|----|-------|-----|
|1896|	Summer|	Athina|
|1900|	Summer	|Paris
|1904|	Summer|	St. Louis
|1906	|Summer	|Athina
|1908	|Summer|	London
|1912	|Summer	|Stockholm
|1920	|Summer	|Antwerpen
|1924	|Winter	|Chamonix
|1928	|Winter|Sankt Moritz
|1928	|Summer	|Amsterdam
|1932	|Summer|	Los Angeles
|1932	|Winter	|Lake Placid
|1948	|Summer	|London
|1948	|Winter	|Sankt Moritz
|1952	|Summer	|Helsinki
1952	|Winter|	Oslo
1956|	Summer	|Melbourne
1956|	Summer	|Stockholm
1956|	Winter	|Cortina d'Ampezzo
1960|	Winter	|Squaw Valley
1960|	Summer	|Roma
1964|	Summer	|Tokyo
1964|	Winter	|Innsbruck
1968|	Winter	|Grenoble
1968|	Summer	|Mexico City
1972|	Summer	|Munich
1972|	Winter	|Sapporo
1976|	Winter	|Innsbruck
1976|	Summer	|Montreal
1980|	Winter	|Lake Placid
1980|	Summer	|Moskva
1984|	Winter	|Sarajevo
1984|	Summer	|Los Angeles
1988|	Summer	|Seoul
1988|	Winter	|Calgary
1992|	Winter	|Albertville
1992|	Summer	|Barcelona
1994|	Winter	|Lillehammer
1996|	Summer	|Atlanta
1998|	Winter	|Nagano
2000|	Summer|	|Sydney
2002|	Winter	|Salt Lake City
2004|	Summer	|Athina
2006|	Winter	|Torino
2008|	Summer	|Beijing
2010|	Winter	|Vancouver
2012|	Summer	|London
2014|	Winter	|Sochi
2016|	Summer	|Rio de Janeiro

</p>
</details>

    3. Fetch the total number of nations who participated in each olympics game?

    WITH All_Countries as
    (SELECT DISTINCT games, nr.noc
     FROM olympics_history o
     JOIN olympics_history_noc_regions nr
      ON o.noc = nr.noc
      GROUP BY games, nr.noc)
    SELECT games, count(1) as total_countries
      FROM All_Countries
       GROUP BY games
       Order BY games;

<details><summary>SHOW ANSWER</summary>
<p>

games	total_countries
1896 Summer	12
1900 Summer	31
1904 Summer	15
1906 Summer	21
1908 Summer	22
1912 Summer	29
1920 Summer	29
1924 Summer	45
1924 Winter	19
1928 Summer	46
1928 Winter	25
1932 Summer	47
1932 Winter	17
1936 Summer	49
1936 Winter	28
1948 Summer	58
1948 Winter	28
1952 Summer	68
1952 Winter	30
1956 Summer	71
1956 Winter	32
1960 Summer	83
1960 Winter	30
1964 Summer	93
1964 Winter	36
1968 Summer	111
1968 Winter	37
1972 Summer	120
1972 Winter	35
1976 Summer	91
1976 Winter	37
1980 Summer	80
1980 Winter	37
1984 Summer	139
1984 Winter	49
1988 Summer	158
1988 Winter	57
1992 Summer	168
1992 Winter	64
1994 Winter	67
1996 Summer	196
1998 Winter	72
2000 Summer	199
2002 Winter	77
2004 Summer	200
2006 Winter	79
2008 Summer	203
2010 Winter	82
2012 Summer	204
2014 Winter	89
2016 Summer	206

</p>
</details>

    4.Write a SQL query to return the Olympic Games which had the highest participating countries and the lowest participating countries.

      WITH All_countries as
	  (SELECT games, nr.region
	  FROM olympics_history o
	  JOIN olympics_history_noc_regions nr
	  ON nr.noc = o.noc
	    GROUP BY games, nr.region),
	    total_countries as
	  (SELECT games, count(1) as total_countries
      	FROM All_countries
      	GROUP BY games)
	
	SELECT DISTINCT
	concat(first_value(games) over(order by total_countries)
      , ' - '
      , first_value(total_countries) over(order by total_countries)) as Lowest_Countries,
      concat(first_value(games) over(order by total_countries desc)
      , ' - '
      , first_value(total_countries) over(order by total_countries desc)) as Highest_Countries
      from total_countries
      order by 1;
 <details><summary>CLick Me</summary>
  <p>
      
 |lowest_countries |	highest_countries|
 |-----------------|------------------|
 |1896 Summer - 12 |	2016 Summer - 204|
  </p>
  </details>
5. SQL query to return the list of countries who have been part of every Olympics games.
      
      WITH total_games AS
	  (SELECT count(distinct games) AS total_games
	  FROM olympics_history),
	  countries AS
	  (SELECT games, nr.region as country
	  FROM olympics_history o
	  JOIN olympics_history_noc_regions nr
	  ON nr.noc = o.noc
	  GROUP BY games, nr.region),
	  countries_participated as
	  (SELECT country, count(1) as total_participated_games
	  FROM countries
	  GROUP BY country)
	  
	  SELECT cp.*
	  FROM countries_participated cp
	  JOIN total_games tg 
	  ON tg.total_games = cp.total_participated_games
	  ORDER BY 1;
    
<details><summary>SHOW ANSWER</summary>
<p>
    
|country|	total_participated_games|
|-------|------|
|France	|51
|Italy	|51
|Switzerland|	51
|UK	|51

</p>
</details>

    6. SQL query to fetch the list of all sports which have been part of every olympics.
     
     
    WITH T1 AS
	  (SELECT count(DISTINCT games) As total_games
	  FROM olympics_history 
	  WHERE season = 'Summer'),
	  T2 AS
	  (SELECT DISTINCT games, sport
	  FROM olympics_history 
	  WHERE season = 'Summer'),
	  T3 AS 
	  (SELECT sport, count(1) AS no_of_games
	   FROM T2
	   GROUP BY sport)
     SELECT *
	   FROM T3
	   JOIN T1 ON T1.total_games = T3.no_of_games;
     
<details><summary>SHOW ANSWER</summary>
<p>

|sport|	no_of_games	|total_games|
|--------|------|-----|
|Swimming|	29	|29|
|Cycling	|29|	29|
|Fencing	|29	|29|
|Gymnastics|	29	|29|
|Athletics|	29|	29|

</p>
</details>

        7.Using SQL query, Identify the sport which were just played once in all of olympics.

         WITH T1 AS
	   			(SELECT distinct games , sport
				FROM olympics_history),
				T2 AS 
				(SELECT sport, count(1) AS no_of_games
				FROM T1
				GROUP BY sport)
				
				SELECT T2.*, T1.games
				FROM T2
				JOIN T1 ON T1.sport = T2.sport
				WHERE T2.no_of_games = 1
				ORDER BY T1.sport;
        
<details><summary>SHOW ANSWER</summary>
<p>
        
|sport|	no_of_games|	games|
|----|-----|----|
Aeronautics|	1	|1936 Summer
Basque Pelota|	1	|1900 Summer
Cricket|	1	|1900 Summer
Croquet	|1|	1900 Summer
Jeu De Paume	|1	|1908 Summer
Military Ski Patrol|	1	|1924 Winter
Motorboating|	1	|1908 Summer
Racquets|	1	|1908 Summer
Roque	|1|	1904 Summer
Rugby Sevens	|1	|2016 Summer

</p>
</details>

    8.Write SQL query to fetch the total no of sports played in each olympics.

          WITH T1 as
	        (SELECT DISTINCT games, sport
	          FROM olympics_history),
	T2 AS
	(SELECT games, count(1) AS number_of_sports
	FROM T1
	GROUP BY games)
	
	SELECT * FROM T2
	ORDER BY number_of_sports DESC;
  
<details><summary>SHOW ANSWER</summary>
<p>

games	|number_of_sports|
|-----|----|
2000 |Summer	34
2004 |Summer	34
2016 |Summer	34
2008 |Summer	34
2012 |Summer	32
1996 |Summer	31
1992 |Summer	29
1988 |Summer	27
1920 |Summer	25
1984 |Summer	25
1908 |Summer	24
1936 |Summer	24
1972 |Summer	23
1980 |Summer	23
1976 |Summer	23|
 
</p>
</details>

      9. SQL Query to fetch the details of the oldest athletes to win a gold medal at the olympics.

                WITH T1 AS
	                (SELECT name, sex, cast(CASE when age = 'NA'
						   		then '0'
						   		ELSE age END as int) as age,
	 				team,games,city,sport,event,medal
		        FROM olympics_history),
		          Rank1 AS
		          (SELECT *, RANK() OVER(ORDER BY age desc) as rank
		           FROM T1
		          WHERE medal = 'Gold')
		 
		          SELECT * 
		          FROM Rank1
		            WHERE rank =1;
     
<details><summary>SHOW ANSWER</summary>
<p>
     
|name|	sex|	age|	team|	games|	city|	sport	|event|	medal|	rank|
|----|----|----|----|----|----|---|----|-----|----|
Charles Jacobus	|M	|64|	United States|	1904 Summer	|St. Louis	|Roque	|Roque Men's Singles	|Gold|	1|
Oscar Gomer Swahn|	M	|64	|Sweden	|1912 Summer	|Stockholm	|Shooting	|Shooting Men's Running Target, Single Shot, Team	|Gold	|1

</p>
</details>

      10. SQL query to get the ratio of male and female participants

       WITH T1 AS
		 (SELECT sex, count(1) as count
		 FROM olympics_history
		 GROUP BY sex),
		 T2 AS
		 (SELECT *, ROW_NUMBER() OVER(ORDER BY count) as rn
		 FROM T1),
		 min_cnt as
		 (SELECT count FROM T2 
		 WHERE rn = 1),
		 max_cnt as
		 (SELECT count FROM T2
		 WHERE rn = 2)
		 
		 SELECT concat('1:', round(max_cnt.count :: decimal/min_cnt.count, 2)) as ratio
		 FROM min_cnt, max_cnt;
     
<details><summary>SHOW ANSWER</summary>
<p>
     |ratio|
     |----|
    |1:2.64|
    
</p>
</details>
    
    11. Fetch the top 5 athletes who have won the most gold medals.
    
    WITH T1 AS
		 (SELECT name, team, count(1) AS total_gold_medals
		 FROM olympics_history
		 WHERE medal = 'Gold'
		 GROUP BY name, team
		 ORDER BY total_gold_medals DESC),
		 T2 AS
		 (SELECT *, DENSE_RANK() OVER (ORDER BY total_gold_medals desc) as rnk
		 FROM T1)
		 
		 SELECT name, team, total_gold_medals
		 FROM T2
		 WHERE rnk <=5;
     
<details><summary>SHOW ANSWER</summary>
<p>

 |name|	team	|total_gold_medals|
 |----|----|----------|
|Michael Fred Phelps, II|	United States|	23|
Raymond Clarence "Ray" Ewry	|United States	|10|
Mark Andrew Spitz|	United States	|9|
Larysa Semenivna Latynina (Diriy-)	|Soviet Union|	9
Frederick Carlton "Carl" Lewis	|United States	|9
Paavo Johannes Nurmi	|Finland	|9|
Ole Einar Bjrndalen|	Norway	8|
Matthew Nicholas "Matt" Biondi|	United States|	8|
Sawao Kato	|Japan	|8|
Jennifer Elisabeth "Jenny" Thompson (-Cumpelik)	|United States|	8
Usain St. Leo Bolt|	Jamaica	|8|
Donald Arthur "Don" Schollander|	United States|	7
Vra slavsk (-Odloilov)|	Czechoslovakia	|7|
Viktor Ivanovych Chukarin	|Soviet Union|	7
Nikolay Yefimovich Andrianov|	Soviet Union|	7
Borys Anfiyanovych Shakhlin	|Soviet Union|	7
Aladr Gerevich (-Gerei)|	Hungary|	7

</p>
</details>

        12.  Fetch the top 5 athletes who have won the most medals (gold/silver/bronze).
        WITH T1 AS
             (SELECT name, team, count(1) AS total_medals
             FROM olympics_history
             WHERE medal IN('Gold', 'Silver','Bronze')
             GROUP BY name, team
             ORDER BY total_medals DESC),
             T2 AS
             (SELECT *, DENSE_RANK() OVER (ORDER BY total_medals desc) as rnk
             FROM T1)

             SELECT name, team, total_medals
             FROM T2
             WHERE rnk <=5;
<details><summary>SHOW ANSWER</summary>
<p>
     
  |name	|team|	total_medals|
  |--|------|-----------|
Michael Fred Phelps, II|	United States	|28
Larysa Semenivna Latynina (Diriy-)	|Soviet Union	|18
Nikolay Yefimovich Andrianov	|Soviet Union|	15
Borys Anfiyanovych Shakhlin|	Soviet Union|	13
Takashi Ono	|Japan	|13|
Edoardo Mangiarotti|	Italy|	13
Ole Einar Bjrndalen|	Norway	|13
Dara Grace Torres (-Hoffman, -Minas)|	United States|	12
Ryan Steven Lochte	|United States	|12
Natalie Anne Coughlin (-Hall)|	United States	|12
Paavo Johannes Nurmi	|Finland	|12
Sawao Kato|	Japan	|12|
Jennifer Elisabeth "Jenny" Thompson (-Cumpelik)|	United States|	12
Aleksey Yuryevich Nemov|	Russia	|12

</p>
</details>

            13. Fetch the top 5 most successful countries in olympics. Success is defined by no of medals won.

            WITH T1 AS 
                 ( SELECT nr.region, count(1) as total_medals
                  FROM olympics_history o
                  JOIN olympics_history_noc_regions nr
                  ON nr.noc = o.noc
                  WHERE medal <> 'NA'
                  GROUP BY nr.region
                  ORDER BY total_medals DESC),
                  T2 AS
                  (SELECT * , DENSE_RANK() OVER(ORDER BY total_medals desc) as rnk
                  FROM T1)

                  SELECT *
                  FROM T2
                  WHERE rnk <= 5;

<details><summary>SHOW ANSWER</summary>
<p>

region|	total_medals|	rnk|
|------|------|----|
USA	|5637	|1
Russia|	3947	|2
Germany	|3756	|3
UK	|2068|	4
France	|1777|	5

</p>
</details>

          14. List down total gold, silver and bronze medals won by each country.

          crosstab function to create pivot table crosstab function in a part of extension called tablefunc

          CREATE EXTENSION TABLEFUNC;

          SELECT country,
            coalesce(gold,0) as gold,
            coalesce(silver,0) as silver,
            coalesce(bronze,0) as bronze
            FROM CROSSTAB('SELECT nr.region as country, medal,
                    count(1) as total_medals
                    FROM olympics_history o
                    JOIN olympics_history_noc_regions nr
                    ON nr.noc = o.noc
                    where medal <> ''NA''
                   GROUP BY nr.region, medal
                   ORDER BY nr.region,medal',
                   'values (''Bronze''),(''Gold''),(''Silver'')')
                   AS FINAL_RESULT(country varchar, bronze bigint, gold bigint, silver bigint)
                   ORDER BY gold desc, silver desc, bronze desc;
         
<details><summary>SHOW ANSWER</summary>
<p>
         
| country	|gold|	silver|	bronze|
|-----|----|-----|------|
USA	|2638|	1641	|1358|
Russia|	1599	|1170|	1178
Germany	|1301|	1195|	1260
UK	|678	|739	|651
Italy	|575	|531	|531
France	|501	|610	|666
Sweden	|479	|522	|535
Canada	|463	|438	|451
Hungary	|432	|332	|371
Norway	|378	|361	|294
Australia	|368	|459|	522
|....|.....|.....|....|
Iraq|	0	|0	|1|
Bermuda|	0	|0	|1|

</p>
</details>
        15. List down total gold, silver and bronze medals won by each country corresponding to each olympic games.

        SELECT substring(games,1,position(' - ' in games) - 1) as games,
            substring(games,position(' - ' in games) + 3) as country,
            coalesce(gold,0) as gold,
            coalesce(silver,0) as silver,
            coalesce(bronze,0) as bronze
            FROM CROSSTAB('SELECT concat(games, '' - '', nr.region) as games
                    , medal,
                    count(1) as total_medals
                    FROM olympics_history o
                    JOIN olympics_history_noc_regions nr
                    ON nr.noc = o.noc
                    WHERE medal <> ''NA''
                   GROUP BY games, nr.region,medal
                   ORDER BY games, medal',
                   'values(''Bronze''),(''Gold''),(''Silver'')')

                   AS FINAL_RESULT(games text, bronze bigint, gold bigint, silver bigint);
           
<details><summary>SHOW ANSWER</summary>
<p>

games	|country|	gold|	silver|	bronze|
|----|-----|-----|----|-----|
1896 Summer|	Australia	|2	|0	|1
1896 Summer|	Austria|	2	|1	|2
1896 Summer	|Denmark	|1|	2	|3
1896 Summer	|France	|5	|4|	2
1896 Summer	|Germany	|25	|5	|2
1896 Summer|	Greece	|10|	18|	20
1896 Summer	|Hungary	|2	|1	|3
1896 Summer	|Switzerland|	1	|2|	0
1896 Summer	|UK	|3	|3|	3
1896 Summer|	USA	|11	|7	|2
1900 Summer	|Australia	|3|	0|	3
1900 Summer	|Austria	|0|	3	|3
1900 Summer	|Belgium	|6	|24|	13
1900 Summer	|Canada	|1|	0	|1
1900 Summer	|Colombia	|0	|1|	0|
1900 Summer	|Cuba|	1	|1|	0|
1900 Summer|	Czech Republic	|0|	1|	2
1900 Summer	|Denmark|	4	|3	|2
1900 Summer	|France|	52|	101|	82

</p>
</details>

        16.  Identify which country won the most gold, most silver and most bronze medals in each olympic games.

         WITH temp as
              (SELECT substring(games, 1, position(' - ' in games) - 1) as games
                , substring(games, position(' - ' in games) + 3) as country
                    , coalesce(gold, 0) as gold
                    , coalesce(silver, 0) as silver
                    , coalesce(bronze, 0) as bronze
              FROM CROSSTAB('SELECT concat(games, '' - '', nr.region) as games
                      , medal
                        , count(1) as total_medals
                      FROM olympics_history oh
                      JOIN olympics_history_noc_regions nr ON nr.noc = oh.noc
                      where medal <> ''NA''
                      GROUP BY games,nr.region,medal
                      order BY games,medal',
                          'values (''Bronze''), (''Gold''), (''Silver'')')
                     AS FINAL_RESULT(games text, bronze bigint, gold bigint, silver bigint))
            select distinct games
              , concat(first_value(country) over(partition by games order by gold desc)
                  , ' - '
                  , first_value(gold) over(partition by games order by gold desc)) as Max_Gold
              , concat(first_value(country) over(partition by games order by silver desc)
                  , ' - '
                  , first_value(silver) over(partition by games order by silver desc)) as Max_Silver
              , concat(first_value(country) over(partition by games order by bronze desc)
                  , ' - '
                  , first_value(bronze) over(partition by games order by bronze desc)) as Max_Bronze
            from temp
            order by games;

          WITH temp as
              (SELECT substring(games, 1, position(' - ' in games) - 1) as games
                , substring(games, position(' - ' in games) + 3) as country
                    , coalesce(gold, 0) as gold
                    , coalesce(silver, 0) as silver
                    , coalesce(bronze, 0) as bronze
              FROM CROSSTAB('SELECT concat(games, '' - '', nr.region) as games
                      , medal
                        , count(1) as total_medals
                      FROM olympics_history oh
                      JOIN olympics_history_noc_regions nr ON nr.noc = oh.noc
                      where medal <> ''NA''
                      GROUP BY games,nr.region,medal
                      order BY games,medal',
                          'values (''Bronze''), (''Gold''), (''Silver'')')
                     AS FINAL_RESULT(games text, bronze bigint, gold bigint, silver bigint))
            select distinct games
              , concat(first_value(country) over(partition by games order by gold desc)
                  , ' - '
                  , first_value(gold) over(partition by games order by gold desc)) as Max_Gold
              , concat(first_value(country) over(partition by games order by silver desc)
                  , ' - '
                  , first_value(silver) over(partition by games order by silver desc)) as Max_Silver
              , concat(first_value(country) over(partition by games order by bronze desc)
                  , ' - '
                  , first_value(bronze) over(partition by games order by bronze desc)) as Max_Bronze
            from temp
            order by games;
    
<details><summary>SHOW ANSWER</summary>
<p>
	
|games|	max_gold|	max_silver	|max_bronze|
|------------|--------|---------|--------|
1896 Summer|	Germany - 25	|Greece - 18|	Greece - 20
1900 Summer|	UK - 59	|France - 101|	France - 82
1904 Summer	|USA - 128|	USA - 141|	USA - 125
1906 Summer|	Greece - 24	|Greece - 48|	Greece - 30
1908 Summer	|UK - 147|	UK - 131|	UK - 90
1912 Summer	|Sweden - 103|	UK - 64|	UK - 59
1920 Summer	|USA - 111|	France - 71	|Belgium - 66
1924 Summer|	USA - 97	|France - 51	|USA - 49|

</p>
</details>

        17. Identify which country won the most gold, most silver, most bronze medals and the most medals in each olympic games.
           with temp as
              (SELECT substring(games, 1, position(' - ' in games) - 1) as games
                , substring(games, position(' - ' in games) + 3) as country
                , coalesce(gold, 0) as gold
                , coalesce(silver, 0) as silver
                , coalesce(bronze, 0) as bronze
              FROM CROSSTAB('SELECT concat(games, '' - '', nr.region) as games
                      , medal
                      , count(1) as total_medals
                      FROM olympics_history oh
                      JOIN olympics_history_noc_regions nr ON nr.noc = oh.noc
                      where medal <> ''NA''
                      GROUP BY games,nr.region,medal
                      order BY games,medal',
                          'values (''Bronze''), (''Gold''), (''Silver'')')
                     AS FINAL_RESULT(games text, bronze bigint, gold bigint, silver bigint)),
              tot_medals as
                (SELECT games, nr.region as country, count(1) as total_medals
                FROM olympics_history oh
                JOIN olympics_history_noc_regions nr ON nr.noc = oh.noc
                where medal <> 'NA'
                GROUP BY games,nr.region order BY 1, 2)
            select distinct t.games
              , concat(first_value(t.country) over(partition by t.games order by gold desc)
                  , ' - '
                  , first_value(t.gold) over(partition by t.games order by gold desc)) as Max_Gold
              , concat(first_value(t.country) over(partition by t.games order by silver desc)
                  , ' - '
                  , first_value(t.silver) over(partition by t.games order by silver desc)) as Max_Silver
              , concat(first_value(t.country) over(partition by t.games order by bronze desc)
                  , ' - '
                  , first_value(t.bronze) over(partition by t.games order by bronze desc)) as Max_Bronze
              , concat(first_value(tm.country) over (partition by tm.games order by total_medals desc nulls last)
                  , ' - '
                  , first_value(tm.total_medals) over(partition by tm.games order by total_medals desc nulls last)) as Max_Medals
            from temp t
            join tot_medals tm on tm.games = t.games and tm.country = t.country
            order by games;
    
<details><summary>SHOW ANSWER</summary>
<p>
    
| games|	max_gold|	max_silver	|max_bronze|	max_medals|
|---|----|-----------|----------|------------|
1896 Summer|	Germany - 25|	Greece - 18|	Greece - 20|	Greece - 48
1900 Summer|	UK - 59	|France - 101|	France - 82|	France - 235
1904 Summer	|USA - 128	|USA - 141	|USA - 125	|USA - 394
1906 Summer	|Greece - 24|	Greece - 48	|Greece - 30|	Greece - 102
1908 Summer|	UK - 147|	UK - 131|	UK - 90|	UK - 368|

</p>
</details>

      18.  Which countries have never won gold medal but have won silver/bronze medals?
      select * from (
            SELECT country, coalesce(gold,0) as gold, coalesce(silver,0) as silver, coalesce(bronze,0) as bronze
              FROM CROSSTAB('SELECT nr.region as country
                    , medal, count(1) as total_medals
                    FROM OLYMPICS_HISTORY oh
                    JOIN OLYMPICS_HISTORY_NOC_REGIONS nr ON nr.noc=oh.noc
                    where medal <> ''NA''
                    GROUP BY nr.region,medal order BY nr.region,medal',
                          'values (''Bronze''), (''Gold''), (''Silver'')')
              AS FINAL_RESULT(country varchar,
              bronze bigint, gold bigint, silver bigint)) x
          where gold = 0 and (silver > 0 or bronze > 0)
          order by gold desc nulls last, silver desc nulls last, bronze desc nulls last;
<details><summary>SHOW ANSWER</summary>
<p>
    
|country	|gold|	silver|	bronze|
|-----|-------|---------|-----|
Paraguay|	0	|17|	0
Iceland|	0	|15	|2
Montenegro|	0|	14|	0
Malaysia	|0	|11|	5
Namibia	|0	|4|	0
Philippines|	0	|3	|7
Moldova	|0	|3	|5
Lebanon	|0	|2|	2
Sri Lanka	|0	|2	|0
Tanzania|	0|	2	|0
Ghana	|0	|1|	22
|....|...|..|..|


</p>
</details>
      19.  In which Sport/event, India has won highest medals.

        with t1 as
                (select sport, count(1) as total_medals
                from olympics_history
                where medal <> 'NA'
                and team = 'India'
                group by sport
                order by total_medals desc),
              t2 as
                (select *, rank() over(order by total_medals desc) as rnk
                from t1)
          select sport, total_medals
          from t2
          where rnk = 1;

<details><summary>SHOW ANSWER</summary>
<p>
    
|sport	|total_medals|
|-----|-----------|
Hockey|	173|

</p>
</details>
