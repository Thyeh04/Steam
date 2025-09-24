📌 Project: Analysis of The International (Dota 2) Dataset
📖 Overview

This project analyzes The International (TI) Dota 2 tournament dataset using PostgreSQL and pgAdmin. The dataset includes prize pool standings, group stage results, playoff matches, and country representation of players.

The goal is to store the data in a relational database and run SQL queries to extract insights such as:

Which teams won the most prize money

Group stage rankings

Country representation

Playoff results and statistics

📂 Dataset Files

The dataset (CSV files) comes from Liquipedia.net (scraped).
The following files are used:

prize_pool.csv → Prize money and final standings

playoffs.csv → Main event playoff matches

group_a.csv → Group A standings

group_b.csv → Group B standings

country_representation.csv → Countries and player

🛠️ Database Setup

Create a database in pgAdmin (e.g., dota2_ti).

Run the following table creation scripts:

-- Prize pool table
CREATE TABLE prize_pool (
    id SERIAL PRIMARY KEY,
    place TEXT,
    prize_amount NUMERIC,
    percent TEXT,
    team TEXT
);

-- Playoffs table
CREATE TABLE playoffs (
    id SERIAL PRIMARY KEY,
    round TEXT,
    team1 TEXT,
    score1 INT,
    team2 TEXT,
    score2 INT,
    winner TEXT
);

-- Group A standings
CREATE TABLE group_a_clean (
    id SERIAL PRIMARY KEY,
    team TEXT,
    wins INT,
    draws INT,
    losses INT,
    points_num INT
);

-- Group B standings
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


Import CSV files into their respective tables.

Remove the first index column from each CSV before importing (those 0,1,2... rows are just counters).

Do not include id in the import (Postgres auto-generates it).

Ensure numeric fields (prize_amount, wins, points_num) are cleaned (no $, %, or text).

See queries.sql
 for the full list (10 queries). Examples:

-- Top 5 teams by prize money
SELECT team, prize_amount
FROM prize_pool
ORDER BY prize_amount DESC
LIMIT 5;
<img width="1404" height="371" alt="image" src="https://github.com/user-attachments/assets/561daee9-78fb-4941-9945-e4cf929c0abd" />

-- Total prize pool distributed
SELECT SUM(prize_amount) AS total_prize_pool
FROM prize_pool;

-- Group A standings sorted by wins
SELECT team, wins, points_num
FROM group_a_clean
ORDER BY wins DESC, points_num DESC;


📌 Python Script (Data Analysis)

The project includes a script project DATA.py that connects to the PostgreSQL database and displays query results in a readable tabular format using pandas.

🔧 Main Steps:

Connects to the database ti_db using the user ti_user.

Executes SQL queries to fetch:

Prize Pool Standings – ranking teams by total earnings.

Group A Standings – table with wins, draws, losses, and calculated points.

Group B Standings – same structure for Group B teams.

Country Representation – list of countries and their associated teams.

Prints the data as tables in the console using pandas.

Closes the database connection after execution.

📚 Libraries Used:

psycopg2 – PostgreSQL connection driver

pandas – tabular data handling and display

▶️ How to Run:

Install dependencies:

pip install psycopg2 pandas


Make sure PostgreSQL is running and the database ti_db is accessible.

Run the script:

python "project DATA.py"
