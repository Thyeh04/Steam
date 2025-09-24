📌 The International (Dota 2) Dataset Analysis
📖 Overview

This project analyzes The International (TI) Dota 2 tournament data using PostgreSQL and Python.

Goals:

Analyze prize pool standings

Compute group stage rankings

Check country representation of players

Extract playoff results and statistics

📂 Dataset

CSV files (from https://www.kaggle.com/datasets/arpan129/dota-2-the-international-complete-dataset):


Notes:

Remove the first index column before importing

Do not include id (Postgres auto-generates it)

Numeric fields (prize_amount, wins, points_num) must be cleaned

🛠️ Database Setup
Import CSV files into the tables using pgAdmin or COPY FROM.
<img width="1090" height="541" alt="image" src="https://github.com/user-attachments/assets/7873528e-a2e6-4db2-9342-44942e328252" />

🔧 Python Script: project_DATA.py

The Python script connects to the database, executes queries, and displays results in a readable table format using pandas.
Database connection example:

DB_USER = "ti_user"
DB_PASS = "MyStrongPass123"
DB_NAME = "ti_db"
DB_HOST = "localhost"
DB_PORT = "5432"


Run queries and display results:

```import pandas as pd
from sqlalchemy import create_engine

engine = create_engine(f"postgresql+psycopg2://{DB_USER}:{DB_PASS}@{DB_HOST}:{DB_PORT}/{DB_NAME}")
```
🔹 Queries in Python
1. Prize Pool Standings
# Rank teams by prize money
```q1 = "SELECT team, prize_amount::BIGINT AS prize_usd FROM prize_pool ORDER BY prize_usd DESC;"
df1 = pd.read_sql(q1, engine)
print(df1)
```

Interpretation: Shows teams with highest earnings.

2. Group A Standings
# Standings with wins, draws, losses, and calculated points
```q2 = """
SELECT team, wins, draws, losses, (wins*2 + COALESCE(draws,0)) AS points_num
FROM group_a_clean
ORDER BY points_num DESC;
"""
df2 = pd.read_sql(q2, engine)
print(df2)
```

Interpretation: Lists Group A teams by performance points.

3. Group B Standings
# Same as Group A but for Group B
```q3 = """
SELECT team, wins, draws, losses, (wins*2 + COALESCE(draws,0)) AS points_num
FROM group_b_clean
ORDER BY points_num DESC;
"""
df3 = pd.read_sql(q3, engine)
print(df3)
```
4. Players per Country
# Count players for each country
```q4 = "SELECT country, num_from_country AS players_count FROM country_rep_clean ORDER BY players_count DESC;"
df4 = pd.read_sql(q4, engine)
print(df4)
```

Interpretation: Shows country representation of players.

5. Grand Final Matches
# Fetch Grand Final matches
```q5 = "SELECT id, round, team1, score1, team2, score2, winner FROM playoffs WHERE round ILIKE '%grand final%' ORDER BY id;"
df5 = pd.read_sql(q5, engine)
print(df5)
```

Interpretation: Displays final matches and results.

6. Teams with Group Points + Prize
# Join group points with prize money
```q6 = """
SELECT g.team, g.points_num, prize_amount::BIGINT AS prize_usd
FROM (
  SELECT team, (wins*2 + COALESCE(draws,0)) AS points_num FROM group_a_clean
  UNION ALL
  SELECT team, (wins*2 + COALESCE(draws,0)) AS points_num FROM group_b_clean
) g
LEFT JOIN prize_pool p ON lower(trim(p.team)) = lower(trim(g.team))
ORDER BY prize_usd DESC NULLS LAST;
"""
df6 = pd.read_sql(q6, engine)
print(df6)
```

Interpretation: Combines points and earnings for all teams.

7. Top Team Overall
# Find the best team across both groups
```q7 = """
SELECT team, wins, points_num
FROM (
  SELECT team, wins, points_num FROM group_a_clean
  UNION ALL
  SELECT team, wins, points_num FROM group_b_clean
) all_groups
ORDER BY wins DESC, points_num DESC
LIMIT 1;
"""
df7 = pd.read_sql(q7, engine)
print(df7)
```

Interpretation: Shows the single top-performing team.

8. Total Playoff Matches
# Count total playoff matches
```q8 = "SELECT COUNT(*) AS total_matches FROM playoffs_matches;"
df8 = pd.read_sql(q8, engine)
print(df8)
```

Interpretation: Shows total number of matches in playoffs.

9. Country + Teams Count
# Count teams per country
```q9 = """
SELECT cr.country, cr.num_from_country AS players, COUNT(DISTINCT g.team) AS teams_count
FROM country_rep_clean cr
JOIN (
  SELECT team, 'China' AS country FROM group_a_clean
  UNION ALL
  SELECT team, 'Ukraine' AS country FROM group_b_clean
) g ON cr.country = g.country
GROUP BY cr.country, cr.num_from_country
ORDER BY teams_count DESC;
"""
df9 = pd.read_sql(q9, engine)
print(df9)
```

Interpretation: Combines player count and number of teams per country.

10. Total Prize Money
# Sum of all prize money
```q10 = "SELECT SUM(prize_amount::BIGINT) AS total_prize_usd FROM prize_pool;"
df10 = pd.read_sql(q10, engine)
print(df10)
```

Interpretation: Shows total prize money awarded across all teams.

▶️ Run
```pip install psycopg2 pandas
python "project_DATA.py"
```
