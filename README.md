# 📊 The Analysis
## 🎯 The Most Demanded Skills for Remote Data Analyst Jobs

I put together this analysis to find out which technical skills are most requested for remote **Data Analyst** jobs. By looking at percentages rather than raw counts, it's easier to see the real probability of an employer asking for a specific skill.




```python
# 1. Filter for remote Data Analyst roles and find total job count
df_remote = df[(df['job_title_short'] == 'Data Analyst') & (df['job_work_from_home'] == True)].copy()
total_remote_jobs = len(df_remote)

# 2. Clean stringified lists and explode the skills into individual rows
df_remote['job_skills'] = df_remote['job_skills'].str.replace(r"[\[\]']", "", regex=True).str.split(", ")
df_skills_exploded = df_remote.explode('job_skills')

# 3. Calculate the percentage of appearance for the Top 10 skills
skill_percentages = (df_skills_exploded['job_skills'].value_counts() / total_remote_jobs) * 100
top_10_skills = skill_percentages.head(10)

# 4. Plot the results
top_10_skills.sort_values().plot(kind='barh', figsize=(10, 6), title='Top 10 Remote Data Analyst Skills (%)')
```
View my [notebook](1_skills_in_demand.ipynb) to see the full analysis, including data cleaning and methodology.
### Visualize Data
![Image](Images\skills_likelihood.png)


### 💡 Key Insights

- **SQL is non-negotiable** — appearing in 54.8% of remote data analyst postings, it's the only skill demanded by more than half of employers, making it nearly twice as common as the next skill (Excel at 34.5%).
- **The core stack is SQL + Excel + Python + Tableau** — these four skills cover the top 28%–55% demand range and represent the minimum viable toolkit for remote analyst roles; everything below Power BI (19.6%) drops off sharply.
- **Cloud and presentation tools are rare differentiators** — Azure, PowerPoint, and Looker are only requested in about 6% of job postings. However, checking this against salary data shows Azure is a bit of a hidden gem. It has lower demand but a high median salary ($105K), making it a smart niche skill to learn after mastering the core stack


## 📈 Top skills trends

Just looking at total skill counts doesn't tell the whole story. I wanted to track the **momentum** of these skills over the year.To get a clear picture, I converted the data into **percentages** instead of raw counts. This smoothed out seasonal hiring spikes and gives a better idea of how likely a skill is to be requested in any given month.




```python
# 1. Explode the skills list and pivot the data by month
df_exploded = df_DA.explode('job_skills')
df_pivot = df_exploded.pivot_table(index='job_posted_month', columns='job_skills', aggfunc='size')

# 2. Normalize into percentages based on total monthly job postings
monthly_totals = df_DA.groupby('job_posted_month').size()
df_pivot_pct = df_pivot.div(monthly_totals, axis=0) * 100

# 3. Extract the Top 5 skills and plot the trend
top_5 = df_pivot.sum().sort_values(ascending=False).head(5).index
df_pivot_pct[top_5].plot(kind='line', figsize=(10, 6), marker='o')
```
View my [notebook](2_skills_trend.ipynb) to see the full analysis, including data cleaning and methodology.
### Visualize Data
![Image](Images\skill_trend.png)


### 💡 Key Insights

- **SQL dominates data analyst job postings** (44–50%), maintaining a lead over Excel throughout the year making it the single most critical skill to have.
- **Excel and Python are neck-and-neck in mid-tier demand** (both 28–36%), but Python shows resilience with a sharp late-year recovery in December, signaling growing employer preference for programmatic analysis.
- **Tableau and Power BI remain baseline expectations** (20–25%), suggesting visualization tools are treated as complementary skills rather than primary hiring filters but their consistent presence means neither can be ignored.

## 💰 The Financial Value of Skills

### 🎯 Objective
While SQL and Excel are the most requested skills, are they the highest paying skills? The goal of this analysis was to identify which technical skills yield the highest median salaries for Data Analysts, isolating the tools that provide the best return on investment for learning.


```python
# The core logic to extract valid, high-paying skills
skill_stats = df_DA_exploded.groupby('job_skills').agg(
    median_salary=('salary_year_avg', 'median'),
    skill_count=('job_skills', 'count')
).reset_index()

# Statistical filtering to remove low-sample outliers
min_count = len(df_DA_salary) * 0.05 
top_paying_skills = skill_stats[skill_stats['skill_count'] >= min_count]
top_paying_skills = top_paying_skills.sort_values(by='median_salary', ascending=False).head(10)
```
View my [notebook](3_skills_salary.ipynb) to see the full analysis, including data cleaning and methodology.
## 📉 Visualize Data
This bar chart displays the premium the market places on specific competencies:

![Image](Images\highest_skills.png)
## Key Insights

- **Cloud platforms command the highest salaries** — **Snowflake** ($106,479), **Azure** ($105,000), and **AWS** ($104,000) occupy the top 3 spots, paying ~$10K+ more than traditional BI tools, signaling a clear market premium for cloud-native skills.
- **The $95K cluster reveals a competitive mid-tier** — **Python**, **Power BI**, **SQL Server**, and **Looker** all land at exactly $95,000, suggesting employers bundle these as interchangeable "standard analyst" skills with no differentiation in pay.
- **Even the lowest-ranked skill (Tableau at $92,500) pays six figures nearby** — the entire top 10 sits within a tight $14K band, meaning any one of these skills can anchor a strong remote analyst salary, but cloud tools are the ceiling-breakers.