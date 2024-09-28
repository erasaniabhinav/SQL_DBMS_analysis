# Population Projection and Analysis

This project analyzes the population projections of California counties based on gender and race for various years, using SQL for database management and querying.

## Project Overview

The dataset contains population projections from 2010 to 2060, including details such as county code, county name, gender, race, and age. The project focuses on analyzing population data for different counties, genders, and races for specific years, with a focus on 2014 in particular.

### Database Schema

- **Database Name**: `ca_population`
- **Table Name**: `pop_proj`

The table contains the following columns:
- `county_code`: Unique identifier for each county.
- `county_name`: Name of the county.
- `date_year`: Year of the population projection.
- `race_code`: Numerical code for race.
- `race`: Descriptive name of the race.
- `gender`: Gender (Male/Female).
- `age`: Age of the population group.
- `population`: Population count.

### SQL Scripts Overview

1. **Creating the Database and Table**:
    - Creates a new database schema `ca_population`.
    - Defines the `pop_proj` table to store population projection data.

    ```sql
    CREATE SCHEMA ca_population;

    USE ca_population;

    CREATE TABLE pop_proj(
        county_code VARCHAR(45) NOT NULL,
        county_name VARCHAR(45) NOT NULL,
        date_year INT NOT NULL,
        race_code INT NOT NULL,
        race TEXT NOT NULL,
        gender VARCHAR(6) NOT NULL,
        age INT NOT NULL,
        population INT NOT NULL
    );
    ```

2. **Loading the Data**:
    - Loads data from a CSV file (`CA_DRU_proj_2010-2060.csv`) into the `pop_proj` table.
    - Ignores the first header line and specifies the delimiter settings.

    ```sql
    LOAD DATA LOCAL INFILE 'C:\\Users\\User\\CA_DRU_proj_2010-2060.csv'
    INTO TABLE pop_proj
    FIELDS TERMINATED BY ','
    ENCLOSED BY '"'
    LINES TERMINATED BY '\n'
    IGNORE 1 LINES;
    ```

3. **Creating Index**:
    - Creates an index on `county_name` to improve query performance.

    ```sql
    CREATE INDEX county_name
    ON pop_proj(county_name);
    ```

4. **Querying Data**:
    - Initial query to retrieve a summary of the population by gender in each county for the year 2014.

    ```sql
    SELECT county_name, gender, SUM(population) AS total_population
    FROM pop_proj
    WHERE date_year = 2014
    GROUP BY county_name, gender
    ORDER BY county_name;
    ```

    - A more complex query to retrieve a formatted table showing male and female populations for each county in 2014.

    ```sql
    SELECT p.county_name, 
        SUM(p.population) AS Male, 
        female_pop.Female 
    FROM 
        (SELECT county_name, SUM(population) AS Female
        FROM pop_proj
        WHERE date_year = 2014 AND gender = 'Female'
        GROUP BY county_name
        ORDER BY county_name) AS female_pop
    JOIN pop_proj p
    ON p.county_name = female_pop.county_name
    WHERE p.date_year = 2014 AND p.gender = 'Male'
    GROUP BY p.county_name
    ORDER BY p.county_name;
    ```

### How to Run the Project

1. Create the database and table by running the `CREATE SCHEMA` and `CREATE TABLE` commands.
2. Load the population data using the `LOAD DATA` command.
3. Execute the queries to analyze the data for specific years or conditions.
4. Create the index to improve query performance when dealing with large datasets.

### Data Source

The data for this project comes from the California Department of Finance's Demographic Research Unit (DRU), containing population projections by race, age, and gender for each county.

### Key Learnings

- Using SQL to manage and analyze large datasets efficiently.
- Creating and indexing tables for faster querying.
- Writing complex queries to join and aggregate data based on multiple conditions.
