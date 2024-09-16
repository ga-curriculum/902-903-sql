# SQL I
- `SELECT * FROM bigquery-public-data.ncaa_basketball.mascots`
- SQL is case-insensitive. ; optional (usually).
- `SELECT market, name FROM bigquery-public-data.ncaa_basketball.mascots`
- add LIMIT
- Check out ehc/incent/incent-participant/models?
- SLACK: get me `turner_name, name` from mbb_teams
- `SELECT DISTINCT(conf_name) FROM ...mbb_teams`
- `SELECT DISTINCT conf_name, conf_id FROM ...mbb_teams`
- ^ Cool! It's a map
- `SELECT COUNT(conf_name) FROM ...mbb_teams`
- `SELECT COUNT(DISTINCT conf_name) FROM ...mbb_teams`
- `SELECT COUNT(*) FROM ...mbb_teams`
- `SELECT AVG(venue_capacity) FROM ...mbb_teams`
- Aliasing above names to be pretty
- Break
- Show `mbb_players_games_sr`
- `SELECT DISTINCT full_name, height FROM ...mbb_players_games_sr`
- `ORDER BY height DESC` (get messed up by nulls)
- `SELECT last_name || ', ' || first_name FROM ...mbb_players_games_sr`
-
    ```
    SELECT
        last_name || ', ' || first_name AS new_name,
        height AS ht
    FROM ...mbb_players_games_sr
    ```
- AS is optional
- `SELECT m.market, m.name FROM ...mascots AS m`

# SQL II
- Take look at schema if possible?
- Warmup: Show me teams with top 10 biggest venues. Show me only turner name, venue name, venue capacity from `mbb_teams`
- Add `WHERE venue_capacity >= 20000`
- Show it all together with ORDER BY
- `WHERE venue_capacity >= 20000 OR venue_capacity < 1000`
- `WHERE venue_capacity >= 20000 AND venue_capacity < 25000`
- `SELECT * FROM ...mascots WHERE name = 'Tigers'` <- No ==!
    - Only use single quotes. Double quotes usage unpredictable and differs by SQL dialect
- `WHERE LOWER(name) = 'tigers'`
- `WHERE market LIKE '%State%'` vs `%State`
- `ILIKE` <- not in BQ
- `WHERE market ~ '*State*'` <- not in BQ?
- `WHERE regexp_contains(market, '.*State.*')`
- `SELECT * FROM ...mascots WHERE mascot_name IS NULL` vs `IS NOT NULL`
- Show `...team_colors` note ID should match up with mascots
- 
    ```
    SELECT *
    FROM ...mascots AS m
    LEFT JOIN ...team_colors AS c
    ON m.id = c.id AND m.market = c.market
    ``` 
- Change * to: `m.*, c.code_ncaa, c.color`
- SLACK: From `mbb_teams`, give me turner name, conf name, venue name, mascot, mascot name, but only for schools in PA.
    ```
    SELECT 
        t.turner_name,
        t.conf_name,
        t.venue_name,
        m.mascot,
        m.mascot_name
    FROM ...mbb_teams AS t
    LEFT JOIN ...mascots AS m
    ON t.id, m.id
    WHERE t.venue_state = 'PA'
    ``` 
- Can slap on `...team_colors AS c ON t.id = m.id`
- Look at avg venue capac over time: `SELECT AVG(venue_capacity) FROM ...mbb_games_sr GROUP BY season`
- `SELECT season, AVG(venue_capacity) AS avg_capacity FROM ...mbb_games_sr GROUP BY season ORDER BY season`
- `SELECT season, AVG(venue_capacity), count(*), AVG(h_points), AVG(a_points)`
- SLACK: Show me the top 10 schools with the most attended home games
    ```
    SELECT h_market, ROUND(AVG(attendance, 2)) AS avg_attendance
    FROM ...mbb_games_sr
    GROUP BY h_market
    ```
- Tack on: `HAVING avg_attendance > 20000`
- SLACK: What is the most lopsided matchup in these five years of NCAA history?
    ```
    SELECT
        h_market,
        a_market,
        AVG(ABS(h_points - a_points)) AS avg_point_diff
    FROM ...mbb_games_sr
    GROUP BY h_market, a_market
    ```
    Proceed to fix up

# SQL III
- WARMUP: Show me all (unique) mbb_players_games_sr lname, fname, heights, sorted desc by last name.
- Demonstrate CASE WHEN to make players tall or short
    ```
    SELECT DISTINCT
        last_name,
        first_name,
        height,
        CASE
            WHEN height >= 78 THEN 'Tall'
            ELSE 'Short'
        END AS is_tall
    FROM ...mbb_players_games_sr
    WHERE height IS NOT NULL AND height <> 0
    ORDER BY last_name
    ```
- Datetime stuff
    ```
    SELECT
        gametime,
        EXTRACT (YEAR FROM gametime) AS year,
        EXTRACT (MONTH FROM gametime) AS month,
        EXTRACT (DAY FROM gametime) AS day
        FORMAT_DATETIME('%Y-%m-%d', gametime) AS gamedate
    FROM ...mbb_players_games_sr
    ```
    Note that it's STRFTIME in sqlite3
- SLACK: Show me all mbb_games_sr where either the home OR away team is Ohio State in 2015, sort by gametime. Show me home/away scores
- HARD MODE: Make a `osu_points` that is the amount of points OSU scored
- Talk about CTEs:
    ```
    WITH osu_games AS (
      SELECT
        gametime,
        FORMAT_DATETIME("%Y-%m-%d", gametime) as gamedate,
        CASE
          WHEN h_market = 'Ohio State' THEN h_points
          WHEN a_market = 'Ohio State' THEN a_points
        END AS osu_points
      FROM `bigquery-public-data.ncaa_basketball.mbb_games_sr`
      WHERE
        h_market = 'Ohio State' OR a_market = 'Ohio State'
        AND season = 2015
      ORDER BY gametime DESC
    )
    SELECT
      gamedate,
      osu_points,
      ROUND(AVG(osu_points) OVER (ORDER BY gametime)) AS cum_mean_points
    FROM osu_games
    ```
- Rolling statistics:
    `AVG(osu_points) OVER (ORDER BY gametime ROWS BETWEEN 4 PRECEDING AND 0 PRECEDING)`
- Show me all teams that have a player above 86in tall. However, height only exists in mbb_players_games_sr, but I want the data from mbb_teams
    - Use a subquery with IN:
    ```
    SELECT id, turner_name, name
    FROM ...mbb_teams
    WHERE id IN (
        SELECT DISTINCT team_id
        FROM ...mbb_players_games_sr
        WHERE height > 86
    )
    ```
- Can also use an inner join:
    ```
    WITH tall_teams AS (
      SELECT DISTINCT team_id
      FROM `bigquery-public-data.ncaa_basketball.mbb_players_games_sr`
      WHERE height > 86
    )
    SELECT *
    FROM `bigquery-public-data.ncaa_basketball.mbb_teams` AS t
    INNER JOIN tall_teams AS cte ON t.id = cte.team_id
    ```
