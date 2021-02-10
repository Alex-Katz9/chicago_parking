Completed using SQLite3 in Jupyter Notebook

Please complete this exercise using SQL Lite (i.e., the Lahman baseball data, above) and your Jupyter notebook.

__What was the total spent on salaries by each team, each year?__
SELECT * FROM salaries GROUP BY teamID, yearID;

__What is the first and last year played for each player? Hint: Create a new table from 'Fielding.csv'.__

SELECT playerID, yearID
FROM (
SELECT *, ROW_NUMBER()
OVER (PARTITION BY playerID ORDER BY yearID ASC) rn
FROM fielding)
WHERE rn =1

UNION

SELECT playerID, yearID
FROM (
SELECT *, ROW_NUMBER()
OVER (PARTITION BY playerID ORDER BY yearID DESC) rn
FROM fielding)
WHERE rn =1
ORDER BY playerID, yearID


__Who has played the most all star games?__

SELECT playerID,
ROW_NUMBER() OVER (PARTITION BY playerID ORDER BY yearID) AS num_appearance
FROM allstarfull
ORDER BY num_appearance DESC
;


Willie Mays, Hank Aaron (RIP), and Stan Musial all had 24 appearances.

__Which school has generated the most distinct players? Hint: Create new table from 'CollegePlaying.csv'.__

WITH colleges AS
(SELECT playerID, schoolID, yearID
FROM (
SELECT * , ROW_NUMBER()
OVER (PARTITION BY playerID ORDER BY yearID DESC) rn
FROM collegeplaying)
WHERE rn =1
)
SELECT schoolID,
ROW_NUMBER() OVER (PARTITION BY schoolID ORDER BY yearID) AS num_players
FROM colleges
ORDER BY num_players DESC;

Texas and USC have produced, by which school players last appeared with, the most players to appear in the MLB with 103.

__Which players have the longest career? Assume that the debut and finalGame columns comprise the start and end, respectively, of a player's career. Hint: Create a new table from 'Master.csv'. Also note that strings can be converted to dates using the DATE function and can then be subtracted from each other yielding their difference in days.__

SELECT
playerID, debut, finalGame,
(DATE(finalGame) - DATE(debut)) AS 'Career'
FROM people
ORDER BY Career DESC
;

__What is the distribution of debut months? Hint: Look at the DATE and EXTRACT functions.__

SELECT
strftime('%m', debut) AS 'Debut_Month',
COUNT(*)
FROM people
GROUP BY Debut_Month
;

__What is the effect of table join order on mean salary for the players listed in the main (master) table? Hint: Perform two different queries, one that joins on playerID in the salary table and other that joins on the same column in the master table. You will have to use left joins for each since right joins are not currently supported with SQLalchemy.__

The order of joining determines which table will retain all of its playerIDs (and related records). So, if we do people LEFT JOIN salaries, we will have a table with all of the players in the people table, along with their salary data if it existed. If we do salaries LEFT JOIN people, we will have a table with all of the players in the salaries table, along with their info from the people table if it existed. That means, that our resulting table could have a different set of players. In our case, it appears as though one player, Hank Aaron, seems to be without salary data and therefore left out or left without average salary, depending on the other.
