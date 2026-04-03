**Source:** [Kaggle - Video Game Sales](https://www.kaggle.com/datasets/gregorut/videogamesales)

## 1. Project Overview

### 1.1 Introduction
As a student in data science, I'm interested in understanding the numbers behind this success (success of videogames). For my project, I have chosen to analyze the **Global Video Game Sales** dataset. This dataset is a historical record of video game sales across various platforms, genres and regions (NA, EU, JP and etc).

## 2. Dataset Description

The dataset used in this analysis was sourced from Kaggle. It contains a listing of video games with sales greater than 100000 copies. Before beginning the analysis, it is crucial to define the variables (columns) present in the data.

### 2.1 Data Dictionary
The dataset consists of **16597 rows** and **11 columns**. Below is a breakdown of what each column represents:

1.  **Rank:** an integer representing the ranking of overall sales.
2.  **Name:** the title of the game (object - string).
3.  **Platform:** the console or system the game was released on (for example: PS4, Xbox360, PC).
4.  **Year:** the year of the game's release. *Note: in the raw data, this was stored as a float, but should be int*
5.  **Genre:** the category of the game (for example: action, sports, racing, role-playing).
6.  **Publisher:** the company responsible for publishing the game (e.g., Nintendo, Electronic Arts).
7.  **NA_Sales:** sales in the North America (in millions).
8.  **EU_Sales:** sales in the Europe (in millions).
9.  **JP_Sales:** sales in the Japan (in millions).
10. **Other_Sales:** sales in another countries.
11. **Global_Sales:** the total of sales (sum of previous four columns).

## 3. Data Preparation and сleaning

The first step of my analysis was to ensure the data was accurate and ready for processing. Raw data often contains errors or missing values that can ruin statistical results.

### 3.1 Loading the Data
I used the `pd.read_csv()` function to load the file. I wrapped this in a `try-except` block to ensure that if the file wasn't found, the program would alert me rather than crashing explicitly.
Upon loading, I used `df.shape` and `df.info()` to inspect the structure.
*   **Initial Rows:** 16597
*   **Initial Columns:** 11

### 3.2 Duplicate Detection
I checked for duplicate rows using `df.duplicated().sum()`.
*   **Result:** 0 duplicates
*   **Significance:** this is excellent. It means I don't need to worry about the same transaction being counted twice, which would influence in future.

### 3.3 Handling Missing Values
When I ran `df.isnull().sum()`, I discovered missing values in the **Year** and **Publisher** columns.
*   **Decision:** I decided to drop the rows with missing values.
*   **Reasoning:** The "Year" is critical for any timeline analysis. If a game doesn't have a year, I can't plot it on a time-series graph. Guessing the year (imputation) would be factually wrong for a specific product. Since the missing data constituted a very small percentage (less than 2%) of the total dataset, removing these rows `df.dropna(subset=['Year','Publisher'])` was the safest approach to maintain accuracy.

### 3.4 Data Type Conversion
A minor issue in the raw data was that the `Year` column was formatted as a float (e.g., `2008.0`). Years are discrete points in time, not continuous measurements.
*   **Action:** I converted this column to integers using `.astype(int)`.
*   **Result:** The years now appear as `2008`, `2009` etc., which makes the graphs much cleaner.

## 4. Descriptive Statistics

After cleaning, I moved to the Descriptive Statistics. This is where we look at the "shape" of the numbers. I focused on the sales columns (`NA_Sales`, `EU_Sales`, `JP_Sales`, `Other_Sales`, `Global_Sales`).

### 4.1 Measures of central tendency
I calculated the **mean**, **median** and **mode** (most frequent value).

*   **Global Sales mean:** ~0.54 million
*   **Global Sales median:** ~0.17 million

**Analysis:**
This comparison provided the first major insight of the report. The mean (0.54) is significantly higher than the median (0.17). In statistics, when mean>median, it indicates a **positive skew**.
*   **Interpretation:** This means the "average" video game is not actually that successful. The average is being pulled up by a small number of massive hits (outliers) like *Grand Theft Auto* or *Call of Duty*. The "median" is a better representation of a typical game's performance: most games sell less than 200000 copies.

### 4.2 Measures of dispersion
I calculated the **variance**, **standard deviation** and **interquartile range (IQR)** to understand the spread of the data.
*   **Standard Deviation (Global Sales):** 1.56
*   **Range:** the range is massive, stretching from 0.01 million to over 80 million (Wii Sports).

**Interpretation:**
the high standard deviation relative to the mean confirms that the video game market is highly unpredictable. It is a "success-dependent" business. There is no consistency; a game is either a failure, a moderate success or a top sellers.

### 4.3 Summary by genre
I grouped the data by `Genre` to see specific trends.
*   **Top performers:** platformers (like *Super Mario*) and Shooters (like *Halo*) have the highest average sales.
*   **Most common:** action games are the most frequently released genre, but their average sales are lower than shooters. This suggests the action genre is "saturated" there are too many games competing for the same players.

## 5. Data Visualization

I created five visualizations to make these statistics easier to understand.

### 5.1 Visualization 1: histogram of global sales
![vis1](vis1.png)
I plotted a histogram with a Kernel Density Estimate (KDE).
*   **Observation:** the distribution is heavily right-skewed, with most products clustered near zero sales and a long tail extending toward higher values. The frequency peaks dramatically at the lower end (around 50,000+ occurrences) and drops sharply as sales increase.
*   **Action:** the visualization focuses on sales from 0 to 4 million, which captures the bulk of the data. Without this range limitation, the extreme outliers would make the main pattern nearly impossible to see.
*   **Meaning:** this visually confirms the positive skew. It looks like a "Power Law" distribution. This distribution is common in creative industries (like music and books), where a "winner takes all" dynamic exists.

### 5.2 Visualization 2: bar chart of counts by genre
![vis2](vis2.png)
I used a bar chart (`sns.countplot`) to see how many games were made for each genre.
*   **Observation:** the Action bar is significantly taller than the rest. **Sports** is second.
*   **Meaning:** developers love making Action games. However, referencing back to my descriptive stats, since Action games don't have the *highest* average sales, this implies that making an Action game is risky because the competition is so strong competitive.

### 5.3 Visualization 3: boxplot of sales by genre
![vis3](vis3.png)
I used a boxplot to visualize the spread and outliers.
*   **Observation:** every single genre has black dots above the "whiskers."
*   **Meaning:** these dots are the outliers. Even in "niche" genres like Strategy, there are occasional breakout hits. However, the "box" (which represents the middle 50% of games) is higher for Platformers than for Adventure games, meaning Platformers are generally a "safer" investment.

### 5.4 Visualization 4: scatterplot. NA Sales versus EU Sales
![vis4](vis4.png)
I plotted NA sales against EU sales.
*   **Observation:** the dots form a cluster that moves upwards diagonally.
*   **Meaning:** there is a positive linear relationship. If a game is popular in the US, it is almost certainly popular in Europe. The tastes of these two markets are very similar.

### 5.5 Visualization 5: line сhart - total global sales over time
![vis5](vis5.png)
I grouped the sales by `Year` and plotted a line graph.
*   **Observation:** the line rises steadily in the 90s, peaks massively around **2008-2009**, and then appears to drop off.
*   **Context:** the peak corresponds to the "Wii/PS3/Xbox360" era, which was a golden age for physical retail. The drop-off after 2012 might look alarming, but it likely reflects a limitation in the dataset: the shift to digital downloads (Steam, PlayStation Store) which might not be fully captured in this older retail dataset.

## 6. Relationship Analysis

In this section, I used correlation matrices to put specific numbers on the relationships I saw in the graphs.

### 6.1 Regional Correlation Matrix
I generated a correlation matrix for the numerical columns. The results were fascinating:

| Relationship | Correlation Coefficient (r) | Interpretation |
| :--- | :--- | :--- |
| **NA vs EU** | **0.77** | **Strong positive.** these markets are twins. |
| **NA vs Global** | **0.94** | **Very strong.** NA is the primary driver of world success. |
| **NA vs JP (or EU vs Japan)** | **0.45** | **Moderate/weak.** this is the outlier. |

![heatmap](heatmap.png)

**My thoughts about Japan's anomaly:**

The correlation of 0.45 between North America and Japan or Europe and Japan is significantly lower than the others. This proves statistically that the Japanese market is unique. A game that tops the charts in New York might fail in Tokyo, and vice versa. For a global publisher, this means they need a different strategy for Japan compared to the rest of the world.

### 6.2 Regression analysis
![line](line.png)
I plotted a regression line for NA Sales versus Global Sales.
The regression line fits the data very tightly. The slope is steep, indicating that for every 1 million units sold in North America, the Global Sales usually increase by roughly 2 million (accounting for the rest of the world). This confirms that North America is the "main buyer" market if you win there, you win everywhere (except maybe Japan).

## 7. Probability Analysis

For the final section of my analysis, I applied probability concepts to the dataset. Specifically, I was interested in calculating the empirical probability of specific events occurring based on the historical data available.

### 7.1 Probability of a "Hit" Game
One of the most important questions in business is "What are the chances of success?"
I defined a successful "Hit" game as any title that has achieved **Global Sales greater than 1.0 million units**.

To find this probability, I used formula from school:
P(success) = count of success events / all events

**Code:**
*   I filtered the dataframe to find rows where `Global_Sales` > 1.0.
*   I divided this count by the total length of the dataframe.

**Results:**
*   **Total Games:** 16291
*   **Games > 1M:** 2031
*   **Calculated Probability:** **~0.125 (or ~12.5%)**

**Analysis:**

This statistic reveals reality about the gaming industry. Only about **1 in 8 games** ever breaks the 1 million sales mark. The majority of games (87.5%) fall below this threshold. This quantitative finding supports the qualitative idea that the industry is "high risk."

### 7.2 Probability of genre selection
I also wanted to determine the likelihood of a random game belonging to the most popular genre - **Action**.

I used the same formula of probabilty.

Results:
*   Calculated Probability: ~0.20 (or ~20%)

**Analysis:**

This means that if you pick a random game from history, there is a **20% chance** it is an Action game. This confirms that Action is the most saturated genre. When combined with the previous visualization showing that Platformers have higher *average* sales, we can infer that while Action games are more common (higher probability of existence), they might not necessarily have the highest probability of being a "Hit" compared to less saturated genres.

## 8. Conclusion

### 8.1 Summary of Findings

1.  **The "Superstar" economy:** the video game industry is defined by inequality. The mean sales are much higher than the median. A few winners take most of the revenue.
2.  **Regional divides:** the West (NA/EU) acts as a single block, while Japan is a distinct market with different behaviors.