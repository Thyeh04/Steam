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

import pandas as pd
from sqlalchemy import create_engine

engine = create_engine(f"postgresql+psycopg2://{DB_USER}:{DB_PASS}@{DB_HOST}:{DB_PORT}/{DB_NAME}")

query = "SELECT team, prize_amount::BIGINT AS prize_usd FROM prize_pool ORDER BY prize_usd DESC;"
df = pd.read_sql(query, engine)
print(df)


Close connection after execution:

engine.dispose()

▶️ Run

Install dependencies:

pip install psycopg2 pandas


Ensure PostgreSQL is running and the database ti_db is accessible.

Run the script:

python "project_DATA.py"


Results are printed as pandas tables in the console.

📸 Example Output

Prize Pool Standings:

Team	Prize USD
Team Secret	4000000
OG	3500000

Country Representation:

Country	Players
China	5
Ukraine	4
