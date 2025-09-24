📌 Project: Analysis of The International (Dota 2) Dataset
📖 Overview

This project analyzes The International (TI) Dota 2 tournament dataset.

Goal: Store data in PostgreSQL and extract insights using SQL and Python.

Key insights include:

Teams with the highest prize money

Group stage rankings

Country representation of players

Playoff results and statistics

📂 Dataset

CSV files (source: https://www.kaggle.com/datasets/arpan129/dota-2-the-international-complete-dataset):



Notes:

Remove the first index column (0,1,2...) before importing.

Do not import id (Postgres auto-generates it).

Numeric fields (prize_amount, wins, points_num) must be cleaned (no $, %, or text).

🛠️ Database Setup

Create a database, e.g., ti_db, and tables:

-- Prize pool
CREATE TABLE prize_pool (
    id SERIAL PRIMARY KEY,
    place TEXT,
    prize_amount NUMERIC,
    percent TEXT,
    team TEXT
);

-- Playoffs
CREATE TABLE playoffs (
    id SERIAL PRIMARY KEY,
    round TEXT,
    team1 TEXT,
    score1 INT,
    team2 TEXT,
    score2 INT,
    winner TEXT
);

-- Group A
CREATE TABLE group_a_clean (
    id SERIAL PRIMARY KEY,
    team TEXT,
    wins INT,
    draws INT,
    losses INT,
    points_num INT
);

-- Group B
CREATE TABLE group_b_clean (
    id SERIAL PRIMARY KEY,
    team TEXT,
    wins INT,
    draws INT,
    losses INT,
    points_num INT
);

-- Country representation
CREATE TABLE country_rep_clean (
    id SERIAL PRIMARY KEY,
    country TEXT,
    num_from_country INT,
    players TEXT
);
<img width="1090" height="541" alt="Снимок экрана 2025-09-24 081213" src="https://github.com/user-attachments/assets/f658e128-8250-480e-ad2d-2a1d7aa48943" />


Import CSV files into respective tables using pgAdmin or COPY FROM.

🔧 Python Script: project_DATA.py

The script connects to PostgreSQL, runs 10 SQL queries, and prints results as tables using pandas.

Database connection:
DB_USER = "ti_user"
DB_PASS = "MyStrongPass123"
DB_NAME = "ti_db"
DB_HOST = "localhost"
DB_PORT = "5432"

🔹 10 SQL Queries Executed

Prize Pool Standings (team + prize_usd)

```SELECT team, prize_amount::BIGINT AS prize_usd
FROM prize_pool
ORDER BY prize_usd DESC NULLS LAST;
```

Group A Standings

SELECT team, wins, draws, losses, (wins * 2 + COALESCE(draws,0)) AS points_num
FROM group_a_clean
ORDER BY points_num DESC;


Group B Standings

SELECT team, wins, draws, losses, (wins * 2 + COALESCE(draws,0)) AS points_num
FROM group_b_clean
ORDER BY points_num DESC;


Number of Teams per Country

SELECT country, num_from_country AS players_count
FROM country_rep_clean
ORDER BY players_count DESC;


Grand Final Match(es)

SELECT id, round, team1, score1, team2, score2, winner
FROM playoffs
WHERE round ILIKE '%grand final%'
ORDER BY id;


JOIN: Teams with Group Points and Prize Money

SELECT g.team, g.points_num, prize_amount::BIGINT AS prize_usd
FROM (
    SELECT team, (wins * 2 + COALESCE(draws,0)) AS points_num FROM group_a_clean
    UNION ALL
    SELECT team, (wins * 2 + COALESCE(draws,0)) AS points_num FROM group_b_clean
) g
LEFT JOIN prize_pool p ON lower(trim(p.team)) = lower(trim(g.team))
ORDER BY prize_usd DESC NULLS LAST;


Top Team Across All Groups

SELECT team, wins, points_num
FROM (
    SELECT team, wins, points_num FROM group_a_clean
    UNION ALL
    SELECT team, wins, points_num FROM group_b_clean
) all_groups
ORDER BY wins DESC, points_num DESC
LIMIT 1;


Total Number of Playoff Matches

SELECT COUNT(*) AS total_matches
FROM playoffs_matches;


JOIN: Country Representation with Teams Count

SELECT cr.country, cr.num_from_country AS players, COUNT(DISTINCT g.team) AS teams_count
FROM country_rep_clean cr
JOIN (
    SELECT team, 'China' AS country FROM group_a_clean
    UNION ALL
    SELECT team, 'Ukraine' AS country FROM group_b_clean
) g ON cr.country = g.country
GROUP BY cr.country, cr.num_from_country
ORDER BY teams_count DESC;


Total Prize Money Across All Teams

SELECT SUM(prize_amount::BIGINT) AS total_prize_usd
FROM prize_pool;

▶️ How to Run

Install dependencies:

pip install psycopg2 pandas


Ensure PostgreSQL is running and ti_db is accessible.

Run the script:

python "project_DATA.py"


Results will be printed in the console as pandas tables.
